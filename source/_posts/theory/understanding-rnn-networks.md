---
title: 【搞定神经网络】循环神经网络篇
date: 2020-07-14 16:15:48
updated: 2020-07-14 16:15:48
tags:
  - 神经网络
  - RNN
  - LSTM
  - GRU
categories:
  - 一点理论
katex: true
---

## 引言

【搞定神经网络系列】博客开坑第一篇，循环神经网络篇。

循环神经网络（Recurrent Neural Network, RNN）是一类人工神经网络，通常以序列（sequence）数据为输入，在序列的演进方向进行递归（recursion）且所有节点（循环单元）按链式连接。需要注意的是循环神经网络具有记忆性且参数共享。

<!-- more -->

## 神经网络基础

要了解递归神经网络，首先就得了解神经网络。

那么，什么是神经网络呢？你可以将神经网络简单地看成是一个**黑盒子**，里面装了一大堆的可训练参数，参数与参数的组织形式共同构成了所谓的“**模型**”，这个模型建模了具有实际意义的一项或几项任务，当你手头拥有与任务相对应的数据时，你就可以使用你的数据训练这些参数来拟合真实的模型（上帝的模型？除了上帝，没人知道真实的模型是啥样的，只能通过已有的数据进行拟合）。

> 当然，要训练一个神经网络，除了模型本身之外，还需要有**优化器**和**损失函数**（这就是另外的话题了）。

既然说参数与参数的组织形式共同构成了模型，那么我们自然可以通过改变参数及其组织形式来构造出各种不同的（神经网络）模型，本博客介绍的就是其中的循环神经网络。

## 循环神经网络

考虑平时阅读文章时的情景，显然你并不是将文章的每个字/词当成一个个完全独立的单元来进行阅读的，你在阅读每个字/词时都会考虑前文内容（甚至预想后文内容），也就是说，在处理类似于文本/语音这类序列数据时，我们的模型需要具备获取上下文信息的能力，传统的神经网络结构显然对此无能为力，然鹅，循环神经网络解决了这一问题。

循环神经网络，顾名思义，具有循环结构的神经网络，它（理论上）可以持续保存上下文信息，其结构如下图：

![RNN.png](https://i.loli.net/2020/07/23/GZcjyeQiAWLsNaX.png)

看起来貌似有那么一丢丢的复杂，那么我们将循环展开来看看：

![RNN-.png](https://i.loli.net/2020/07/23/3AdiHYOaXCqTzw8.png)

Emmm，结构清晰多了，可以看到前文信息确实是可以随着循环一层一层地往后传递的。

> 需要注意的是，在单向 RNN 中，模型只能向后传递前文信息，若要同时考虑上下文的信息则应使用双向 RNN，关于双向 RNN 的内容后面也会提到。

事实上这个结构应该叫做简单循环网络（simple recurrent network，SRN），又称为 Elman Network，是由 Jeff Elman 在 1990 年提出的，在 Jordan Network（1986）的基础上进行了改进并简化了结构。关于两者的区别这里不做赘述。

Notes：前文提到，循环神经网络通常用于处理序列数据，因此，在进一步介绍循环神经网络之前，我想在这儿丢张图，列举出序列数据处理任务可能的几种形式，具体就不多 BB 了，很容易看懂。

![seq2seq.png](https://i.loli.net/2020/08/07/FPe4EL9rbmwizVG.png)

## 长短时记忆网络

上一节已经介绍了循环神经网络的基本结构，但是循环结构中的重复单元（可以类比于编程中的循环体）是啥呢？

在具体说明之前，我们需要提前定义几个操作的表示方式：

![operation.png](https://i.loli.net/2020/08/07/GIm9ilRwsjEaOgS.png)

定义操作完毕，我们圆规正转（~~谐音梗扣钱~~），继续来讨论重复单元的问题。显而易见，根据重复单元的不同，可以定义出完全不同类型的循环神经网络。

事实上，标准 RNN 中的重复单元具有非常简单的结构，就是一个单层的神经网络。

![rnn.png](https://i.loli.net/2020/08/07/8YSrXD74nuL2iVT.png)

具体公式如下：

$$ h_{t}=g(W \cdot [x_{t}, h_{t-1}]+b) $$

其中，$g(\cdot)$ 为激活函数，通常选择 $sigmoid(\cdot)$ 或 $tanh(\cdot)$。

很遗憾的一点是，尽管 RNN 的结构在理论上具备获取足够的上下文信息的能力，但在实践中似乎不是这样的，标准 RNN 在实际表现中并不尽如人意，尤其是随着序列的上下文信息的跨度变大，标准 RNN  开始无法学习相应的信息。

幸运的是，后续衍生出的 LSTM 较好地解决了这个问题。

![lstm.png](https://i.loli.net/2020/08/07/ndczrI1UkKu9BoA.png)

LSTM 的关键点在于 Cell State，也就是如下图所示，贯穿了整个“循环”过程的 $C$。

![cell.png](https://i.loli.net/2020/08/07/CtQHRNah2bYGIVk.png)

这其实有点像是传送带，cell state 会沿着这条传送带逐层往后传送，在传送过程中不断有旧的状态被删除、新的状态被添加，而对状态信息的增删进行控制的部分就是其中门的机制。具体的（控制）操作方式就是逐元素乘法（pointwise multiplication operation）。

![gated.png](https://i.loli.net/2020/08/07/9cHTfi1PgjZAsJG.png)

## LSTM 逐步分解

遗忘门（forget gate）控制有哪些旧的信息会被删除。

![lstm-forget.png](https://i.loli.net/2020/08/07/Ofw8mvycq9pFSH7.png)

输入门（input gate）控制有哪些新的信息会被添加。

![lstm-input.png](https://i.loli.net/2020/08/07/wvOMDegrj1idyNG.png)

旧的状态信息经过遗忘门和输入门对信息的增删之后，得到新的状态信息（cell state）。

![lstm-cell.png](https://i.loli.net/2020/08/07/TvKPbklNqOdjw9L.png)

输出门（output gate）控制最终有哪些信息会被输出。

![lstm-output.png](https://i.loli.net/2020/08/07/ecyrjV6iN2OvpgH.png)

## LSTM 的变体

窥孔连接：

![lstm-var1.png](https://i.loli.net/2020/08/07/Y8yXdcvHnqGmbgN.png)

耦合遗忘/输入门：

![lstm-var2.png](https://i.loli.net/2020/08/07/28VcwqbzKu5lE9L.png)

GRU（Gated Recurrent Unit）将遗忘/输入门合并为更新门（update gate），并合并了 cell state 与 hidden state。

![gru.png](https://i.loli.net/2020/08/07/35ldwRJIc6sEoQm.png)

需要注意的是，以上这几种类型（包括标准的 LSTM）各擅胜场，并没有明确的优劣势，在具体的任务中选择哪种类型通常需要通过实验来确定。

## 多层 RNN 结构

（空）

## 双向 RNN 结构

（空）

---

To Be Continued.

## 参考资料

[Understanding LSTM Networks](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)

[long short-term memory (LSTM)](https://www.researchgate.net/publication/13853244_Long_Short-term_Memory)

[gated recurrent units (GRU)](https://arxiv.org/abs/1409.1259)

<!-- Q.E.D. -->