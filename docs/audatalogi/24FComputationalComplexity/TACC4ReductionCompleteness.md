---
title: 算法和计算复杂度 4 Reductions and Completeness
author: tinsir888
date: 2024/7/10
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 8b004a36
---

# Reductions

NP-completeness: by polynomial-time many-one reductions (aka Karp reductions)

Studying completeness of P and NL: by log-space many-one reduction. Also too powerful for completeness inside L.

:book:Definition 14 (Log-space reductions). Let $L_1\subseteq\Sigma_1^*$ and $L_2\subseteq\Sigma_2^*$ be languages. A **log-space many-one** reduction from $L_1$ to $L_2$ is a function $f:\Sigma_1^*\rarr\Sigma_2^*$ s.t.

1. There exists a ==log-space== Turing machine computing $f$.
2. For all $x\in\Sigma_1^*$, we have $x\in L_1\Lrarr f(x)\in L_2$.

Denote: $L_1\le_m^{\log}L_2$

The definition of **polynomial-time many-one** reduction ($L_1\le_m^pL_2$) is similar (simply using polynomial-time Turing machine instead).

:book:Definition 15 ($\le_m^{\log}$-hard/complete). Let $L$ be a language and $\mathcal C$ be a class of languages. We say that $L$ is $\le_m^{\log}$**-hard** for $\mathcal C$ if for every $L'\in\mathcal C$ we have $L'\le_m^{\log}L$. We say $L$ is $\le_m^{\log}$**-complete** if we additionally have $L\in\mathcal C$.

Reduction has property of **transitivity**.

:thinking:Lemma 1. $L_1\le_m^{\log}L_2, L_2\le_m^{\log}L_3\Rarr L_1\le_m^{\log}L_3$.

> Let $M_1$ and $M_2$ be log-space Turing machines computing $f_1:L_1\to L_2,f_2:L_2\to L_3$, respectively. It suffices to show that $f=f_2\circ f_1$ is computable by log-space Turing machine $M$.
>
> On input $x$, we simulate $M_2$ with virtual input tape $f_1(x)$. We maintain a counter giving the position of the virtual input tape head of $M_2$. To simulate step of computation of $M_2$, we start simulating $M_1$ on input $x$, counting and then discarding the output symbols by $M_1$ until the output symbol where the virtual input tape head is placed is to be written. Then the step of $M_2$ can be complete.
>
> Let $x$ be of length $n=|n|$. $|f(x)|=2^{O(\log n)}=n^{O(1)}$, storing the position of the virtual tape head as well as the work tapes of $M_1$ and $M_2$ takes space $O(\log n)$.

~~我觉得也挺显然的呀，用两台对数空间的图灵机模拟，总空间仍然是对数的。~~

Then we define reduction $\preceq$ between languages.

:book:Definition 16. A language $L$ is hard for $\mathcal C$ under $\preceq$ reductions if for all $L'\in\mathcal C$ we have $L'\preceq L$. A language $L$ is complete for $\mathcal C$ under $\preceq$ reductions if $L\in\mathcal C$ in addition to begin hard for $\mathcal C$ under $\preceq$ reductions.

Complexity class has an important property: the complexity class is **closed** under reductions.

We say $\mathcal C$ is closed w.r.t. $\preceq$ reductions, if whenever $L'\preceq L$ and $L\in\mathcal C$, we also have $L'\in C$. Proof omitted.

:thinking:Proposition 2. The classes $L,NL,P,NP,PSPACE,EXP,NEXP$ are all closed under log-space many-one reductions. The classes $P,NP,PSPACE,EXP,NEXP$ are all closed under polynomial-time many-one reductions.

# NL-completeness

The basic NL-complete problem is given by *connectivity in a directed graph* ($STCON$).

## STCON

The instance: Directed graph $D$ and nodes $s$ and $t$.

Question: Is there a directed path in $D$ from $s$ to $t$?

:dart:Theorem 20. $STCON$ is complete for NL.

> A non-deterministic log-space Turing machine can guess a path from $s$ to $t$ one node at a time, reusing space.
>
> To show NL-hardness, Let $L\in NL$ be accepted by non-deterministic log-space Turing machine $M$.
>
> Given input $x$ s.t. $M$ accepts $x$ iff there is a path in the configuration graph of $M$ on input $x$ from the initial configuration to an accepting configuration.
>
> A log-space reduction from $L$ to $STCON$ is:
>
> On input $x$ we generate all configurations of $M$ on input $x$, which then forms the set of nodes together with an additional node $t$. Initial configuration becomes the node $s$.
>
> Next generate all arcs between configurations corresponding to a single step of the Turing machine and finally arcs between all accepting configurations to the new node $t$. We then have that the resulting directed graph $D$ has a path from $s$ to $t$ iff $M$ accepts $x$.

By Savitch's Theorem and Immerman-Szelepcsényi Theorem, $STCON\in DSPACE(\log^2n)$ and $\overline{STCON}\in NL$, respectively.

# P/NP-completeness

Basic $P$ and $NP$-complete problems are given by the ability of Boolean circuits to simulate Turing machines on fixed input length. For $P$ this gives the circuit value problem.

## CVP problem

Instance: Boolean circuit $C$ on $n$ inputs and input $x\in\{0,1\}^n$.

Question: Is $C(x)=1$?

:dart:Theorem 21. $CVP$ is complete for $P$.

## SAT problem

### CircuitSAT

Instance: Boolean circuit $C$ on $n$ inputs

Question: Does there exist $x\in\{0,1\}^n$ s.t. $C(x)=1$?

:dart:Theorem 22. $CircuitSAT$ is complete for $NP$.

> ~~Let me use Chinese~~
>
> 为什么 CircuitSAT 属于 $NP$。简单理解就是非确定性图灵机 NTM 可以在多项式时间解决此问题。一个简单的思想就是暴力枚举，一共遍历 $2^n$ 次。这样在确定性图灵机 DTM 上确实就是 $O(2^n)$，但是在非确定性图灵机上，就厉害了。针对每一位，非确定性图灵机可以同时猜测 $0$ 和 $1$，这样一共遍历 $n$ 次分别对应 $n$ 位，就得到了 $NP$ 的结果。
>
> As for $NP$-hard, construct language $L\in NTIME(n^k)$ for $k\gt0$.
>
> ......

### SAT

Instance: CNF formula $\varphi$.

Question: Does $\varphi$ have a satisfying assigment?

:dart:Theorem 23. CNF satisfiability problem is also complete  for $NP$.

> It's easy to see $SAT\in NP$.
>
> For hardness, by reduction from $CircuitSAT$.
>
> Given a Boolean circuit $C$, take Tseitin transformation of $C$ giving an equi-satisfiable CNF formula $\varphi$.
>
> This involves introducing new variable for each gate of $C$.
>
> The number of new variable for each gate is a constant.

3SAT 也是 NP 的，即每个合取范式最多包含三个子句。

# PSPACE-completeness

Basic PSPACE-completeness is given by the problem of deciding validity of true quantified Boolean formulas as shown by Stockmeyer and Meyer. ($TQBF$)

Instance: Quantified Boolean formula with no free variable $\psi=Q_1x_1Q_2x_2\cdots Q_nx_n\varphi(x_1,\cdots,x_n)$, where $Q_i\in\{\forall,\exist\}$ and $x_i\in\{0,1\}$ for all $i$.

Question: Is $\psi$ true?

:dart:Theorem 24. $TQBF$ is complete for $PSPACE$.

> To see $TQBF\in PSPACE$: The algorithm is a recursion. At the bottom of the recursion, if $Q_i=\forall$, return $1$ iff both returned $1$; if $Q_i=\exist$, return $0$ iff both returned $0$. We of course need a stack with length $O(n)$ for evaluating $\varphi$.
>
> To show hardness, proof similar to Savitch's theorem.
>
> ......

# EXP/NEXP-completeness

Consider so-called **succinct** versions of complete problems of $P$ and $NP$.

Let $C$ be a Boolean circuit with $n$ input gates and one output gate. The truthtable of $C$ is the string $tt(C)\in\{0,1\}^{2^n}$ giving the evaluation of $C$ on all possible inputs.

Consider a language $L\subseteq\{0,1\}^*$. Then define the **succinct** version $SuccinctL$ of $L$ simply by $SuccinctL=\{C|tt(C)\in L\}$. Note that when $L\in P$ we have that $SuccinctL\in EXP$ simply by decompressing the input $C$ to $tt(C)$ in exponential time and then using the polynomial-time Turing machine to check for membership in $L$.

简而言之，把上面这些玩意的真值表算出来，就是属于 EXP/NEXP 的复杂度类。

Similarly we have $SuccinctL\in NEXP$ when $L\in NP$.

:dart:Theorem 25. $SuccinctCVP$ is complete for $EXP$.

> Similar transformation.

:dart:Theorem 26. $SucinctCircuitSAT$ and $SuccinctSAT$ are complete for $NEXP$.