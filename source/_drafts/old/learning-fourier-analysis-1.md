---
title: 【数学天坑】之“学废”傅里叶分析（一）
date: 2020-07-27 13:47:24
updated: 2020-07-27 13:47:24
tags:
  - 数学
  - 傅里叶分析
categories:
  - 数学研究
katex: true
---

## 引言

**傅里叶分析**是分析学中的一个分支，在诸多领域都扮演着重要的角色。毫无疑问，它是一柄无比强大的数学“利器”，但与此同时，它更可以是一种能够彻底打破你的世界观、颠覆你看待世界方式的一种**思维模式**。遗憾的是，许多同学在学习相关知识时更多地抱着一种应付考试的态度，方式也倾向于死记硬背，并不能真正体会到傅里叶分析的“美感”，所以这篇博客将从另一个角度切入，从一个音符开始走进傅里叶分析的世界。

<!-- more -->

## 频域

从呱呱坠地到长眠地下，我们这一生会经历诸多事情，吃喝玩乐、结婚生子…… 这些事情，或大或小，但始终都有“时间”这一条线贯穿其中。譬如，股票趋势图（对对对，就是那个每一下波动都牵动着你的心决定了你是否要”上天台“的红绿绿绿绿绿的图），就是描述股价随着时间的变化，所以其横轴表示时间。以**时间**为参照来观察分析世界的方法，我们称之为**时域分析**。

从这一个角度来看待世界时，我们的世界是动态的、瞬时的，时时刻刻都在发生着变化，但所谓“横看成岭侧成峰”，那么是不是会有另外一个角度，当从那个角度来看待世界时，我们的世界将是静止的、永恒的呢？

答案是肯定的！我不是在开玩笑，在那个永恒静止世界里，我们以**频率**为参照，而从那个角度观察分析世界的方式，我们称之为**频域分析**。

那么问题来了，，，**什么是频域？**

让我们回到初中物理课的课堂。我们都知道，声音的源头是振动，有振动才会有声音，而最简单的振动可以用一个正弦波来表示：

$$ y=A \sin(\omega t + \theta) $$

> Notes：以前在数学课上学习三角函数时会分为正弦（sin）和余弦（cos），但实际上两者只是相差了一个相位，因此本文将统一用“正弦”二字来表示，不区分正余弦。

上式中，在 $\omega$ 确定的情况下，振动频率 $f=\cfrac{\omega}{2\pi}$ 确定，而频率又决定了音调。

接下来让我们再回到音乐课的课堂（当然，前提是你的音乐课没被各种所谓的正课所占据）。在五线谱上，音调的不同是由音符来表示的。

由此我们发现，一个音，在物理上的表示是一个随时间变化（周期性）的振动，而在音乐上的表示却只是一个简简单单的音符（频率）。

事实上，这就是音乐在时域和频域中完全不同的两个模样（也是引言中提过的，从不同的角度看待世界，世界将因此而大不相同）。好了，现在可以回过头来回答前面提出的那个问题了，什么是频域？**频域就是以频率为参照的世界**，“频率”这一条线将贯穿整个频域世界。

时域：

![sine.png](https://i.loli.net/2020/08/15/sWoCkQtne1HvLr4.png)

频域：

![note.jpg](https://i.loli.net/2020/08/15/pcfIFySHWCZQ4vP.jpg)

讲完了频域，那么傅里叶分析在这其中到底扮演了怎么样的重要角色呢？

答曰：**傅里叶分析是连接时域与频域的桥梁！**傅里叶分析可以打通时域世界与频域世界之间的“世界壁垒”，是贯通两个世界的重要方法之一。

接下来的内容中我们将从两个方面来介绍傅里叶分析，那就是傅里叶级数与傅里叶变换。

## 傅里叶级数

### 频率谱

在步入傅里叶分析的世界这一过程中，有一个很重要的门槛，那就是从内心里接受“**用正弦波可以合出矩形波**”这一反直觉的事实。

初学者很容易卡在这里，其根本原因在于矩形波方方正正、有棱有角，而正弦波圆润光滑，所以用正弦波来合出矩形波本身是很反直觉的。然而数学不是“想当然”，眼见才为实：

![gaitubao_rect-wave.png](https://i.loli.net/2020/08/15/bm6SFgXC5PJaDGB.png)

如上四个图依次是：

$$ \begin{aligned} y&=\frac{4}{\pi} \cos(t) \\\\ y&=\frac{4}{\pi} \cos(t)-\frac{4}{3\pi} \cos(3t) \\\\ y&=\frac{4}{\pi} \cos(t)-\frac{4}{3\pi} \cos(3t)+\frac{4}{5\pi} \cos(5t) \\\\ y&=\frac{4}{\pi} \cos(t)-\frac{4}{3\pi} \cos(3t)+\frac{4}{5\pi} \cos(5t)-\cdots+\frac{4}{21\pi} \cos(21t) \end{aligned} $$

可以看到，当我们加到 $\cos(5x)$（5 次谐波）时，已经稍微有点矩形波的感觉了，当加到 $\cos(21x)$ 时，这种感觉就更加明显了。

> Notes：这里我们将 $\cos(x)$ 称为基波，$\cos(nx)$ 称为 $n$ 次谐波。基波决定合成波形的整体，谐波决定合成波形的局部。对于声波来说，基波决定了声音的音调，谐波决定了声音的音色。

杯具的是，若要真正合成出矩形波，我们需要的正弦波是无数个，即：

$$ y=\displaystyle\sum_{i=0}^{\infin}{\frac{4}{(2i+1) \pi} \sin((2i+1) t)} $$

不过事情似乎渐渐变得有趣起来了，既然矩形波都能用正弦波合成出来，那大胆假设，三角波是不是也可以？其他更普通的波形是不是也可以？答案是NO趴笨！在这种方式下，正弦波可以合出任何你想得到的或者想不到的波形（前提是该波形要有**周期性**）。

![periodic_identity_function.gif](https://i.loli.net/2020/08/16/d6apF3PLBy9ebcG.gif)

这就是**傅里叶级数**，可以将任意周期函数（波形）展开成三角函数（正弦波）构成的无穷级数。傅里叶级数公式的三角函数形式如下：

$$ y=\cfrac{a_{0}}{2}+\displaystyle\sum_{n=1}^{\infin}{a_{n} \cos(n \omega t)+b_{n} \sin(n \omega t)} $$

其中：

$$ \begin{aligned} a_{n}&=\cfrac{2}{T} \displaystyle\int_{t_{0}}^{t_{0}+T}{y \cdot \cos(n \omega t) dt} \\\\ b_{n}&=\cfrac{2}{T} \displaystyle\int_{t_{0}}^{t_{0}+T}{y \cdot \sin(n \omega t) dt} \end{aligned} $$

> 注意，这里的”任意“并不是严格意义上的”任意“，傅里叶级数的收敛需要满足**狄利赫里条件**，但因为实践中遇到的绝大多数情况都是满足该条件的，所以可以近似地当成是”任意“。

接下来是见证奇迹的时刻~~

针对上述正弦波合成矩形波的“神奇”栗子，让我们换个角度来看看：

![gaitubao_rect-wave-5.png](https://i.loli.net/2020/08/15/fC7ZGQL2JqVv5Oy.png)

![gaitubao_rect-wave-11.png](https://i.loli.net/2020/08/15/4HUPeDcf6pmqIgM.png)

![gaitubao_rect-wave-21.png](https://i.loli.net/2020/08/15/YilQXtNrTIxq7Ho.png)

我们前面提到过谐波的概念，若波形叠加到 $n$ 次谐波时，记 $K \triangleq n$，则如上三图依次对应 $K=5, K=11, K=21$ 三种情况。图中，最前方的蓝色线条所表示的波形就是后方所有正弦波的叠加，可以看到，随着 $K$ 的增大，合成波形会越来越接近矩形波；后方花花绿绿的线条所表示的波形都是组成矩形波的正弦波分量，称为**频率分量**，每个频率分量的幅度都是不同的，表示组成矩形波所需要的各频率分量的“比例”是不同的。通常来说，基波的比例是最大的，它决定合成波的整体，合成波频率与基波频率一致；各次谐波的比例相对较小，它们决定合成波的局部。

如果将图形完全旋转过来我们就可以看到波形的频域表示（频谱），如下图：

![gaitubao_rect-wave-21-freq.png](https://i.loli.net/2020/08/15/jk14FBm6VSeKR7l.png)

这就是矩形波在频域世界中的模样，神奇吧。事实上，因为正弦波的振幅范围是关于 $x$ 轴对称的，所以更多时候你看到的频谱会是下面这样的：

![gaitubao_rect-wave-21-freq-norm.png](https://i.loli.net/2020/08/15/T7lrqUD2PB3jGmA.png)

最前面橘红色的那条线表示基波，后面的线分别表示各次谐波（矩形波的合成不需要偶次谐波，所以图上看不到偶次谐波分量）。

维基百科中有一张 [动态图](https://en.wikipedia.org/wiki/File:Fourier_series_and_transform.gif) 很好地展示了上述过程：

![Fourier_series_and_transform.gif](https://i.loli.net/2020/08/15/JmANLqjtbzQkE8M.gif)

值得一提的是，除了基波与各次谐波之外，还有一个点是我们需要关注的，那就是 ”$0$ 次谐波”，或者说是**直流分量**。如果将基波频率 $\omega_{0}$ 视作自然数系统中的 ”$1$“ 的话，那么直流分量就是自然数系统中的 ”$0$“。考虑 $\omega=0\omega_{0}=0$，此时 $a \cdot \cos(0t) \equiv a$ 恒为常量，在坐标系中表现为直线（或者说是周期无限大的正弦波），因此称为直流分量，它在合成波形的叠加中只会影响波形在 $z$ 轴上的整体偏移，而不影响波形的形状。

---

傅里叶级数的频谱部分到这里其实就结束了，不过考虑到后续的傅里叶变换，有些内容倒是可以先放出来，算是为傅里叶变换的内容提前做一些准备。

还记得吗，当初刚学习三角函数时数学老师是用这种方式来进行定义的：单位圆周运动在坐标轴上的投影。我在网络上找到了一张很有意思的动态图来展示这一过程：

![trigonometric-function.gif](https://i.loli.net/2020/08/15/Jfv2N1RYQnULqIo.gif)

因此上述正弦波合成矩形波的例子还可以用下面这张维基百科上的 [动态图](https://en.wikipedia.org/wiki/File:Fourier_series_square_wave_circles_animation.gif) 来表示：

![Fourier_series_square_wave_circles_animation.gif](https://i.loli.net/2020/08/15/ICdEPn7gkSXDWrU.gif)

Emmmm，这张图上用的是 $\sin(\cdot)$ 函数，而我用的是 $\cos(\cdot)$ 函数，不过前面也说过了，正余弦函数之间归根结底只是相差了一个相位而已，并不影响结论。

> Notes：这部分有助于理解后续傅里叶变换中三角形式转化为指数形式的内容。

### 相位谱

还记得我们一开始列出的正弦波公式吗？

$$ y=A \sin(\omega t + \theta) $$

在这个公式中，关键参数共有三个：$A,\omega, \theta$。在上一节中我们已经搞定了傅里叶级数的频谱，频谱涉及其中 $A, \omega$ 两个参数，那么接下来我们就来搞定与最后一个参数 $\theta$ 相关的相位谱。

如果说频谱的关键在于**从侧面看**的话，那么相位谱的关键就在于**从下面看**。

由于正弦函数是周期函数，所以我们需要设定一个标识来标记正弦波的位置（否则我们再怎么从下面看也只能看个寂寞），这个标识理论上可以是正弦波的任意位置，但通常为了方便会将距离频率轴最近的一个波峰作为标识，这样就可以人为地规定相位的取值范围为 $(-\pi, \pi]$。

需要注意的是，$\cfrac{\theta}{2\pi}=\cfrac{t}{T}$，即 $\theta=2\pi \cfrac{t}{T}$，所以对于相位谱，我们无法通过“从下面看”的方式直接看到，还需要有一个 $t-\theta$ 的转化过程。针对上述正弦波合成矩形波的例子，转化后的相位谱如下图：

![gaitubao_rect-wave-21-phase.png](https://i.loli.net/2020/08/15/s8f6yizPXxYDJ4l.png)

> Notes：在各个频率分量中，绝对的相位本身并没有太多实质意义，但是相对相位是有意义的，相对相位会直接影响到合成的结果。在声波中，影响的就是声音的音色。

### WHY ?

前两节我们已经介绍完了傅里叶级数的频谱和相位谱，但是还有一个非常关键的问题没有解决，那就是为什么展开成**三角函数**的无穷级数？换句话说，世上函数千千万，你三角函数“何德何能”？

这个问题和【泰勒级数为什么是展开成幂级数？】本质上是一样的问题。那我们不妨先来研究下泰勒级数为什么选择了**幂函数**。

在泰勒级数中，我们会将复杂函数展开成如下形式：

$$ y=\displaystyle\sum_{i=0}^{\infin}{a_{i}(x-x_{0})^{i}}, \quad a_{n}=\cfrac{1}{n!} y^{(n)} $$

那么 $(x-x_{0})^{i}$ 这样的幂函数到底有什么优势呢？显然，幂函数的 $n$ 阶求导是非常简单的，而导数对应着变化，$n$ 阶导数对应着第 $n$ 阶的变化率。所以幂函数很适合用来描述”精细“的变化，用幂函数的叠加来逼近某复杂函数在特定点 $x_{0}$ 附近（数学上叫做邻域 $\mathring{U}(x_{0},\delta)$）的值是比较容易的。

这里记住一句话就可以了：导数即变化，泰勒级数关注的是**变化**。

我们再回过头来考虑傅里叶级数。傅里叶级数选择三角函数又是因为它的什么优势呢？

So Easy！傅里叶级数本质上就是将周期连续信号分解成无穷个（离散的）正弦信号，所以相较于泰勒级数关注变化而言，傅里叶级数更关注的是**周期**，而最简单最典型的周期函数显然非三角函数莫属了吧。

此外还有一点，只有（在不考虑多种函数组合的情况下）两种函数能够在多次求导后还是自己本身，那就是（正余弦函数只算一种）：$e^{x}, \sin(x), \cos(x)$。

> Notes：通过欧拉公式 $e^{ix}=\cos(x)+i \sin(x)$ 又能将这两种函数进行统一。

---

这里多说几句题外话。

无论是泰勒级数展开还是傅里叶级数展开，本质上其实都是**线性空间中的投影操作**，幂函数（$1,x^{1},x^{2},\ldots,x^{n},\ldots$）或者三角函数（$1,\sin(x),\cos(x),\ldots,\sin(nx),\cos(nx),\ldots$）也只是在不同情况下选择的不同的**基底**而已。只要你愿意，完全可以选择方波（甚至更普通的波形）作为“世界的基础”（也就是线性空间的基）。归根结底这些操作也只是类似于“将向量投影到各个坐标轴上”这样的操作罢了：

$$ V=(a, b, c)=a \vec{i}+b \vec{j}+c \vec{k} $$

其中，$\vec{i}, \vec{j}, \vec{k}$ 分别为 $x, y, z$ 轴的单位向量表示。只不过投影的东西更复杂一些，不是简单的向量，而是复杂的函数。

> Notes：以方波作为基底搞出来的东西叫做“沃尔什变换”。

## 参考资料

[傅里叶分析之掐死教程（完整版）](https://zhuanlan.zhihu.com/p/19763358)

[什么是傅里叶级数？](https://wenku.baidu.com/view/a8cb3548336c1eb91a375dc6.html)

[傅里叶级数和幂级数的区别](https://baijiahao.baidu.com/s?id=1636391202494475281&wfr=spider&for=pc)

[傅里叶系列（一）傅里叶级数的推导](https://zhuanlan.zhihu.com/p/41455378)

[数学动态图：三角函数](http://www.360doc.com/content/17/0615/07/35804869_663243198.shtml)

[如何从听觉上理解声音的相位？](https://www.zhihu.com/question/53051266)

<!-- Q.E.D. -->
