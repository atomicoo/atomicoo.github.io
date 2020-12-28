---
title: 【数学天坑】之“学废”傅里叶分析（二）
date: 2020-08-16 09:27:24
updated: 2020-08-16 09:27:24
tags:
  - 数学
  - 傅里叶分析
categories:
  - 数学研究
katex: true
---

## 引言

在上一篇博客 [【数学天坑】之“学废”傅里叶分析](https://atomicoo.site/theory/learning-fourier-analysis-1/) 中我们已经研究过了傅里叶级数，由于篇幅之长远超我的预期，所以新开了第二篇来继续研究傅里叶变换的内容。

**傅里叶级数**本质上是将“任意”连续的周期信号分解成无穷多个（离散的）正弦波信号，然而我们的世界并不是周期的，也就是说，在更多的时候我们需要面对的应该是连续的非周期信号。对于这种情况，傅里叶级数就显得有些力不从心了。此时，铛铛铛铛 ~~~ ，傅里叶变换闪亮登场。

本篇博客的 C 位，**傅里叶变换**，能够将时域上非周期的连续信号转换成频域上连续的非周期信号。

<!-- more -->

## 傅里叶变换

引言中已经对傅里叶级数与傅里叶变换的区别进行了简单介绍，总结一下就是：

- 傅里叶级数：（时）周期性连续信号 $\Longrightarrow$ 离散非周期信号（频）
- 傅里叶变换：（时）非周期连续信号 $\Longrightarrow$ 连续非周期信号（频）

---

还记得我们上一篇博客中正弦波合成矩形波的例子吗？不妨回忆一下那三张神奇的图片。

![gaitubao_rect-wave-11.png](https://i.loli.net/2020/08/15/4HUPeDcf6pmqIgM.png)

上图是其中 $K=11$ 对应的那张图。随着 $K$ 的增大，最终合成的波形会是周期性的矩形波，如下图所示：

![square-wave.png](https://i.loli.net/2020/08/16/shWtwmVgHakPQ3l.png)

这里为了看起来更习惯一些，我增加了偏移量为 $1$ 的直流分量且将振幅压缩为原来的 $1/2$。

然而在实践中，很多情况下我们需要处理的真实信号是非周期的，譬如下图中的非周期矩形波。

![square-wave-.png](https://i.loli.net/2020/08/16/R5SLMQJFkprNIUo.png)

事实上可以将非周期函数视作周期无限大的周期函数，即 $T \to +\infin$，那么根据 $\omega=\cfrac{2 \pi}{T}$，有基波 $\omega_{0} \to 0$，此时频谱图像将由离散变为连续：

![gaitubao_rect-wave-11-freq-trans.png](https://i.loli.net/2020/08/16/m37R2VFsN6vg5QJ.png)

表示波形合成的三维图像也由原本傅里叶级数的离散形式变为傅里叶变换的连续形式：

![gaitubao_rect-wave-11-trans.png](https://i.loli.net/2020/08/16/rVFuG5ZbJ6ajhO2.png)



原本离散谱的叠加变成了连续谱的累积，公式中的累加符号变成了积分符号。

![fourier-series-to-trans.png](https://i.loli.net/2020/08/16/T3I2s8CF6pJAMH7.png)

## 参考资料

[傅里叶分析之掐死教程（完整版）](https://zhuanlan.zhihu.com/p/19763358)

[如何理解傅里叶变换公式？](https://www.zhihu.com/question/19714540)

[傅里叶系列（二）傅里叶变换的推导](https://zhuanlan.zhihu.com/p/41875010)

[傅里叶系列（三）离散傅里叶变换（DFT）](https://zhuanlan.zhihu.com/p/75521342)

---

To Be Continued.

<!-- Q.E.D. -->
