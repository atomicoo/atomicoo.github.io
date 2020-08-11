---
title: 【数学天坑】之反向传播算法数学原理
date: 2020-08-10 17:46:13
updated: 2020-08-10 17:46:13
tags:
  - 神经网络
  - 数学
  - BP算法
categories:
  - 一点理论
katex: true
---

## 引言

神经网络主要由三个部分组成：1) 网络模型；2) 损失函数；3) 参数学习算法。

我们今天的主角，**反向传播算法**（Back Propagation Algorithm，BP Algorithm），可以说是目前使用最为广泛的参数学习算法。其基本思想是：学习过程由信号的正向传播（求损失）与误差的反向传播（误差回传）两个过程组成。

<!-- more -->

## 抛砖引玉

先用一个例子来抛砖引玉。

已知：$J=(X \bold{\omega}-\bold{y})^{T} (X \bold{\omega}-\bold{y})=\|X \bold{\omega}-\bold{y}\|^{2}$，其中 $X \in \R^{m \times n}, \bold{\omega} \in \R^{n}, \bold{y} \in \R^{m}$。

求 $\cfrac{\partial{J}}{\partial{X}}, \cfrac{\partial{J}}{\partial{\bold{\omega}}}, \cfrac{\partial{J}}{\partial{\bold{y}}}$？

熟悉的老铁们可能已经发现了，这其实就是线性回归的矩阵表示形式。在实践时，我们通常只会对 $\bold{\omega}$ 求导来进行最小二乘估计，但在这里因为本例只是作为一个引子，所以会对 $X, \bold{\omega}, \bold{y}$ 都进行求导，并无实际意义。

反向传播算法的关键在于“**链式求导法则**”：若函数 $f, g$ 可导，则 $(f \circ g)^{'}(x)=f^{'}(g(x))g^{'}(x)$。

## 快速矩阵求导

这一节将介绍如何从“维度相容”的角度进行快速矩阵求导，其核心在于**维度相容原则**。那么，什么是维度相容原则？

**维度相容原则**：通过**换序**、**转置**操作使求导结果满足维度条件。

> 关于矩阵求导结果需要满足的维度条件，涉及到矩阵求导布局的问题，具体可以参考我之前的博客 [【数学天坑】之矩阵/向量求导（一）](https://atomicoo.com/theory/matrix-vector-derivation-1/)。
> 另外，维度相容实际上是多元微分中的知识，我在之前的博客中也说过，**矩阵求导本质就是多元函数求导**，这也就很容易理解为什么可以把维度相容“移植”过来使用了，毕竟知识都是相通的。

从维度相容角度进行快速矩阵求导说起来就四个字“简单粗暴”，不够严谨但足够好用。套用一下“爆炸即艺术”的句式，那就是——**简单即艺术**！

快速矩阵求导共有两个步骤，我们用上一节的例子来进行说明。

步骤一：把所有参数看成是标量来进行求导。

显然，这一步哪怕是中学生也能轻松完成，$J=(X \bold{\omega}-\bold{y})^{2}$，由链式法则，按照标量方式求导的结果：

$$ \begin{aligned} \cfrac{\partial{J}}{\partial{X}}=2 (X \bold{\omega}-\bold{y}) \bold{\omega} \\\\ \cfrac{\partial{J}}{\partial{\bold{\omega}}}=2 (X \bold{\omega}-\bold{y}) X \\\\ \cfrac{\partial{J}}{\partial{\bold{y}}}=-2 (X \bold{\omega}-\bold{y}) \end{aligned} $$

检查求导结果的维度，除了 $\cfrac{\partial{J}}{\partial{\bold{y}}}$ 之外，$\cfrac{\partial{J}}{\partial{X}}$ 和 $\cfrac{\partial{J}}{\partial{\bold{\omega}}}$ 都是不满足维度条件的。

步骤二：利用换序、转置操作调整维度满足条件。

考虑矩阵求导维度，有：

$$ \cfrac{\partial{J}}{\partial{X}} \in \R^{m \times n}, \cfrac{\partial{J}}{\partial{\bold{\omega}}} \in \R^{n} $$

根据维度相容原则：

$$ \cfrac{\partial{J}}{\partial{X}} \in \R^{m \times n}, (X \bold{\omega}-\bold{y}) \in \R^{m}, \bold{\omega} \in \R^{n} $$

通过换序、转置操作可得：

$$ \cfrac{\partial{J}}{\partial{X}}=2 (X \bold{\omega}-\bold{y}) \bold{\omega}^{T} $$

满足维度条件。同理可得：

$$ \cfrac{\partial{J}}{\partial{\bold{\omega}}}=2 X^{T} (X \bold{\omega}-\bold{y}) $$

简单总结下维度相容快速矩阵求导：

- 步骤一：把所有参数看成是标量来进行求导
- 步骤二：利用换序、转置操作调整维度满足条件

## 快速反向传播

前面已经提到过，反向传播算法的关键在于“**链式求导法则**”，在反向时需要一层一层地往前进行链式求导。

第 $l$ 层的前向传播过程：

$$ \begin{aligned} \bold{z}^{(l+1)}&=W^{(l)} \bold{a}^{(l)}+\bold{b}^{(l)} \\\\ \bold{a}^{(l+1)}&=f(\bold{z}^{(l+1)}) \end{aligned} $$

其中，$\bold{a}^{(l)}$ 为第 $l$ 层的输入，$W^{(l)}, \bold{b}^{(l)}$ 为第 $l$ 层的参数，$\bold{z}^{(l+1)}$ 为第 $l$ 层的中间结果，$f(\cdot)$ 为激活函数，$\bold{a}^{(l+1)}$ 为第 $l$ 层的激活值（也是第 $l$ 层的输出）。

设损失函数为 $J(W, \bold{b}) \in \R$（这里不做具体定义，$J$ 可以为任意损失函数），由链式法则：

$$ \begin{aligned} \cfrac{\partial{J}}{\partial{W^{(l)}}}&=\cfrac{\partial{J}}{\partial{\bold{z}^{(l+1)}}} \cfrac{\partial{\bold{z}^{(l+1)}}}{\partial{W^{(l)}}}=\bold{\delta}^{(l+1)} (\bold{a}^{(l)})^{T} \\\\ \cfrac{\partial{J}}{\partial{\bold{b}^{(l)}}}&=\cfrac{\partial{J}}{\partial{\bold{z}^{(l+1)}}} \cfrac{\partial{\bold{z}^{(l+1)}}}{\partial{\bold{b}^{(l)}}}=\bold{\delta}^{(l+1)} \end{aligned} $$

为方便书写，记 $\bold{\delta}^{(l)} \triangleq \cfrac{\partial{J}}{\partial{\bold{z}^{(l)}}}$。上式中，考虑矩阵求导维度条件：

$$\cfrac{\partial{J}}{\partial{W^{(l)}}} \in \R^{s^{(l+1)} \times s^{(l)}}, \bold{\delta}^{(l+1)}=\cfrac{\partial{J}}{\partial{\bold{z}^{(l+1)}}} \in \R^{s^{(l+1)}}$$

$s^{(l+1)}$ 为第 $l$ 层中间结果与激活值的维度，即 $\bold{a}^{(l+1)}, \bold{z}^{(l+1)} \in \R^{s^{(l+1)}}$。

所以根据**维度相容原则**增加转置符号调整结果为：

$$ \cfrac{\partial{\bold{z}^{(l+1)}}}{\partial{W^{(l)}}}=(\bold{a}^{(l)})^{T} \in \R^{s^{(l)}} $$

接下来就只需要搞定 $\bold{\delta}^{(l)}$ 即可。那么应该如何求解 $\bold{\delta}^{(l)}$？

因为在前向传播过程中，链式依赖关系为 $\cdots \to \bold{z}^{(l)} \to \bold{a}^{(l)} \to \bold{z}^{(l+1)} \to \bold{a}^{(l+1)} \to \cdots$，所以可以用递推的方式依次求解：

$$ \bold{\delta}^{(l)}=\cfrac{\partial{J}}{\partial{\bold{z}^{(l)}}}=\cfrac{\partial{J}}{\partial{\bold{z}^{(l+1)}}} \cfrac{\partial{\bold{z}^{(l+1)}}}{\partial{\bold{a}^{(l)}}} \cfrac{\partial{\bold{a}^{(l)}}}{\partial{\bold{z}^{(l)}}} $$

其中，

$$ \begin{aligned} \cfrac{\partial{J}}{\partial{\bold{z}^{(l+1)}}}&=\bold{\delta}^{(l+1)} \in \R^{s^{(l+1)}} \\\\ \cfrac{\partial{\bold{z}^{(l+1)}}}{\partial{\bold{a}^{(l)}}}&=W^{(l)} \in \R^{s^{(l+1)} \times s^{(l)}} \\\\ \cfrac{\partial{\bold{a}^{(l)}}}{\partial{\bold{z}^{(l)}}}&=f^{'}(\bold{z}^{(l)}) \in \R^{s^{(l)}} \end{aligned}$$

根据**维度相容原则**，调整结果得：

$$ \bold{\delta}^{(l)}=((W^{(l)})^{T} \bold{\delta}^{(l+1)}) \odot f^{'}(\bold{z}^{(l)}) $$

注意，$\bold{a}^{(l)}=f(\bold{z}^{(l)})$ 是逐元素运算，因此 $\cfrac{\partial{\bold{a}^{(l)}}}{\partial{\bold{z}^{(l)}}}=f^{'}(\bold{z}^{(l)})$ 也是逐元素求导的形式，在公式中为**逐元素乘**。

至此，反向传播算法的推导全部完成，整理整个反向传播过程如下：

- 利用前向传播公式进行前向传播计算，得到各层的激活值 $\bold{a}^{(l)}$
- 对于输出层（第 $L$ 层），计算 $\bold{\delta}{(L)}$
- 对于隐藏层，利用递推公式依次计算 $\bold{\delta}{(l)}$
- 计算各层参数 $W^{(l)}, \bold{b}^{(l)}$ 的偏导

## 总结

从**维度相容**的角度进行快速矩阵求导遵循“**简单即艺术**”的信条，在数学证明上显然并不严谨，但对于机器学习相关领域的学习者来说已经足以应付大多数情况。当然，对于想要更进一步的人来说，还是需要脚踏实地地去查阅各种相关资料，将矩阵求导方面的知识完全吃透。而搞定了矩阵求导，包括**反向传播**在内的许多算法的推导也将迎刃而解。

## 参考资料

[神经网络反向传播的数学原理](https://zhuanlan.zhihu.com/p/22473137)

[反向传播算法推导过程（非常详细）](https://zhuanlan.zhihu.com/p/79657669)

[前向传播算法和反向传播算法（BP算法）及其推导](https://zhuanlan.zhihu.com/p/71892752)

[反向传播算法推导-全连接神经网络](https://zhuanlan.zhihu.com/p/39195266)

<!-- Q.E.D. -->
