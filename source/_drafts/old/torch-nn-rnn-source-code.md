---
title: 【PyTorch 源码阅读】 torch.nn.RNN 篇
date: 2020-07-15 13:40:49
updated: 2020-07-15 13:40:49
tags:
  - PyTorch
  - 源码阅读
  - RNN
categories:
  - 一点技术
katex: true
---

## 引言

因为最近正好在回顾递归神经网络（Recurrent Neural Network, RNN）的一些内容，所以这篇手撸 PyTorch 源码的内容我们就来搞定 RNN 模块吧。本文为 `torch.nn.RNN` 篇，是【PyTorch 源码阅读系列】的第二篇。

<!-- more -->

## 源码阅读

> PyTorch 版本：1.5.1 - py3.7_cuda102_cudnn7_0

PyTorch 的 RNN 模块实现了以下三种 mode 的 RNN：

- `torch.nn.RNN`：[simple recurrent neural network (SRNN)](#)
- `torch.nn.LSTM`：[long short-term memory (LSTM) RNN](#)
- `torch.nn.GRU`：[gated recurrent unit (GRU) RNN]()

三种 RNN 都继承自 `torch.nn.RNNBase`（后者继承自 `torch.nn.Module`），只是根据定义的不同在构造方法 `__init__` 上有些许的区别。

---

`__init__`。

先来看看 `__init__` 的源码：

```python
def __init__(self, mode, input_size, hidden_size,
             num_layers=1, bias=True, batch_first=False,
             dropout=0., bidirectional=False):
    super(RNNBase, self).__init__()
    self.mode = mode
    # ...

    for layer in range(num_layers):
        for direction in range(num_directions):
            # ...
            for name, param in zip(param_names, layer_params):
                setattr(self, name, param)
            # ...

    self.flatten_parameters()
    self.reset_parameters()
```

先是初始化了一些内部参数（包括 RNN 的 类型 mode），然后针对 RNN 的每一层、每个方向（Bi-RNN）创建 `Parameters`，根据公式：

$$
h_t=g(W_{ih} x_{t}+b_{ih}+W_{hh} h_{t-1} +b_{hh})
$$
参数包括 `w_ih, w_hh, b_ih, b_hh`，需要注意的是 PyTorch 中 RNN 的激活函数只支持：`tanh` 和 `ReLU`，即上式中的 `g` 只能取这两种函数之一。

最后调用了 `flatten_parameters` 和 `reset_parameters` 来初始化所有参数，这两个函数的作用下面会谈到。

---

`flatten_parameters`。

`flatten_parameters` 重置所有参数的数据指针（*Resets parameter data pointer so that they can use faster code paths*）。

不明所以有木有？查看源码后可以发现它调用了 `torch._cudnn_rnn_flatten_weight`，对应的核代码的位置在 [_cudnn_rnn_flatten_weight](https://github.com/pytorch/pytorch/blob/fbd690c1fec0651ee9e6cc07ddcb12217ffb31bc/aten/src/ATen/native/cudnn/RNN.cpp#L622-L674)。

个人理解就是通过重置将所有的参数存放到一块连续的存储空间以提高效率。

PyTorch 在某些情况下会给出以下警告，明确建议我们使用连续的存储空间：

> UserWarning: RNN module weights are not part of single contiguous chunk of memory. This means they need to be compacted at every call, possibly greately increasing memory usage. To compact weights again call `flatten_parameters()`.

另外，此函数目前只有在模块位于 GPU 且 cuDNN 启动的情况下起作用（*this works only if the module is on the GPU and cuDNN is enabled*）。

---

`reset_parameters`。

`reset_parameters` 使用均匀分布随机初始化模块的所有参数，看源码：

```python
def reset_parameters(self):
    stdv = 1.0 / math.sqrt(self.hidden_size)
    for weight in self.parameters():
        init.uniform_(weight, -stdv, stdv)
```

---

To Be Continued.

<!-- Q.E.D. -->

## 参考资料

[PyTorch 官方文档](https://pytorch.org/docs/stable/nn.html#recurrent-layers)

[What does flatten_parameters() do?](https://stackoverflow.com/questions/53231571/what-does-flatten-parameters-do)