---
title: APS 非重点复习课程之离散数学
author: tinsir888
date: 2022/11/04
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 离散数学
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 7c1dd781
---

# Logic and Reasoning

[details](https://tinsir888.github.io/posts/9805f71d.html) in Introduction to AI.

# Modern Algebra

## Magma

magma 代数系统

Binary operation assume S is a set mapping f: $S\times S\rightarrow S$ is a dyadic operation on S.

dyadic operation 二元运算:

1. The result of the dyadic operation is still belong to S.

2. Every element in S can participate in the dyadic operation and get a unique result.

   e.g. In the natural number set N addition and multiplication are binary operators on N, but subtraction and division are not.

## Quasigroup

准群

assume G is a nonempty set and $\circ$ is the dyadic operation on the G, then $\circ$ is called multiplication. If the following condition is true, we call G is a quasigroup about binary operation multiplication $\circ$.

> $a,b\in G$, equation $a\circ x=b$ and $y\circ a=b$ have solutions in G.

## Semigroup

半群

assume G is a nonempty set and $\circ$ is the dyadic operation on the G, $\circ$ is called multiplication. If the following condition is true, we call G is a semigroup about dyadic operation multiplication $\circ$.

> $a,b,c\in G$, $(a\circ b)\circ c=a\circ(b\circ c)$. (associative law)

## Monoid

幺半群

Suppose that S is a set and $\cdot$ is some dyadic operation in S, then S with $\cdot$ is a monoid if it satisfies the following two axioms

> 1. Associativity for all a, b and c in S, the equation $(a\cdot b)\cdot c=a\cdot(b\cdot c)$ holds.
> 2. Identity element. there exists an element e in S such that for every element a in S, equation $e\cdot a=a\cdot e$ hold.

## Group

群

A group is a set, G, together with an operation $\cdot$ that combines any two elements a and b to form another element, denoted $a\cdot b$ or $ab$. To qualify as a group, the set and operation, $(G,\cdot)$, must satisfy 4 requirements known as the group axioms 群公理

> 1. Closure for all a, b in G, the result of the operation $a\cdot b$, is also in G.
> 2. Associativity. for all a, b and c in G, $(a\cdot b)\cdot c=a\cdot(b\cdot c)$.
> 3. Identity element. there exists an element e in S such that for every element a in S, equation $e\cdot a=a\cdot e$ hold.
> 4. Inverse element. for each a in G, there exists an element b in G, commonly denoted $a^{-1}$ or $-a$, s.t. $a\cdot b=b\cdot a=e$, where e is the identity element.

## Ring

A ring is a set R equipped with 2 binary operations $+$ and $\cdot$, satisfying the following 3 sets of axioms, called the ring axioms.

> 1. R is an abelian group（阿贝尔群） under addition. i.e.
>    - $(a+b)+c=a+(b+c)$ associative
>    - $a+b=b+a$ commutative 交换律
>    - there is an element 0 in R s.t. $a+0=a$ for all a in R (0 is additive identity)
>    - for each a in R there exists -a in R s.t. $a+(-a)=0$ (-a is the additive inverse of a) 加法逆元
> 2. R is a monoid under multiplication.
>    - $(a\cdot b)\cdot c=a\cdot(b\cdot c)$ associative
>    - $a\cdot1=1\cdot a=a$ multiplicative identity
> 3. Multiplication is distributive with respect to addition.
>    - $a\cdot(b+c)=(a\cdot b)+(a\cdot c)$ left distributivity
>    - $(b+c)\cdot a=(b\cdot a)+(c\cdot a)$ right distributivity

# Set Theory

## Set

Difference Set 差集

Symmetry difference 对称差 $A\Delta B=A\bigcup B-A\bigcap B$

De Morgen's Law

The principle of capacitive repulsion. 容斥原理

## Map

Injective: x1!=x2, f(x1)!=f(x2) 单射

surjective: every y has a x from X that points to it. 满射

bijection: injective + surjective. 双射

# Graph Theory

Undirect Graph 无向图

Direct Graph 有向图

## Euler Graph

Has Euler Circle.

Undirect: degree of vertices is even number.

Direct: Input degree=Output Degree

能“一笔画”的图

## Hamiltonian Graph

Has a Hamiltonian Circle

能“一笔画”且回到原点的图

## Minimum Spanning Tree

Kruskal algorithm

Prim algorithm

[details](https://tinsir888.github.io/posts/efc05345.html) in Introduction to algorithm