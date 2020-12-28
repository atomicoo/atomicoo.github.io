---
title: 【数学天坑】之矩阵/向量求导（三）
date: 2020-08-08 10:34:23
updated: 2020-08-08 10:34:23
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

这是【数学天坑】系列之矩阵/向量求导的第三部分。

在前两篇文章中，已经介绍了矩阵/向量求导的定义、求导布局以及定义法求导的内容，同时通过几个例子说明了定义法求导的局限性，因此我们需要寻找一种更优的方法来完成矩阵/向量求导的任务。这篇博客将从导数与微分的关系出发，引出矩阵求导的**微分法**。

<!-- more -->

## 导数微分

在高等数学中，我们曾经学过一元函数的导数与微分的关系，那么这样的关系是否能推广到多元函数中？而又能否利用这样的关系来进行矩阵求导？显然，两个答案都是肯定的。

### 导数微分关系

现在先来回顾下高数里的知识，在单变量微积分中，导数与微分的关系：

$$ df=f^{'}(x)dx=\cfrac{df}{dx} dx \tag{1} $$

据此可以写出多变量时的情况（对应标量对向量求导）：

$$ df=\displaystyle\sum_{i=1}^{n}{\cfrac{\partial{f}}{\partial{x_{i}}} dx_{i}}=(\cfrac{\partial{f}}{\partial{\bold{x}}})^{T} d\bold{x} \tag{2} $$

进一步可以推广到矩阵（对应标量对矩阵求导）：

$$ df=\displaystyle\sum_{i=1}^{m}\displaystyle\sum_{j=1}^{n}{\cfrac{\partial{f}}{\partial{X_{ij}}} dX_{ij}}=tr((\cfrac{\partial{f}}{\partial{X}})^{T} dX) \tag{3} $$

这里使用了矩阵迹的一个性质：

$$ tr(A^{T} B)=\displaystyle\sum_{i,j}{a_{ij} b_{ij}} $$

由于标量可以视作一个 $1 \times 1$ 维的矩阵，其转置与迹都仍为自身，所以可以将以上三种情况统一起来：

$$ df=tr((\cfrac{\partial{f}}{\partial{X}})^{T} dX) \tag{∗} $$

### 矩阵微分法则

在进一步研究如何通过矩阵微分来求导之前，先花点时间眼熟下矩阵微分的几条运算法则：

- $d(X \pm Y)=dX \pm dY$
- $d(X Y)=X dY+Y dX$
- $d(X^{T})=(dX)^{T}$
- $dtr(X)=tr(dX)$
- $d(X \odot Y)=X \odot dY+dX \odot Y$
- $d\sigma(X)=\sigma^{'}(X) \odot dX$
- $dX^{-1}=-X^{-1} dX X^{-1}$
- $d|X|=|X| tr(X^{-1} dX)$

## 微分法

根据上一节得到的结论：$(∗)$ 式，我们完全可以利用矩阵微分来对矩阵进行求导，这就是所谓的**微分法**。

具体来说就是：若有标量函数 $f: \R^{m \times n} \to \R$，即标量函数 $f$ 是由加减乘逆迹行列式逐元素等矩阵运算构成，则可以利用**微分运算法则**先求出其微分 $df$，再利用**迹技巧**（trace trick）给求出的微分套上 $tr(\cdot)$，并将除 $dX$ 外的其他项交换至 $dX$ 的左侧，最终构造出 $df=tr(M^{T} dX)$ 的形式，这样就可以直接得到：$\cfrac{\partial{f}}{\partial{X}}=M$。

### 矩阵迹技巧

微分法求导需要的预备知识包括**矩阵微分运算法则**和**矩阵迹技巧**两部分，前者在上一节已经列出，那么这里我们就把主要用到的几个迹技巧也列出来：

- $tr(x)=x$
- $tr(A)=tr(A^{T})$
- $tr(AB)=tr(BA)$，$A,B^{T} \in \R^{m \times n}$
- $tr(X \pm Y)=tr(X) \pm tr(Y)$
- $tr((A \odot B)^{T} C)=tr(A^{T} (B \odot C))$，$A,B,C \in \R^{m \times n}$

### 微分法求导

例1：$y=a^{T} X b$，求 $\cfrac{\partial{y}}{\partial{X}}$？

对于上述例子，按照之前提到的微分法求导思路，先求出其微分：

$$ dy=d(a^{T} X b)=da^{T} X b+a^{T} dX b+a^{T} X db=a^{T} dX b $$

再利用迹技巧构造出 $dy=tr(M^{T} dX)$ 的形式：

$$ dy=tr(dy)=tr(a^{T} dX b)=tr(b a^{T} dX)=tr((a b^{T})^{T} dX) $$

最终得到求导结果：

$$ \cfrac{\partial{y}}{\partial{X}}=a b^{T} $$

例2：$y=a^{T} exp(X b)$，求 $\cfrac{\partial{y}}{\partial{X}}$？

这个例子相对比较复杂，但是思路不变，先计算微分：

$$ dy=d(a^{T} exp(X b))=a^{T} dexp(X b)=a^{T} (exp(X b) \odot d(X b)) $$

再利用迹技巧构造出 $dy=tr(M^{T} dX)$ 的形式：

$$ \begin{aligned} dy&=tr(dy) \\\\ &=tr(a^{T} (exp(X b) \odot d(X b))) \\\\ &=tr((a \odot exp(X b))^{T} d(X b)) \\\\ &=tr(((a \odot exp(X b)) b^{T})^{T} dX) \end{aligned} $$

最终得到求导结果：

$$ \cfrac{\partial{y}}{\partial{X}}=((a \odot exp(X b)) b^{T} $$

### 迹函数求导

注意到，微分法中有一个步骤是利用迹技巧给微分套上$tr(\cdot)$，那么迹函数本身进行矩阵求导的情况显然会更方便些。接下来给出几个常见迹函数的求导：

例3：$\cfrac{\partial{tr(AB)}}{\partial{A}}=B^{T}$

$$ d(tr(AB))=tr(d(BA))=tr((B^{T})^{T} dA) $$

例4：$\cfrac{\partial{tr(AB)}}{\partial{B}}=A^{T}$

$$ d(tr(AB))=tr(d(AB))=tr((A^{T})^{T} dB) $$

例5：$ \cfrac{\partial{tr(W^{T} A W)}}{\partial{W}}=(A+A^{T}) W $

$$ \begin{aligned} d(tr(W^{T} A W))&=tr(d(W^{T} A W)) \\\\ &=tr(d(W^{T}) A W+W^{T} d(A W)) \\\\ &=tr((dW)^{T} A W)+tr(W^{T} A dW) \\\\ &=tr(W^{T} (A+A^{T}) dW) \\\\ &=tr(((A+A^{T})^{T} W)^{T} dW) \end{aligned} $$

## 总结

上一篇博客中已经说明了定义法进行矩阵求导的局限性：复杂麻烦且破坏整体性。微分法很大程度上避免了这些问题。但在一些复杂的多层链式求导中微分法使用起来仍然有些麻烦，这就需要我们能够记忆一些常用的求导结果，并利用链式求导法则来进行计算。

## 参考资料

[机器学习中的矩阵向量求导(三) 矩阵向量求导之微分法](https://www.cnblogs.com/pinard/p/10791506.html)

[机器学习中的矩阵求导技术](https://zhuanlan.zhihu.com/p/46908990)

<!-- Q.E.D. -->