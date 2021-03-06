---
title: 【数学天坑】之矩阵/向量求导（一）
date: 2020-08-06 14:49:48
updated: 2020-08-06 14:49:48
tags:
  - 机器学习
  - 数学
  - 矩阵求导
categories:
  - 数学研究
  - 信息时代
katex: true
---

## 引言

【数学天坑】系列博客的开篇。本系列主要作为接下来一段时间自己恶补荒废多年数学的记录与分享。这么多年下来挖出的“天坑”显然不是一朝一夕能够填上的，只能送给自己八个字——**“戒骄戒躁、脚踏实地”**。

矩阵/向量求导在包括机器学习在内的许多领域都有着广泛的使用，但对于这部分知识感jio自己一直都处于一种比较懵逼的状态，所以有了这篇博客。因为鄙人是菜鸡程序猿一枚，并不需要像数学大佬那样钻研得非常深入，所以本文只会涉及比较浅层的部分，主要为机器学习相关的矩阵/向量求导，旨在快速掌握矩阵/向量求导法则。

本篇主要介绍矩阵**求导定义**及**求导布局**。

<!-- more -->

## 符号说明

为了后续方便以及统一书写规范，在此先对符号表示进行说明：

- 标量：普通小写字母或希腊字母，例如 $x$ 或 $\alpha$
- 向量：粗体小写字母，例如 $\bold{x}$，向量默认为**列向量**，且其每个分量记作 $x_{i}$
- 矩阵：普通大写字母，例如 $X$，$X_{i,}$ / $X_{,i}$ 分别表示第 $i$ 行/列，且其每个分量记作 $x_{ij}$
- 特殊说明的除外

## 导数定义

在高等数学中，通常都是标量对标量求导，例如标量 $y$ 对 标量 $x$ 求导记作 $\cfrac{\partial{y}}{\partial{x}}$，但有时会遇到一组标量 $y_{i}, i=1,2,\ldots,m$（记作向量 $\bold{y}$）对标量 $x$ 求导的情况，此时的求导结果 $\cfrac{\partial{y_{i}}}{\partial{x}},i=1,2,\ldots,m$ 可以排列成一个 $m$ 维向量：$\cfrac{\partial{\bold{y}}}{\partial{x}}$。

可见，向量对标量求导其实就是对向量的每个分量分别求导然后重新排列成一个向量。这一结论对后文提到的几种形式同样适用。由此，**矩阵求导本质上不过是多元函数求导**，只是将自变量/因变量/求导结果排列成矩阵形式，方便表达、计算及推导而已。

根据求导时因变量/自变量的形式不同，可以列出以下几种可能的求导定义：

| 自变量\因变量 |                  标量 y​                  |                     向量 y                      |                  矩阵 Y​                  |
| :-----------: | :--------------------------------------: | :---------------------------------------------: | :--------------------------------------: |
|  **标量 x​**   |    $\cfrac{\partial{y}}{\partial{x}}$     |    $\cfrac{\partial{\bold{y}}}{\partial{x}}$     |    $\cfrac{\partial{Y}}{\partial{x}}$     |
|  **向量 x**   | $\cfrac{\partial{y}}{\partial{\bold{x}}}$ | $\cfrac{\partial{\bold{y}}}{\partial{\bold{x}}}$ | $\cfrac{\partial{Y}}{\partial{\bold{x}}}$ |
|  **矩阵 X​**   |    $\cfrac{\partial{y}}{\partial{X}}$     |    $\cfrac{\partial{\bold{y}}}{\partial{X}}$     |    $\cfrac{\partial{Y}}{\partial{X}}$     |

需要注意的是，除了标量对标量求导以外，其他求导都会有一个排列方式的问题。譬如标量对列向量求导时，结果究竟是行向量还是列向量？

事实上对于单一求导过程来说，这两者都是可以的，只不过是定义的不同罢了，但是当你要使用向量/矩阵来进行一些推导时，必须要进行统一的规定（无论规定为哪种方式），否则会出现维度不匹配的问题。由此，引出了求导布局的概念。

## 求导布局

最基础的求导布局有两个：分子布局（numerator layout）和分母布局（denominator layout）。

举个栗子。

对于分子布局，求导结果的维度以分子为主。譬如：

- $m$ 维列向量 $\bold{y}$ 对标量 $x$ 求导，结果 $\cfrac{\partial{\bold{y}}}{\partial{x}}$ 为 $m$ 维列向量
- 标量 $y$ 对 $n$ 维列向量 $\bold{x}$ 求导，结果 $\cfrac{\partial{y}}{\partial{\bold{x}}}$ 为 $n$ 维行向量

对于分母布局，求导结果的维度以分母为主。譬如：

- $m$ 维列向量 $\bold{y}$ 对标量 $x$ 求导，结果 $\cfrac{\partial{\bold{y}}}{\partial{x}}$ 为 $m$ 维行向量
- 标量 $y$ 对 $n$ 维列向量 $\bold{x}$ 求导，结果 $\cfrac{\partial{y}}{\partial{\bold{x}}}$ 为 $n$ 维列向量

显然，两种布局的结果只相差一个转置。

> 再次强调，本文默认向量为列向量，表示行向量时将使用 $\bold{x}^{T}$

同理，很容易得出矩阵对标量或者标量对矩阵求导的结果。

由于在机器学习中几乎不太会遇到 *矩阵对向量/向量对矩阵/矩阵对矩阵* 这三种类型，所以略去不做讨论。

至此，就只剩下最后一种情况：向量对向量求导（只关注列向量对列向量求导）。

考虑到，向量 $\bold{y} \in \R^{m}$ 对向量 $\bold{x} \in \R^{n}$ 求导，实际上就是 $mn$ 个标量对标量求导，问题只在于求导结果应该如何排列。显然，排列成一个矩阵是合理的。那么在两种基础布局中，$\cfrac{\partial{\bold{y}}}{\partial{\bold{x}}}$ 是如何排列的呢？

为方便书写，记 $D_{ij} \triangleq \cfrac{\partial{y_{i}}}{\partial{x_{j}}}$。

对于分子布局，想象 $\bold{y}$ 不变而 $\bold{x}$ 转置的情景，得到结果：

$$\cfrac{\partial{\bold{y}}}{\partial{\bold{x}}}=\begin{pmatrix} D_{11} & D_{12} & \cdots & D_{1n} \\\\ D_{21} & D_{22} & \cdots & D_{2n} \\\\ \vdots & \vdots & \ddots & \vdots \\\\ D_{m1} & D_{m1} & \cdots & D_{mn} \end{pmatrix} $$

对于上述结果矩阵，通常称其为**雅克比（Jacobian）矩阵**，有些资料会使用 $\cfrac{\partial{\bold{y}}}{\partial{\bold{x^{T}}}}$ 来表示，记法不同但意义相同。

对于分母布局，想象 $\bold{y}$ 转置而 $\bold{x}$ 不变的情景，得到结果：

$$\cfrac{\partial{\bold{y}}}{\partial{\bold{x}}}=\begin{pmatrix} D_{11} & D_{21} & \cdots & D_{m1} \\\\ D_{12} & D_{22} & \cdots & D_{m2} \\\\ \vdots & \vdots & \ddots & \vdots \\\\ D_{1n} & D_{2n} & \cdots & D_{mn} \end{pmatrix} $$

对于上述结果矩阵，通常称其为梯度矩阵，有些资料会使用 $\cfrac{\partial{\bold{y^{T}}}}{\partial{\bold{x}}}$ 来表示，记法不同但意义相同。

综上所述，如果 $\bold{x} \in \R^{n}$，$\bold{y} \in \R^{m}$，$X \in \R^{m \times n}$，$Y \in \R^{m \times n}$，则有：

|    自变量\因变量    |                          标量 $y$                          |                      向量 $\bold{y}$                       |                          矩阵 $Y$                          |
| :-----------------: | :--------------------------------------------------------: | :--------------------------------------------------------: | :--------------------------------------------------------: |
|    **标量 $x$**     |                            $/$                             | 分子：$\R^{m \times 1}$（默认）<br>分母：$\R^{1 \times m}$ | 分子：$\R^{m \times n}$（默认）<br>分母：$\R^{n \times m}$ |
| **向量 $\bold{x}$** | 分子：$\R^{1 \times n}$<br>分母：$\R^{n \times 1}$（默认） | 分子：$\R^{m \times n}$（默认）<br>分母：$\R^{n \times m}$ |                            $/$                             |
|    **矩阵 $X$**     | 分子：$\R^{n \times m}$<br>分母：$\R^{m \times n}$（默认） |                            $/$                             |                            $/$                             |

注意到，以上表格展示的五种类型都有所谓的默认布局，其**总体原则**是：尽量保持与前面的向量/矩阵维度一致。向量对向量的求导布局比较有争议，个人习惯使用分子布局。

> 备注：考虑到在机器学习中，几乎很少遇到 *向量对标量/矩阵对标量* 的情况，因此后续的博客将重点关注其他三种，即 *标量对向量/标量对矩阵/向量对向量*。

## 参考资料

[机器学习中的矩阵向量求导(一) 求导定义与求导布局](https://www.cnblogs.com/pinard/p/10750718.html)

[矩阵求导中布局约定及其意义？- 知乎](https://www.zhihu.com/question/352174717)

[机器学习中的矩阵求导技术](https://zhuanlan.zhihu.com/p/46908990)

<!-- Q.E.D. -->