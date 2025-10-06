---
title: 算法和计算复杂度 8 Branching Programs and Barrington’s Theorem
author: tinsir888
date: 2024/9/14
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 5782b241
---

$NC^1\subseteq L/poly\subseteq NL/poly\subseteq AC^1$.

The model of **branching programs** that give a precise characterization of $L/poly$ and $NL/poly$.

Deterministic / non-deterministic branching program

# Boolean Branching Program

## Deterministic

:book:Definition 32. A **Deterministic Boolean branching program** $B$ with $n$ inputs consists of the following:

- A directed acyclic graph $D=(V,A)$, where the out-degree of all nodes is in $\{0,1,2\}$.
- A partial labeling $\ell_V:V\to\{0,1,x_1,\cdots,x_n\}$ of the nodes of $D$.
- A partial labeling $\ell_A:A\to\{0,1\}$ of the edges of $D$.
- An initial node $s\in V$.

When node $v$ is not labeled by $\ell_V$, we say that $v$ is *dummy*.

If $\ell_V(v)\in\{0,1\}$, we say that $v$ is an *output* node.

The graph $D$ and the labeling function $\ell_V,\ell_A$ should satisfy:

1. The node $s$ is not a dummy node.
2. If $\ell_V(v)\in\{x_1,\cdots,x_n\}$, the out-degree of $v$ is $2$, and one outgoing edges of $v$ is labeled $0$ and $1$ by $\ell_A$ respectively.
3. If $\ell_V(v)\in\{0,1\}$, i.e., $v$ is an output node, the  out-degree of $v$ is $0$.
4. If $v$ is a dummy node, the in-degree of $v$ is not $0$, the out-degree of $v$ is $1$.

A branching program with $n$ inputs compute a Boolean function $f:\{0,1\}^n\to\{0,1\}$ in a natural way.

Given an input $x\in\{0,1\}^n$, start in the initial node $s$, follow a path to an output node, thereby giving the value of $f(x)$.

When passing through dummy node, directly go through the unique path.

When passing through labeled node, go to $1$-labeled edge if $x_i=1$, $0$-labeled edge if $x_i=0$.

## Non-deterministic

Simply modify the deterministic branching program:

- label of vertices is $\ell_V:V\to\{0,1,*,x_1,\cdots,x_n\}$. If $\ell_V(v)=*$, $v$ is called a *choice* node.
- We require the out-degree of $v$ is $2$ and outgoing edges are not labeled by $\ell_A$.

For a given input $x\in\{0,1\}^n$, traverse the branching program from $s$, but at a choice node choose to follow an arbitrary arc.

**We say that the non-deterministic branching program accepts $x$ if there exist such choices leading the path to an output node labeled $1$.**



:book:Definition 33. The **size of a branching program** is the number of nodes in the branching program. The **length of a branching program** is the length of the longest path from $s$ to a node to out degree $0$.



Then define complexity classes of languages computed by **size-bounded** branching programs.

:book:Definition 34. Let $S:\mathbb N\to\mathbb N$. $BPSIZE(S(n))$ is the class of languages computed by families of deterministic Boolean branching program of size $O(S(n))$. Similarly, $NPBSIZE(S(n))$ is the class of languages computed by families of non-deterministic Boolean branching program of size $O(S(n))$.

:thinking:Theorem 43. $L/poly=BPSIZE(n^{O(1)}),NL/poly=NBPSIZE(n^{O(1)})$.

:thinking:Theorem 44. $L=U_L-BPSIZE(n^{O(1)}),NL=U_L-NBPSIZE(n^{O(1)})$.

# Programs over Monoids and Bounded Width Branching Programs

monoid 含幺半群

What if we **restrict the width** of branching programs?

Consider the width to be constant.

:book:Definition 35. A branching program $B$ is **in layered form**, if there is a partition of the nodes of $B$ into layers $V_1,\cdots ,V_l,V=V_1\cup\cdots\cup V_l$, in such a way that every edge go between some pair of adjacent layers $V_i$ and $V_{i+1}$.

:book:Definition 36. The width of a branching program with partition into layers $V=V_1\cup\cdots\cup V_l$ is the size of the largest layer, $max_i|V_i|$.

Then here comes new complexity classes.

:book:Definition 37. $BWBP$ is the class of languages computed by families of Boolean branching programs **polynomial size** and **width** $O(1)$.

It's easy to show $AC^0\subseteq BWBP$ (:question:)

Conjecture: $MAJ\not\in BWBP$.

Fact: $BWBP=NC^1$.

Consider computation over finite algebraic structures, finite groups.

:book:Definition 38. A **monoid** is a set $M$ closed under an associative binary operation s.t. $M$ has an identity element:

1. For all $x,y\in M$, we have $xy\in M$. 封闭性
2. For all $x,y,z\in M$, we have $x(yz)=(xy)z$. 结合律
3. There is an element $1\in M$ s.t. $1x=x1=x$ for all $x\in M$ 含有幺元



The set of functions from a set $S$ to itself which forms a monoid, denoted $T_S$.

The set of *partial* functions from a set $S$ to itself forms a monoid, denote $PT_S$.

Commonsense: Any group is a monoid.

:book:Definition 39. Let $M$ be a finite monoid. A program $P$ over $M$ with $n$ inputs consists of the following:

1. A sequence $I_1,\cdots,I_l$ of triples of the form $\langle i,g_0,g_1\rangle$, where $i\in\{1,\cdots,n\}$ and $g_0,g_1\in M$.
2. An accepting set $A\subseteq M$.

The triples is called **instructions of the program** $P$.

A program $P$ over $M$ with $n$ inputs define a Boolean function $f:\{0,1\}^n\to\{0,1\}$ in a natural way:

- Let $x\in\{0,1\}^n$.
- The output of an instruction $I=\langle i,g_0,g_1\rangle$ is given by $I(x)=g_{x_i}$.
- The output of the program is $P(x)=\prod_{j=1}^lI_j(x)$.
- Finally $f(x)=1$ iff $P(x)\in A$.

:book:Definition 40. The length of a program $P$ is the number of instructions of $P$.



:thinking:Proposition 10: Let $f$ be a Boolean function computed by a family of polynomial length programs over a finite monoid $M$. Then $f\in NC^1$.

:thinking:Proposition 11. $BWBP\subseteq NC^1$.

# Barrington's Theorem

Any language in $NC^1$ can be computed by polynomial size constant width branching programs.

Theorem 45 (Barrington's Theorem). $BWBP=NC^1$.



By constructing polynomial length programs over group $S_5$ of permutations of $5$ elements.

The width is fixed $5$.

We denote the identity permutation by $e\in S_5$.



:book:Definition 41. Let $P$ be a program over $S_5$ and let $\sigma\in S_5$ be a $5$-cycle. We say that $P$ $\sigma$-computes a Boolean function $f$ if $P(x)=\sigma$ when $f(x)=1$ and $P(x)=e$ when $f(x)=0$. We can also simply say that $P$ $5$-cycle computes $f$.

:thinking:Lemma. Let $\sigma$ and $\tau$ be $5$-cycles. If a program $P$ over $S_5$ of length $l$ $\sigma$-computes $f$, then there is another program $P'$ of length $l$ over $S_5$ that $\tau$-computes $f$.

:thinking:Lemma. If a program $P$ over $S_5$ of length $l$ $5$-cycle computes $f$, then there is another program $P'$ of length $l$ that $5$-cycle computes $\neg f$.

:book:Definition 42 (commutator 交换子). Let $G$ be a group and $g,h\in G$. The commutator $[g,h]$ is the element $g^{-1}h^{-1}gh$.

:thinking:Lemma. There exists $5$-cycles $\sigma_1$ and $\sigma_2$ s.t. their commutator $[\sigma_1,\sigma_2]$, is also a $5$-cycle.

> $\sigma_1=(1,2,3,4,5),\sigma_2=(1,3,5,4,2).$
>
> $[\sigma_1,\sigma_2]=(1,2,5,3,4)$

:thinking:Proposition 12. Let $C$ be a fanin $2$ circuit of depth $d$. Then there exist a program $P$ of length at most $4^d$ that $5$-cycle computes the output of $C$.

By Proposition 11 and 12, we obtain Barrington's Theorem.