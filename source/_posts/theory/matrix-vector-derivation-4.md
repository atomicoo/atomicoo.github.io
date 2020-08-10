---
title: 【数学天坑】之矩阵/向量求导（四）
date: 2020-08-09 00:33:42
updated: 2020-08-09 00:33:42
tags:
  - 机器学习
  - 数学
  - 矩阵求导
categories:
  - 一点理论
katex: true
---

## 引言

这是【数学天坑】系列之矩阵/向量求导的第四部分。

在上一篇文章的总结部分提到过一点，微分法虽然很大程度上避免了定义法的局限性，但在面对复杂链式求导的情况时仍然会很麻烦，因此还需要一种更优的方法，那就是：**链式求导法**。

链式求导法，对于在机器学习中可能遇到的绝大多数复杂链式求导，我们只需记忆一些常用的求导结果，然后再利用链式法则来求解即可。

<!-- more -->

## 链式法则

### 向量对向量

若有 $\bold{x} \in \R^{n}, \bold{y} \in \R^{m}, \bold{z} \in \R^{p}$，且三者间存在链式关系 $f: \bold{x} \to \bold{y} \to \bold{z}$，则有如下链式求导法则：

$$ \cfrac{\partial{\bold{z}}}{\partial{\bold{x}}}=\cfrac{\partial{\bold{z}}}{\partial{\bold{y}}} \cfrac{\partial{\bold{y}}}{\partial{\bold{x}}} \tag{1.1} $$

注意几个求导结果的维度，$\cfrac{\partial{\bold{z}}}{\partial{\bold{x}}} \in \R^{p \times n}, \cfrac{\partial{\bold{z}}}{\partial{\bold{y}}} \in \R^{p \times m}, \cfrac{\partial{\bold{y}}}{\partial{\bold{x}}} \in \R^{m \times n}$，从**维度相容**的角度可以快速求解。

同样的结论可以推广到更多的向量、更长的链式依赖关系 $f: \bold{x} \to \bold{y_{1}} \to \cdots \to \bold{y_{q}} \to \bold{z}$，记 $\bold{x} \triangleq \bold{y_{0}}, \bold{z} \triangleq \bold{y_{q+1}}$，则：

$$ \cfrac{\partial{\bold{z}}}{\partial{\bold{x}}}=\cfrac{\partial{\bold{y_{q+1}}}}{\partial{\bold{y_{0}}}}=\displaystyle\prod_{i=1}^{q+1}{\cfrac{\partial{\bold{y_{i}}}}{\partial{\bold{y_{i-1}}}}} \tag{1.2} $$

### 标量对向量

若有 $\bold{x} \in \R^{n}, \bold{y} \in \R^{m}, \bold{z} \in \R$，且三者间存在链式关系 $f: \bold{x} \to \bold{y} \to z$，则此时 $\cfrac{\partial{z}}{\partial{\bold{x}}} \in \R^{n}, \cfrac{\partial{z}}{\partial{\bold{y}}} \in \R^{m}, \cfrac{\partial{\bold{y}}}{\partial{\bold{x}}} \in \R^{m \times n}$，有：

$$ \cfrac{\partial{z}}{\partial{\bold{x}}}=((\cfrac{\partial{z}}{\partial{\bold{y}}})^{T} \cfrac{\partial{\bold{y}}}{\partial{\bold{x}}})^{T}=(\cfrac{\partial{\bold{y}}}{\partial{\bold{x}}})^{T} \cfrac{\partial{z}}{\partial{\bold{y}}} \tag{2.1} $$

同样的，该结论可以推广到更多的向量、更长的链式依赖关系 $f: \bold{x} \to \bold{y_{1}} \to \cdots \to \bold{y_{q}} \to z$，记 $\bold{x} \triangleq \bold{y_{0}}$，则：

$$ \cfrac{\partial{z}}{\partial{\bold{x}}}=\cfrac{\partial{z}}{\partial{\bold{y_{0}}}}=(\displaystyle\prod_{i=1}^{q}{\cfrac{\partial{\bold{y_{i}}}}{\partial{\bold{y_{i-1}}}}})^{T} \cfrac{\partial{z}}{\partial{\bold{y}}} \tag{2.2} $$

可以看到，在整个链式关系中，除了 $\bold{y_{q}} \to z$ 的部分，其他部分其实就跟上一种情况（向量对向量）是完全一样的。

### 标量对矩阵

矩阵对矩阵求导的情况是比较复杂的，因此在前几篇博客中并没有过多地关注，在本文我也并不打算过于深入的研究，但还是可以对机器学习中常见的矩阵对矩阵求导的类型做一些分析。

先回归定义法。若有 $X \in \R^{m \times n}, Y \in \R^{p \times q}, z \in \R$，且三者间存在链式关系 $f: X \to Y \to z$，则 $z$ 对 $X$ 中任一分量求导：

$$ \cfrac{\partial{z}}{\partial{x_{ij}}}=\displaystyle\sum_{k,l}{\cfrac{\partial{z}}{\partial{y_{kl}}} \cfrac{\partial{y_{kl}}}{\partial{x_{ij}}}}=tr((\cfrac{\partial{z}}{\partial{Y}})^{T} \cfrac{\partial{Y}}{\partial{x_{ij}}}) \tag{3} $$

机器学习中一个常见的问题：$z=f(Y), Y=A X+B$，求解 $\cfrac{\partial{z}}{\partial{X}}$。

考虑 $(3)$ 式中的 $\cfrac{\partial{y_{kl}}}{\partial{x_{ij}}}$：

$$ \cfrac{\partial{y_{kl}}}{\partial{x_{ij}}}=\cfrac{\partial{\displaystyle\sum_{s}{a_{ks} x_{sl}}}}{\partial{x_{ij}}}=\cfrac{\partial{a_{ki} x_{il}}}{\partial{x_{ij}}}=a_{ki} \delta_{lj} $$

其中，$\delta_{lj}=\begin{cases} 1 &\text{if } l = j \\\\ 0 &\text{if } l \neq j \end{cases}$

进而可得：

$$ \cfrac{\partial{z}}{\partial{x_{ij}}}=\displaystyle\sum_{k,l}{\cfrac{\partial{z}}{\partial{y_{kl}}} a_{ki} \delta_{lj}}=\displaystyle\sum_{k}{a_{ki} \cfrac{\partial{z}}{\partial{y_{kj}}}}=\langle A_{,i}, \cfrac{\partial{z}}{\partial{Y_{,j}}} \rangle $$

按照定义排列成矩阵形式：

$$ \cfrac{\partial{z}}{\partial{X}}=A^{T} \cfrac{\partial{z}}{\partial{Y}} $$

因此，对于这种常见类型，可以直接记忆其结论：

$$ z=f(Y), Y=A X+B \implies \cfrac{\partial{z}}{\partial{X}}=A^{T} \cfrac{\partial{z}}{\partial{Y}} $$

此外还有几个常见类型：

$$ z=f(\bold{y}), \bold{y}=A \bold{x}+\bold{b} \implies \cfrac{\partial{z}}{\partial{\bold{x}}}=A^{T} \cfrac{\partial{z}}{\partial{\bold{y}}} $$

$$ z=f(Y), Y=X A+B \implies \cfrac{\partial{z}}{\partial{X}}=\cfrac{\partial{z}}{\partial{Y}} A^{T} $$

$$ z=f(\bold{y}), \bold{y}=X \bold{a}+\bold{b} \implies \cfrac{\partial{z}}{\partial{X}}=\cfrac{\partial{z}}{\partial{\bold{y}}} \bold{a}^{T} $$

## 总结

至此，关于矩阵/向量求导已记录了四篇博客，搞定了矩阵/向量求导的三种方法：定义法、微分法和链式求导法。关于链式求导法，重点分析了向量对向量、标量对向量的类型，针对标量对矩阵的类型，考虑到矩阵对矩阵求导的复杂性，只分析了机器学习中常见的几种情况。在掌握这四篇博客内容的前提下，应付机器学习中多数的矩阵求导应该是没有问题的了。

## 参考资料

[机器学习中的矩阵向量求导(四) 矩阵向量求导链式法则](https://www.cnblogs.com/pinard/p/10825264.html)

[机器学习中的矩阵求导技术](https://zhuanlan.zhihu.com/p/46908990)

<!-- Q.E.D. -->