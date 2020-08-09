---
title: 【PyTorch 源码阅读】 torch.nn.Module 篇
date: 2020-07-04 16:52:45
updated: 2020-07-06 16:59:00
tags:
  - PyTorch
  - 源码阅读
  - Module
categories:
  - 一点技术
---

## 引言

【PyTorch 源码阅读系列】主要是记录一些阅读 PyTorch 源码时的笔记（**好记性不如烂笔头**）。事实上 PyTorch 的文档齐全，哪怕你不阅读源码也能够很好地使用它来搭建并训练自己的模型，我之所以选择阅读源码，一方面是为了对 PyTorch 有更深入的理解，另一方面是学习这种优秀的源码也能够帮助自己写出更优雅规范的代码。本文为 `torch.nn.Module` 篇，本系列的第一篇。

<!-- more -->

## 源码阅读

> PyTorch 版本：1.5.1 - py3.7_cuda102_cudnn7_0

`torch.nn.Module` 是 PyTorch 所有神经网络模块的基类（官方文档：*Base class for all neural network modules*），无论是官方实现还是自己创建的的网络模块都应该是它的子类。

一个简单的使用示例：

```python
class SimpleNet(nn.Module):       # 定义神经网络
    
    def __init__(self):
        super(SimpleNet, self).__init__()
        # Convolution kennel
        self.conv1 = nn.Conv2d(1, 6, 3)
        self.conv2 = nn.Conv2d(6, 16, 3)
        # an affine operation: y = Wx + b
        self.fc = nn.Linear(16*6*6, 10)
    
    def forward(self, x):
        # Max pooling over (2, 2) window
        x = F.max_pool2d(F.relu(self.conv1(x)), 2)
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)
        x = x.view(-1, 16*6*6)
        x = self.fc(x)
        return x
```

---

`torch.nn.Module` 类中共包含 49 个函数（指定版本 1.5.1），下面一一进行分析。

---

`__init__` 和 `forward`。

`__init__` 函数主要是初始化模块内部状态（*internal Module state*）。`forward` 函数需要在子类中实现，如果子类中没有实现会引发 `NotImplementedError`。

---

`register_buffer`、`register_parameter` 和 `add_module`。

`register_buffer` 添加 `name: buffer` 到模块的 `self._buffers` 字典中，这里 `buffer` 指的是一些非模型参数的持久状态（*the persistent state*），譬如 `BatchNorm` 的 `running_mean` 等。

`register_parameter` 添加 `name: parameter` 到模块的 `self._parameters` 字典中。

`add_module` 根据 `name: module` 添加子模块到模块的 `self._modules` 字典中。

```python
# register_buffer
self._buffers[name] = tensor

# register_parameter
self._parameters[name] = param

# add_module
self._modules[name] = module
```

---

`_apply` 和 `apply`。

`_apply` 的作用是对模块中的所有 `tensor`（包括 `parameters` 和 `buffers` ）进行一遍传入的 `fn` 操作。通过 `_apply` 我们可以方便地对模块中的 `tensor` 做很多操作，譬如下面会讲到的 `cuda` 和 `cpu`。

以下是简化源码：第一个循环就是递归地对所有的子模块进行一遍操作；`compute_should_use_set_data` 决定是否采用 `in-place` 的方式（*change the tensor in-place*），即就地修改 `tensor`；第二个循环对所有的 `parameters` 进行 `fn` 操作，如果 `param` 有 `.grad` 的话需要对其也进行 `fn` 操作；第三个循环对所有的 `buffers` 进行 `fn` 操作。

```python
def _apply(self, fn):
	for module in self.children():
		module._apply(fn)

	def compute_should_use_set_data(tensor, tensor_applied):
		# ...

	for key, param in self._parameters.items():
		if param is not None:
            # ...
			compute_should_use_set_data()
            # ...
			if param.grad is not None:
				# ...

	for key, buf in self._buffers.items():
		# ...

	return self
```

`apply` 与 `_apply` 有所不同，它的作用是递归地对所有子模块用传入的 `fn` 操作一遍（*Applies `fn` recursively to every submodule*）。这里的子模块指的是 `.children()` 列出的内容。

```python
def apply(self, fn):
    for module in self.children():
        module.apply(fn)
    fn(self)
    return self
```

譬如可以利用 `apply` 对模型权重（*weight*）进行初始化。源码中给出的示例：

```sh
>>> @torch.no_grad()
>>> def init_weights(m):
>>>     print(m)
>>>     if type(m) == nn.Linear:
>>>         m.weight.fill_(1.0)
>>>         print(m.weight)
>>> net = nn.Sequential(nn.Linear(2, 2), nn.Linear(2, 2))
>>> net.apply(init_weights)
```

---

`cuda`、`cpu` 和 `share_memory`。

将模块所有的 `tensor` 移入指定位置（GPU/CPU/共享内存）中。通过源码可以看到，这三者都调用了前面讲到的 `_apply` 函数。

```python
# cuda
def cuda(self, device=None):
    return self._apply(lambda t: t.cuda(device))

# cpu
def cpu(self):
    return self._apply(lambda t: t.cpu())

# shared memory
def share_memory(self):
    return self._apply(lambda t: t.share_memory_())
```

---

`type`、`float`、`double`、`half` 和 `bfloat16`。

这几个函数都是将模块中所有的 `tensor` 转成指定的类型。

```python
def type(self, dst_type):
    return self._apply(lambda t: t.type(dst_type))

def float(self):
    return self._apply(lambda t: t.float() if t.is_floating_point() else t)

def double(self):
    return self._apply(lambda t: t.double() if t.is_floating_point() else t)

def half(self):
    return self._apply(lambda t: t.half() if t.is_floating_point() else t)

def bfloat16(self):
    return self._apply(lambda t: t.bfloat16() if t.is_floating_point() else t)
```

---

`to`。

`to` 可以用来对模块的所有 `tensor` 进行设备和/或类型的 `in-place` 方式的操作（*Moves and/or casts the parameters and buffers*）。源码中给出的示例：

```sh
>>> linear = nn.Linear(2, 2)
>>> linear.weight
>>> linear.to(torch.double)
>>> linear.weight
>>> gpu1 = torch.device("cuda:1")
>>> linear.to(gpu1, dtype=torch.half, non_blocking=True)
>>> linear.weight
>>> cpu = torch.device("cpu")
>>> linear.to(cpu)
>>> linear.weight
```

大家可以实际运行下看看结果。

---

`register_backward_hook`、`register_forward_pre_hook` 和 `register_forward_hook`。

这三个函数分别在模块中注册 `forward_pre_hook`、`forward_hook` 和 `backward_hook`。关于 `Hooks` （钩子？挂钩？咋翻好听？）的作用我会在后面专门开一篇文章说明。

```python
# backward_hook
hook(module, grad_input, grad_output) -> Tensor or None

# forward_pre_hook
hook(module, input) -> None or modified input

# forward_hook
hook(module, output) -> None or modified output
```

---

`_slow_forward` 和 `__call__`。

`__call__` 是模块计算的真正入口，内部会调用 `_slow_forward` 函数或者 `forward` 函数进行计算，事实上 `_slow_forward` 内部最终也是调用 `forward` 函数进行计算，两者的差别在于有些自定义操作是没有 C 代码的，这种情况就会直接调用 Python 版本，反之调用 C 版本（效率高）。简化源码如下：

```python
def __call__(self, *input, **kwargs):
    for hook in self._forward_pre_hooks.values():
        # ...
    if torch._C._get_tracing_state():
        result = self._slow_forward(*input, **kwargs)
    else:
        result = self.forward(*input, **kwargs)
    for hook in self._forward_hooks.values():
        # ...
    for hook in self._backward_hooks.values():
        # ...
    return result
```

---

`__setstate__`。

`__setstate__` 设置 `state`，这个比较简单，直接看源码。

```python
def __setstate__(self, state):
    self.__dict__.update(state)
    # Support loading old checkpoints that don't have the following attrs:
    if '_forward_pre_hooks' not in self.__dict__:
        self._forward_pre_hooks = OrderedDict()
    if '_state_dict_hooks' not in self.__dict__:
        self._state_dict_hooks = OrderedDict()
    if '_load_state_dict_pre_hooks' not in self.__dict__:
        self._load_state_dict_pre_hooks = OrderedDict()
```

---

`__getattr__`、`__setattr__` 和 `__delattr__`。

`__getattr__` 用于获取指定 `name` 的模块成员（包括 `parameters`、`buffers` 和 `modules`，查找顺序从前到后）。

`__getattr__` 用于查找指定 `name` 的模块成员后对其进行设置（同上）。

`__delattr__` 用于删除指定 `name` 的模块成员（同上）。

这三个函数都比较简单，不多做说明。

---

`_register_state_dict_hook` 和 `_register_load_state_dict_pre_hook`。

这三个函数与前面提到的 `register_*_hook` 类似，用于注册 `Hooks`。

`_register_state_dict_hook` ：

*These hooks will be called with arguments: `self`, `state_dict`, `prefix`, `local_metadata`, after the `state_dict` of `self` is set.*

`_register_load_state_dict_pre_hook`：

*These hooks will be called with arguments: `state_dict`, `prefix`, `local_metadata`, `strict`, `missing_keys`, `unexpected_keys`, `error_msgs`, before loading `state_dict` into `self`.*

---

`_save_to_state_dict` 和 `_load_from_state_dict`。

`_save_to_state_dict` 作用是保存 `state` 到 `destination` 指定的字典中，此函数会被当前模块的所有子模块调用（*This is called on every submodule in :meth:`~torch.nn.Module.state_dict`*）。

`_load_from_state_dict` 作用与 `_save_to_state_dict` 相反，用来加载模块。

---

`state_dict` 和 `load_state_dict`。

`state_dict` 的作用是返回一个包含模块完整 `state` 的字典（*Returns a dictionary containing a whole state of the module*），字典中的键值对对应着 `name: parameters` 或 `name: buffers`。此函数会调用上面的 `_save_to_state_dict` 函数。

`load_state_dict` 的作用与 `state_dict` 相反，是将  `name: parameters` 或 `name: buffers` 加载到模块及其子模块中去。此函数会调用上面的 `_load_from_state_dict` 函数。

---

`named_parameters`、`named_buffers`、`named_children` 和 `named_modules`。

这四个函数的作用是返回 `name: menbers` 的生成器（*generator*），其中 `members` 可以是 `parameters`、`buffers`、`children` 或 `modules`。

```python
def _named_members(self, get_members_fn, prefix='', recurse=True):
    r"""Helper method for yielding various names + members of modules."""
    memo = set()
    modules = self.named_modules(prefix=prefix) if recurse else [(prefix, self)]
    for module_prefix, module in modules:
        members = get_members_fn(module)
        for k, v in members:
            if v is None or v in memo:
                continue
                memo.add(v)
                name = module_prefix + ('.' if module_prefix else '') + k
                yield name, v

def named_parameters(self, prefix='', recurse=True):
    gen = self._named_members(
        lambda module: module._parameters.items(),
        prefix=prefix, recurse=recurse)
    for elem in gen:
        yield elem

def named_buffers(self, prefix='', recurse=True):
    gen = self._named_members(
        lambda module: module._buffers.items(),
        prefix=prefix, recurse=recurse)
    for elem in gen:
        yield elem

def named_children(self, prefix='', recurse=True):
    memo = set()
    for name, module in self._modules.items():
        if module is not None and module not in memo:
            memo.add(module)
            yield name, module

def named_modules(self, prefix='', recurse=True):
    if memo is None:
        memo = set()
    if self not in memo:
        memo.add(self)
        yield prefix, self
        for name, module in self._modules.items():
            if module is None:
                continue
            submodule_prefix = prefix + ('.' if prefix else '') + name
            for m in module.named_modules(memo, submodule_prefix):
                yield m
```

注意：`named_children` 非递归，只返回下一级的子模块（*immediate children modules*）；`named_modules` 不返回重复的模块（*Duplicate modules are returned only once*）。

此外还有相应的 `parameters`、`buffers`、`children` 和 `modules`，只会返回 `members` 的生成器。通过源码可以看到，这四个函数调用了上面的四个函数。

```python
def parameters(self, recurse=True):
    for name, param in self.named_parameters(recurse=recurse):
        yield param

def buffers(self, recurse=True):
    for name, buf in self.named_buffers(recurse=recurse):
        yield buf

def children(self):
    for name, module in self.named_children():
        yield module

def modules(self):
    for name, module in self.named_modules():
        yield module
```

---

`train` 和 `eval`。

这两个函数的作用是将模块及其子模块设置成训练/评估模式（*training/evaluation mode*）。这只对特定的模块起作用，譬如 `Dropout` 和 `BatchNorm`。

```python
def train(self, mode=True): 
    self.training = mode
    for module in self.children():
        module.train(mode)
    return self

def eval(self):
    return self.train(False)
```

---

`requires_grad_` 和 `zero_grad`。

`requires_grad_` 用于设置模块中的 `parameters` 是否需要追踪梯度，操作层面来说即 `param` 的 `.requires_grad` 是否为 `True`。

`zero_grad` 用于将模块中 `parameters` 的梯度清零。

---

`_get_name`、`extra_repr`、`__repr__` 和 `__dir__`。

这四个函数都是用于输出模块相关信息的，并不复杂，直接看源码即可。

---

想要进一步了解的点：

- buffers
- in-place
- hooks

## 参考资料

[PyTorch 官方文档](https://pytorch.org/docs/stable/nn.html#module)

<!-- Q.E.D. -->