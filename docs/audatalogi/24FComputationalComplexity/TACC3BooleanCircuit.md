---
title: 算法和计算复杂度 3 Boolean Circuits
author: tinsir888
date: 2024/7/4
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: fc2e4161
---

*Non-uniform* model of computation: Boolean circuit model.

In contrast to uniform model like Turing machine.

Boolean circuit: Fixed number of inputs. To study computation of arbitrary inputs, we need a family of Boolean circuits.

# Some Definitions

:book:Definition of **Boolean circuit**. A Boolean circuit $C$ with $n$ inputs and $m$ outputs consists of:

1. A directed ==acyclic== graph $D=(V,A)$ where the in-degree of every node is at most $2$.
2. A labeling $\ell$ of all nodes $g\in V$ with $\ell(g)\in\{0,1,x_1,\cdots,x_n,\overline{x_1},\cdots,\overline{x_n},\land,\lor,\neg\}$.
3. A choice of $m$ output gates $o_1,\cdots, o_m\in V$.

The nodes of $D$ is the gates of $C$, the arcs of $D$ is the wires of $C$.

Nodes with in-degree $0$ are input gates.

The size of $C$ is the number of gates.

**Fanin** of a gate is its in-degree, **fanout** is its out-degree.

Labeling function should satisfy: For all gates $g$:

1. If the fanin is $0$, then $\ell(g)\in\{0,1,x_1,\cdots,x_n,\overline{x_1},\cdots,\overline{x_n},\land,\lor,\neg\}$.
2. If the fanin is $1$, then $\ell(g)=\neg$.
3. If the fanin is $2$, then $\ell(g)\in\{\land,\lor\}$.



A Boolean circuit $C$ with $n$ inputs and $m$ outputs computes  a Boolean function $f:\{0,1\}^n\to\{0,1\}^m$.

Implicit negation such as $\overline{x_i}$ can be made explicit by introducing a new gate, thereby at most doubling the size of the circuit.

:book:Definition of **family of Boolean circuits**. A family $(C_n)_{n=0}^\infty$ of Boolean circuits, where $C_n$ is a circuit with $n$ inputs computes the Boolean function $f:\{0,1\}^*\to\{0,1\}$ given by $f(x)=C_{|x|}(x)$. We say that $(C_n)_{n=0}^\infty$ **computes the language** $L\subseteq\{0,1\}^\star$ if  $(C_n)_{n=0}^\infty$ computes the characteristic function $\chi_L:\{0,1\}^\star\to\{0,1\}$ of $L$ given by  $\chi_L(x)=1$ iff $x\in L$.

!!! info

    直白一点：我们说一个布尔电路族 $(C_n)_{n=0}^\infty$ 计算~~（接受？）~~语言 $L$，当且仅当该布尔电路族的输入 $x$ 属于语言 $L$时，输出结果为 $1$。



If we want to apply non-Boolean alphabet, we just encode these letters into $\{0,1\}^k$ for some fixed $k$.

:book:Definition of $SIZE$. Let $S:\mathbb N\to\mathbb N$. $SIZE(S(n))$ is the class of languages computed by family of Boolean circuit of size $O(S(n))$,

We interested in class of languages computed by a family of polynomial size.
$$
P/poly=\bigcup_{k>0}SIZE(n^k).
$$
:book:Definition of **log-space/polynomial-time uniform**. Let $(C_n)_{n=0}^\infty$ be a family of Boolean circuits of size $O(S(n))$. We say that the family is *log-space uniform* if the function $1^n\mapsto C_n$ is computable in space $O(\log S(n))$. The family is *polynomial-time uniform* if the function is computable in time $(S(n))^{O(1)}$.

:book:Definition of complexity classes of languages computed by families of Boolean circuits. Let $S:\mathbb N\to\mathbb N$.

- $U_L-SIZE(S(n))$ is the class of languages computed by log-space uniform families of Boolean circuits of size $O(S(n))$.
- $U_P-SIZE(S(n))$ is the class of languages computed by polynomial-time uniform families of Boolean circuits of size $O(S(n))$.

# Boolean Formulas

:book:Definition of **Boolean formulas**. A Boolean formula (aka de Morgan formula) is a Boolean circuit where the underlying directed graph is a tree.

合取范式 conjunctive normal form CNF

析取范式 disjunctive normal form DNF

:dart:Theorem 14. Any Boolean function $f:\{0,1\}^n\to\{0,1\}$ is computed by a CNF with $s$ clauses of length $n$ and a DNF with $t$ terms of length $n$ where
$$
s=|\{x\in\{0,1\}^n|f(x)=0\}|,\\
t=|\{x\in\{0,1\}^n|f(x)=1\}|.
$$

# The Shannon Function

The smallest size $L(n)$ in which any Boolean function on $n$ variables can be computed by a Boolean circuit of size $L(n)$.

The function $L(n)$ is called the Shannon function.

By DNF/CNF formulas, we have trivial bound $L(n)\le n2^n$.

Shannon proved that $L(n)=\Theta(2^n/n)$.

:dart:Theorem 15 (Lower Bound on Circuit Size). $L(n)>\frac{2^n}{4n}$ for $n\ge9$. In fact, the fraction of Boolean functions computed by Boolean circuits of size at most $\frac{2^n}{4n}$ is at most $2^{-2^{n-1}}$ for $n\ge9$.

> For a given number of inputs $n\ge1$: first give an upper bound on number of Boolean circuits with $n$ inputs and $1$ output, with at most $s\ge1$ gates. Consider an acyclic ordering of the gates of the circuit and assume the last gate is the output gate.
>
> Different types of gate: input gates have $2n+2$ probabilities, negation with one input $s$, and/or with two inputs $(s^2,s^2)$. Thus we have at most $2n+2+s+s^2+s^2$ options for every gate, meaning that we have at most $(2n+2+s+2s^2)^s$ many circuits with at most $s$ gates.
>
> For $s\ge n+1$ we have
> $$
> 2n+2+s+2s^2\le4s^2\\
> \Rightarrow(2n+2+s+2s^2)^s\le(2s)^{2s}.
> $$
> Each of these at most $(2s)^{2s}$ circuits computes precisely one Boolean function. There are exactly $2^{2^n}$ different Boolean functions on $n$ inputs.
>
> Thus if $2^{2^n}\gt(2s)^{2s}$, there must be a Boolean function that is not computed by a Boolean circuit of size $s$. (By pigeon hole theorem)
>
> Let $n\ge9,s=\frac{2^n}{4n}$, which satisfies $s\ge n+1$, we have
> $$
> (2s)^{2s}=2^{2s\log_2(2s)}=2^{\frac{2^n}{2n}(n-1-\log n)}\lt2^{2^{n-1}}.
> $$
> It follows that less than $2^{2^{n-1}}$ Boolean functions on $n$ inputs are computed by Boolean circuits of size at most $\frac{2^n}{4n}$.

# Turing Machines versus Boolean Circuits

Oblivious Turing machine can efficiently be converted to a family of Boolean circuits.

:thinking:Proposition 1. Let $T(n)\ge n$ and suppose $L$ is computed by a $O(T(n))$ time-bounded oblivious Turing machine. Then $L\in SIZE(T(n))$.

> Any configuration of oblivious Turing machine on input $x$ can be encoded by a binary string of length $O(T(n))$ s.t. every symbol of every work tape corresponds to a fixed number of positions of the encoding.
>
> We can build the Boolean circuit for input length $n$ in $O(T(n))$ steps. Proof by induction.

Proposition 1 and Theorem 4 result in some inclusion relation.

:dart:Theorem 16. $DTIME(T(n))\subseteq SIZE(T(n)\log T(n))$ for $T(n)\ge n$.

Turing machine is supplied with an *advice string* in addition to the input, where the advice string is specific to the input length.

:book:Definition of **Advice classes**. Let $\mathcal C$ be a class of languages and $f:\mathbb N\to\mathbb N$ a function. Define $\mathcal C/f(n)$ to be the class of languages $L$ for which there exist $L'\in\mathcal C$ satisfying that for all input lengths $n$, there is an **advice string** $y_n$ of length $f(n)$ s.t. for all $x$ of length $n$, we have $x\in L$ iff $\langle x,y_n\rangle\in L'$. For a family $\mathcal F$ of functions $F:\mathbb N\to\mathbb N$, we let $\mathcal C/\mathcal F=\bigcup_{f\in\mathcal F}\mathcal C/f(n)$.

:dart:Theorem 17. Let $S(n)\ge n$. Then $SIZE(S(n))\subseteq DTIME(S(n)^2)/O(S(n)\log S(n))$.

> The advice string (length $n$) is an encoding of the circuit $C_n$, which is a string of length $O(S(n)\log S(n))$.
>
> Assume that the gates of $C_n$ are given in the encoding in acyclic order. On input $x$ of length $n$ and advice $C_n$, a Turing machine can evaluate $C_n$ on input $x$, evaluating the gates in the given acyclic ordering and storing all intermediate outputs.
>
> To evaluate each gate we need to retrieve the computed value of its inputs, which will take $O(S(n))$ time. Doing this for all $O(S(n))$ gates leads to $O(S(n)^2)$.

:dart:Corollary 1. Let $T(n)\ge n$. Then $DTIME(T(n)^{O(1)})/T(n)^{O(1)}=SIZE(T(n)^{O(1)})$

> Proof by Definition of advice class and Theorem 17.

:dart:Theorem 18. Let $T(n)\ge n$ and suppose the function $1^n\mapsto T(n)$ is computable in space $O(\log T(n))$. Then $DTIME(T(n))\subseteq U_L-SIZE(T(n)\log T(n))$.

:dart:Theorem 19. Let $(S(n))\ge n$. Then $U_L-SIZE(S(n))\subseteq DTIME(S(n)^{O(1)})$.

:dart:Corollary 2. Let $T(n)\ge n$ and suppose that function $1^n\mapsto T(n)$ is computable in space $O(\log T(n))$. Then $DTIME(T(n)^{O(1)})=U_L-SIZE(T(n)^{O(1)})$.

In particular, we have $P=U_L-SIZE(n^{O(1)})$.