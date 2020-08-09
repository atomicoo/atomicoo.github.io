---
title: 【数学天坑】之矩阵/向量求导（二）
date: 2020-08-07 17:06:54
updated: 2020-08-07 17:06:54
tags:
  - 机器学习
  - 数学
  - 矩阵求导
categories:
  - 一点理论
katex: true
---

## 引言

这是【数学天坑】系列博客的第二篇，或者说是开篇的第二部分。

对于矩阵/向量求导，哪怕只是机器学习中会涉及到的部分，显然也不是一篇博客能搞定的，再加上我对自己博客的期望是**“短小精悍”**，即在文章比较简短的前提下确保每次都完整地记录下自己想要说明的数个知识点（这样做最大的好处就是每篇博客都能用碎片时间看完，而碎片化阅读几乎是现在绝大多数人的常态），所以矩阵/向量求导部分我会分成几篇博客来完成。

本篇将搞定**定义法**求导的部分。

<!-- more -->

## 定义法

在上一篇博客已经说过，**矩阵求导本质上不过是多元函数求导**，只是将自变量/因变量/求导结果排列成矩阵形式，方便表达、计算及推导。因此，我们完全可以对矩阵的分量分别进行求导，然后再按照布局规范重新排列成矩阵，这就是所谓的**定义法**。

在实际计算时显然不会使用这种方法，原因也很简单，先来几个例子感受一下就明白了。

### 标量对向量

例1：$y=\bold{a}^{T} \bold{x}$，$\bold{a}, \bold{x} \in \R^{n}$，求 $\cfrac{\partial{\bold{a}^{T} \bold{x}}}{\partial{\bold{x}}}$？

根据定义，对 $\bold{x}$ 的第 $i$ 个分量求导：

$$ \cfrac{\partial{\bold{a}^{T} \bold{x}}}{\partial{x_{i}}}=\cfrac{\partial{\displaystyle\sum_{j=1}^{n}{a_{j}x_{j}}}}{\partial{x_{i}}}=\cfrac{\partial{a_{i}x_{i}}}{\partial{x_{i}}}=a_{i} $$

由此易得：

$$ \cfrac{\partial{\bold{a}^{T} \bold{x}}}{\partial{\bold{x}}}=\bold{a} $$

同理可得：

$$ \cfrac{\partial{\bold{x}^{T} \bold{a}}}{\partial{\bold{x}}}=\bold{a} \qquad \bold{a}, \bold{x} \in \R^{n} $$

$$ \cfrac{\partial{\bold{x}^{T} \bold{x}}}{\partial{\bold{x}}}=2 \bold{x} \qquad \bold{x} \in \R^{n} $$

例2：$y=\bold{x}^{T} A \bold{x}$，$\bold{x} \in \R^{n}, A \in \R^{n \times n}$，求 $\cfrac{\partial{\bold{x}^{T} A \bold{x}}}{\partial{\bold{x}}}$？

同样，对 $\bold{x}$ 的第 $i$ 个分量求导：

$$ \cfrac{\partial{\bold{x}^{T} A \bold{x}}}{\partial{x_{i}}}=\cfrac{\partial{\displaystyle\sum_{j=1}^{n}\displaystyle\sum_{k=1}^{n}{x_{j} a_{jk} x_{k}}}}{\partial{x_{i}}}=\displaystyle\sum_{j=1}^{n}{a_{ji} x_{j}}+\displaystyle\sum_{k=1}^{n}{a_{ik} x_{k}} $$

可得：

$$ \cfrac{\partial{\bold{x}^{T} A \bold{x}}}{\partial{\bold{x}}}=A^{T} x+A x $$

标量对向量求导的几条**基本法则**：

- 常量对向量求导结果为 0
- 线性法则：若 $f, g$ 为标量函数，$c_{1}, c_{2}$ 为常数，则：

$$ \cfrac{\partial{(c_{1} f+c_{2} g)}}{\partial{\bold{x}}}=c_{1} \cfrac{\partial{f}}{\partial{\bold{x}}}+c_{2} \cfrac{\partial{g}}{\partial{\bold{x}}} $$

- 乘法法则：若 $f, g$ 为标量函数，则：

$$ \cfrac{\partial{f g}}{\partial{\bold{x}}}=f \cfrac{\partial{g}}{\partial{\bold{x}}}+g \cfrac{\partial{f}}{\partial{\bold{x}}} $$

- 除法法则：若 $f, g$ 为标量函数，且 $g(\bold{x}) \neq 0$，则：

$$ \cfrac{\partial{f/g}}{\partial{\bold{x}}}=\cfrac{1}{g^{2}}(g \cfrac{\partial{f}}{\partial{\bold{x}}}-f \cfrac{\partial{g}}{\partial{\bold{x}}}) $$

这些法则在形式上其实与我们非常熟悉的标量对标量求导是统一的。

### 标量对矩阵

例3：$y=\bold{a}^{T} X \bold{b}$，$\bold{a} \in \R^{m}, \bold{b} \in \R^{n}, X \in \R^{m \times n}$，求 $\cfrac{\partial{\bold{a}^{T} X \bold{b}}}{\partial{X}}$？

对矩阵 $X$ 的任一分量 $x_{ij}$ 求导：

$$ \cfrac{\partial{\bold{a}^{T} X \bold{b}}}{x_{ij}}=\cfrac{\partial{\displaystyle\sum_{p=1}^{m}\displaystyle\sum_{q=1}^{n}{a_{p} x_{pq} b_{q}}}}{\partial{x_{ij}}}=a_{i} b_{j} $$

可得：

$$ \cfrac{\partial{\bold{a}^{T} X \bold{b}}}{\partial{X}}=a b^{T} $$

### 向量对向量

例4：$\bold{y}=A \bold{x}$，$x \in \R^{n}, A \in \R^{m \times n}$，求 $\cfrac{\partial{A \bold{x}}}{\partial{\bold{x}}}$？

$\bold{x} \in \R^{n}, \bold{y} \in \R^{m}$，根据上一篇博客，其求导结果（分子布局）应为 $\cfrac{\partial{\bold{y}}}{\partial{\bold{x}}} \in R^{m \times n}$。

现计算  $\bold{y}$ 任一分量 $y_{i}$ 对 $\bold{x}$ 任一分量 $x_{j}$ 求导的结果：

$$ \cfrac{\partial{A_{i,} \bold{x}}}{\partial{x_{j}}}=\cfrac{\partial{\displaystyle\sum_{k=1}^{n}{a_{ik} x_{k}}}}{\partial{x_{j}}}=a_{ij} $$

因为（分子布局）$\cfrac{\partial{\bold{y}}}{\partial{\bold{x}}} \in R^{m \times n}$，可得结果为 $A$ 而非 $A^{T}$：

$$ \cfrac{\partial{A \bold{x}}}{\partial{\bold{x}}}=A $$

## 总结

可见，当函数比较简单时定义法还是能够胜任的，而一旦函数稍微复杂一些，定义法就显得太麻烦了。此外，矩阵求导之所以存在就是因为其整体性，方便表达、计算以及推导，定义法破坏了整体性，重新退化成多元函数求导，矩阵求导的意义不复存在。

## 参考资料

[机器学习中的矩阵向量求导(二) 矩阵向量求导之定义法](https://www.cnblogs.com/pinard/p/10773942.html)

[机器学习中的矩阵求导技术](https://zhuanlan.zhihu.com/p/46908990)

<!-- Q.E.D. -->
