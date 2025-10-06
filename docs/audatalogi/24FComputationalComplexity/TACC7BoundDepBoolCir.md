---
title: 算法和计算复杂度 7 Bounded Depth Boolean Circuits
author: tinsir888
date: 2024/8/13
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 653a215a
---

What if Boolean circuits has bounded **depth**?

Depth of Boolean circuit: the **length** of a longest path from an input gate to an output gate.

It's a model of *parallel* computation where depth corresponds to parallel time.

Parallel model of random access machine PRAM ~~痛苦的计算几何课的回忆~~

:book:Definition of $SIZE-DEPTH$. Let $S:\mathbb N\to\mathbb N$ and $d:\mathbb N\to\mathbb N$. $SIZE-DEPTH(S(n),d(n))$ is the class of languages computed by families of Boolean circuits of size $O(S(n))$ and depth $O(d(n))$.

:book:Definition of $NC^i$. For $i\ge0$, define $NC^i=SIZE-DEPTH(n^{O(1)},\log^i(n))$, and further define $NC=\cup_{i\ge1}NC^i$.

These circuit classes is restrict to polynomial size and **poly-logarithmic** depth.

By definition, $NC\subseteq P/poly$, hence $U_L-NC\subseteq P$.

{% note info flat %}

为什么呢？回顾定义 $P/poly=\bigcup_{k\gt0}SIZE(n^k)$，发现它的多了一个深度的限制。

{% endnote %}

In fact $P\not\subseteq NC$ is expected to hold. This means not all languages in $P$ can benefit greatly from parallelism.

# General Boolean Circuits

Consider Boolean circuits that allows arbitrary fanin gate and more general functions than $AND, OR, NOT$.

General definition of Boolean circuit. A Boolean circuit $C$ with $n$ inputs and $m$ outputs consists of the following:

- An underlying directed **acyclic** graph $D=(V,A)$ (multiple arcs is allowed)
- A labeling $\ell$ of the nodes of $V$.
- A linear ordering on the arcs $A$ of $V$.
- A choice of $m$ output gates $o_1,\cdots,o_m\in V$

The size of $C$ is the **number of wires**. The labelling function $\ell$ should satisfy the following for all gates $g$:

- If the fanin of $g$ is $0$, then $\ell(g)\in\{0,1,x_1,\cdots,x_n,\overline{x_1},\cdots,\overline{x_n}\}$.
- If the fanin of $g$ is $k\ge1$, then $\ell(g)=f_g$ where $f_g:\{0,1\}^k\to\{0,1\}$ is an $k$-ary Boolean function.

$C$ compute a Boolean function $f:\{0,1\}^n\to\{0,1\}^m$ in a natural way.

## Boolean functions with k-ary

$$
AND(z_1,\cdots,z_k)=z_1\and\cdots\and z_k\\
OR(z_1,\cdots,z_k)=z_1\or\cdots\or z_k\\
MOD_m(z_1,\cdots,z_k)=\begin{cases}
1\ if\ \sum_{i=1}^kz_i\not\equiv0\mod m\\
0\ if\ \sum_{i=1}^kz_i\equiv0\mod m
\end{cases}
$$

$MOD_2$ is the same as the xor-sum.

The major voting function $MAJ$:
$$
MAJ(z_1,\cdots,z_k)=\begin{cases}
1\ if\ \sum_{i=1}^kz_i\ge\frac{k}{2}\\
0\ if\ \sum_{i=1}^kz_i\lt\frac{k}{2}
\end{cases}
$$
Let $w=(w_1,\cdots,w_k)\in\mathbb R^k$ be weights and $t\in\mathbb R$ be a threshold. Then $THR_{w,t}$ is defined:
$$
THR_{w,t}(z_1,\cdots,z_k)=\begin{cases}
1\ if\ \sum_{i=1}^kw_iz_i\ge t\\
0\ if\ \sum_{i=1}^kw_iz_i\lt t
\end{cases}
$$
When all weights is $1$,
$$
T_t(z_1,\cdots,z_k)=\begin{cases}
1\ if\ \sum_{i=1}^kz_i\ge t\\
0\ if\ \sum_{i=1}^kz_i\lt t
\end{cases}
$$

## Several Circuit Classes

:book:Definition of $AC^i$. For $i\ge0$, $AC^i$ is the class of languages computed by families of Boolean circuits of polynomial size and depth $O(\log^in)$ using **unbounded fanin** $AND$ and $OR$ gates.

$AC^i$ is similar to $NC^i$.

:thinking:Proposition 5. Hierarchy relation between $NC$ and $AC$. $NC^i\subseteq AC^i\subseteq NC^{i+1}$ for all $i\ge0$.

> $NC^i\subseteq AC^i$ is obtained by definition.
>
> For $AC^i\subseteq NC^{i+1}$: $AND$ and $OR$ gates of fanin $k$ can be replaced by a binary tree of depth $\lceil\log_2k\rceil$ of corresponding fanin $2$ gates. Thus the depth increases by a factor $O(\log n)$.

:book:Definition of $AC^0,ACC^0$. Let $m\gt1$. $AC^0[m]$ is the class of languages computed by families of Boolean circuits of polynomial size and depth $O(1)$ using unbounded fanin $AND,OR,MOD_m$ gates. Define $ACC^0=\bigcup_{m\gt1}AC^0[m]$.

:book:Definition of $TC^0$. $TC^0$ is the class of languages computed by families of Boolean circuits of polynomial size and depth $O(1)$ using unbounded fanin $MAJ$ gates only.

{% note info flat %}

语言类 $TC^0$ 可以被看做神经网络。

{% endnote %}

# Logarithmic Space and Logarithmic Depth Circuits

**log-space** can be roughly the same as **log-depth** circuits.

:dart:Theorem 38. $U_L-NC^1\subseteq L$.

> Circuit $C$ with depth $(\log n)$ is computable by a $O(\log n)$ space Turing Machine.

:book:Definition of Boolean product of matrices. $A=(a_{ij})\in\{0,1\}^{m\times r},B=(b_{ij})\in\{0,1\}^{r\times n}$. The Boolean product $AB$ is the $m\times n$ Boolean matrix $C=(c_{ij})$ s.t.
$$
c_{ij}=\bigvee_{k=1}^ra_{ik}\and a_{kj}.
$$
A $n\times n$ Boolean matrix corresponds to a directed graph. The transitive closure of the graph gives rise to the transitive closure of the matrix.

{% note info flat %}

回想起大二学的离散数学了吗？邻接矩阵的闭包与可达性。

{% endnote %}

:book:Definition of transitive closure. Transitive closure $A^*$ of $A$ is the $n\times n$ Boolean matrix given by $A^*=\bigvee_{i\ge0}A^i$, where $A^0$ is the identity matrix $I$.

:dart:Theorem 39. $NL\subseteq U_L-AC^1$

> ~~懒得看证明了，到学的时候再来~~

:thinking:Corollary 6. $U_L-NC^1\subseteq L\subseteq NL\subseteq U_L-AC^1$.

{% note info flat %}

其实这里也存在层次结构：$NL\subseteq U_L-AC^1\subseteq U_L-NC^2\subseteq L^2(=DSPACE(\log^2n))$.

{% endnote %}

# Functions and Reductions

Boolean circuit is fixed number of inputs, we consider output is also a bounded function of the length of input.

:book:Definition of **length-respecting**. A function $f:\{0,1\}^*\to\{0,1\}^*$ is length-respecting if for all $x,y\in\{0,1\}^*$, we have $|x|=|y|\Rarr|f(x)|=|f(y)|$.

:book:Definition of $AC^0$ **Turing reduction**. Let $f$ and $g$ be length-respecting Boolean functions. We say $f$ reduces to $g$ by an $AC^0$ Turing reduction if $f$ is computed by a family of Boolean functions of polynomial size and depth $O(1)$ using unbounded fanin $AND$ and $OR$ gates as well as gates computing any coordinate function of $g$.

~~什么玩意啊，绕来绕去的~~

If $f$ reduces to $g$ by an $AC^0$ Turing reduction, it is denoted by $f\le_T^{AC^0}g$.

Easy to see that $AC^0$ Turing reductions satisfy transitivity.

:thinking:Lemma 8. If $f\le_T^{AC^0}g$ and $g\le_T^{AC^0}h$, then $f\le_T^{AC^0}h$.

# Addition and Multiplication

$ADD$: given $n$-bit numbers $x$ and $y$, output $n+1$-bit number $z$ s.t. $z=x+y$.

$MULT$: given $n$-bit numbers $x$ and $y$, output $2n$-bit number $z$ s.t. $z=xy$.

:dart:Theorem 40. $ADD\in AC^0$.

> !!!

A generalization of $ADD$: iterated addition of $n$ numbers each of $n$ bits.

$ITADD$: given $n$ numbers $x^1,\cdots,x^n$ of $n$ bits each, output $n+\lceil\log_2n\rceil$-bit number $z$ s.t. $z=x^1+\cdots+x^n$.

:dart:Theorem 41. $ITADD\in NC^1$.

> We can to reduce the task to the task of add $2$ numbers each of $n+O(\log n)$ bits. These may be added in depth $O(\log n)$ by converting $AC^0$ for $ADD$ to a $NC^1$ circuit.

:thinking:Corollary 7. $TC^0\subseteq NC^1$.

> Note that $MAJ\le_T^{AC^0}ITADD$ by computing the sum of the input bits and comparing to the threshold value.

:thinking:Proposition 6. $MULT\le_T^{AC^0}ITADD$.

> Given $n$-bit numbers $x$ and $y$, we form the $n$ numbers $z^i$, each of $2n-1$ bits by letting $z^i=x_i2^iy$ for $0\le i\lt n$. Then the product is simply the sum of the numbers $z^0,\cdots,z^{n-1}$.

$BCOUNT$: given $n$-bit number $x$, output $\lceil\log_2^n+1\rceil$-bit number $z$, the sum of each bit.

Clearly $BCOUNT\le_T^{AC^0}ITADD$.

:thinking:Proposition 7. $ITADD\le_T^{AC^0}BCOUNT$

> ~~看不懂这个特例，但是图灵归约一般不是对称的~~

:thinking:Proposition 8. Let $f$ be a symmetric Boolean function. Then $f\in TC^0$.

:thinking:Corollary 8. $ACC^0\subseteq TC^0$.

:thinking:Proposition 9. $MAJ\le_T^{AC^0}MULT$.

:sob:这些证明都不想看

:dart:Theorem 42. $MULT$ is complete for $TC^0$ under $\le_T^{AC^0}$ reductions.

# Linear Threshold Functions

Using integers of bit-size $O(n\log n)$ as weights is sufficient to realize any linear threshold function.

:thinking:Lemma 9. Let $f$ be a linear threshold function on $n$ bits. Then there exists integers $w_1,\cdots,w_n$ and $t$ of magnitude at most $(n+1)^{\frac{n+1}{2}}$ s.t. $f=THR_{w,t}$.

> 证明跳过。

It's possible to improve the upper bound to $(n+1)^{\frac{n+1}{2}}/2^n$.

:thinking:Corollary 9. Let $f$ be a linear threshold function. Then $f\in TC^0$.



读下来感觉好崩溃:skull:

很难想象考试的时候是有多么悲惨。这么多定义都记不住，更何况这么多定理:sob: