---
title: Katex 测试
date: 2020-07-17 13:29:21
updated: 2020-07-17 13:29:21
katex: true
---

注意，本测试的 `.md` 源文档在 Typora 上编写。当使用 `\(...\)` 或者 `\[...\]` 作为公式块的界定符时，需要多一个 `\` 来进行转义，即 `\\(...\\)` 或者 `\\[...\\]`。当使用 美元符 `$` 或者 `$$` 作为界定符时，在 Typora 本地**「所见即所得」**（尚未经过完全测试）。

---

**行内公式**

```latex
\\( E = m c^2 \\)
```

\\( E = m c^2 \\)

```latex
$ E = m c^2 $
```

$ E=mc^2 $

```latex
$ \alpha + \beta = \gamma $
```

---

**行间公式**

```latex
\\[ E = m c^2 \\]
```

\\[ E = m c^2 \\]

```latex
$$ E = m c^2 $$
```

$$ E = m c^2 $$

---

**方程组**

```latex
$$ \begin{aligned} 3x + 4y &= 10 \\\\ 2x + 5y &= 13 \end{aligned} $$
```

$$ \begin{aligned} 3x + 4y &= 10 \\\\ 2x + 5y &= 13 \end{aligned} $$

```latex
$$ \begin{cases} 3x + 4y &= 10 \\\\ 2x + 5y &= 13 \end{cases} $$
```

$$ \begin{cases} 3x + 4y &= 10 \\\\ 2x + 5y &= 13 \end{cases} $$

```latex
$$ f(x) = a - b \tag{1.1} $$
```

$$ f(x) = a - b \tag{1.1} $$

---

**条件/分段函数**

```latex
$$ y = \begin{cases} x^2 + x &\text{if } a \\\\ -x^2 + 1 &\text{if } b \end{cases} $$
```

$$ y = \begin{cases} x^2 + x &\text{if } a \\\\ -x^2 + 1 &\text{if } b \end{cases} $$

---

**矩阵**

```latex
$$ \begin{pmatrix} a & b \\\\ c & d \end{pmatrix} $$
$$ \begin{bmatrix} a & b \\\\ c & d \end{bmatrix} $$
$$ \begin{vmatrix} a & b \\\\ c & d \end{vmatrix} $$
$$ \begin{Vmatrix} a & b \\\\ c & d \end{Vmatrix} $$
$$ \begin{Bmatrix} a & b \\\\ c & d \end{Bmatrix} $$
```

$$ \begin{pmatrix} a & b \\\\ c & d \end{pmatrix} \qquad \begin{bmatrix} a & b \\\\ c & d \end{bmatrix} \qquad \begin{vmatrix} a & b \\\\ c & d \end{vmatrix} \qquad \begin{Vmatrix} a & b \\\\ c & d \end{Vmatrix} \qquad \begin{Bmatrix} a & b \\\\ c & d \end{Bmatrix} $$

```latext
$$ \begin{pmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\\\ a_{21} & a_{22} & \cdots & a_{2n} \\\\ \vdots & \vdots & \ddots & \vdots \\\\ a_{m1} & a_{m1} & \cdots & a_{mn} \end{pmatrix} $$
```

$$ \begin{pmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\\\ a_{21} & a_{22} & \cdots & a_{2n} \\\\ \vdots & \vdots & \ddots & \vdots \\\\ a_{m1} & a_{m1} & \cdots & a_{mn} \end{pmatrix} $$

---

**上下标记**

```latex
$$ \overbrace{1+2+\cdots+n}^{n个} \qquad \underbrace{a+b+\cdots+z}_{26} $$
```

$$ \overbrace{1+2+\cdots+n}^{n个} \qquad \underbrace{a+b+\cdots+z}_{26} $$

---

To Be Continued.

---

**便捷工具**

[KATEX 支持语法（类型）](https://katex.org/docs/supported.html)

[KATEX 支持语法（表格）](https://katex.org/docs/support_table.html)

[Detexify - 根据 LATEX 符号搜索名称](http://detexify.kirelabs.org/classify.html)

<!-- Q.E.D. -->
