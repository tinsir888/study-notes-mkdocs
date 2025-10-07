---
title: '算法和计算复杂度 1 Turing Machines, Time and Space'
author: tinsir888
date: 2024/6/22
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 7f7103e4
---

Theory of Algorithms and Computational Complexity

Lecturer: Kristoffer Arnsfelt Hansen

:skull_and_crossbones::skull_and_crossbones::skull_and_crossbones:The **most difficult** (no "one of") course in my master's study.:skull_and_crossbones::skull_and_crossbones::skull_and_crossbones:

# Brief Intro to TACC

Study of efficient computation

1960s Hartmanis, Stearns - Time Hierarchy Theorem.

Turing machine, register based machine can simulate each other.

# Turing Machines

Turing machines - simple, and has some nice properties:

- Clean definition of complexity (time and space)
- Straightforward to non-determinism and randomness.

Consider "offline multi-tape sequential Turing machine"

Turing machine is a finite-state machine that can access and modify chars written on "tapes" according to some rules.

## Formal Definition

Turing machine $M$ with one input tape and $k$ work tapes. It includes:

- Finite set $Q$: set of states

  - Element $s\in Q$: **start** state
  - Element $yes\in Q$: **accept** state
  - Element $no\in Q$: **reject** state

- Finite set $\Gamma$: the tape alphabet

  - non-empty finite set $\Sigma\subseteq\Gamma$: **input alphabet**

- Element $\Delta\in\Gamma\diagdown\Sigma$: **blank symbol**

- Function
  $$
  \delta:(Q\diagdown\{yes,no\})\times(\Sigma\cup\{\Delta\})\times\Gamma^k\rightarrow Q\times(\Gamma\diagdown\{\Delta\})^k\times\{L,S,R\}^{k+1}
  $$
  is the **transition function**.

!!! info

    状态转移方程的意思：

    转移前：$(Q\diagdown\{yes,no\})$ 代表转移前状态，不能是接受或拒绝态（因为接受或拒绝就停机了）；$(\Sigma\cup\{\Delta\})$ 代表输入字符；$\Gamma^k$ 代表之前当前针头分别指 $k$ 个工作纸带对应位置的字符。

    转移后：$Q$ 代表转移后的状态，可以是接受或拒绝态；$(\Gamma\diagdown\{\Delta\})^k$ 表示针头分别在 $k$ 个工作纸带上对应位置写入的字符；$\{L,S,R\}^{k+1}$ 表示接下了输入纸带和 $k$ 个工作纸带应该左移（$L$）、右移（$R$），还是不动（$S$）。



Transition Function has following properties:

1. $M$ will stop operation when entering $yes$ or $no$ state.
2. $M$ marks all cells visited on its work tapes

The **operation** of $M$:

- $k+1$ two-way infinite tapes. One for input, the rest for work.
- The cells of each work tape are numbered by the set $\mathbb Z$.
- Input tape on which the cell $1,\cdots,|x|$ are filled with char $x_i$. The other cells are filled with $\Delta$.
- Each tape has a tape head, which initially stays at position $0$.
- The machine $M$ maintain an element of $A$ as its internal state.

The **computation** of $M$:

- Move the tapes according to transition function
- If input head is called to "move left" at position $0$, or "move right" at position $|x|+1$, let $M$ "crash". (So there are $|x|+2$ legal cell for input tape head)
- When $M$ enters $yes$ or $no$, it stops. We called $M$ **halts**. ($M(x)=yes$ or $M(x)=no$)

Turing machine can be seen as *language acceptor* useful for *decision problems* whether the language is *formal language*, i.e., the language computed by $M$ is $L(M)=\{x\in\Sigma|M(x)="yes"\}$.

## An Extension on Turing Machine

Add an additional *output tape* which is *write-only*.

$\Lambda$: non-empty finite set, *output alphabet*. (Usually $\Lambda=\Sigma$)

In each step of computation $M$ can choose to write a single symbol to the output tape. Then transition function:
$$
\delta:(Q\diagdown\{yes,no\})\times(\Sigma\cup\{\Delta\})\times\Gamma^k\\
\rightarrow Q\times(\Gamma\diagdown\{\Delta\})^k\times(\Lambda\cup\{\epsilon\})\times\{L,S,R\}^{k+1}
$$
$\epsilon$ here means write nothing.

Such a Turing machine is also called a *transducer*.

# Time and Space Complexity

For a given input $x\in\Sigma^*$:

- $time_M(x)$ is the number of computational steps that $M$ performs on input $x$ until **halting**.
- $space_M(x)$ is the total number of cells on the *work tapes* that are at some point scanned by a tape head during computation on input $x$.

Both time and space can be infinite. Space can be infinite when $M$ does not halt.

:book:Definition of **time/space-bounded**: let $T:\mathbb N\rightarrow\mathbb N$ and $S:\mathbb N\rightarrow\mathbb N$ be functions with $T(n)\ge n$. We say $M$ is $T(n)$ time-bounded, if for every $x\in\Sigma^*$ we have $time_M(x)\le T(|x|)$. Similarly, $M$ is $S(n)$ space-bounded, if for every $x\in\Sigma^*$ we have $space_M(x)\le S(|x|)$.

Then define the first **complexity classes** based on time/space bounds.

:book:Definition of $DTIME,DSPACE$: $DTIME(T(n))$ is the class of languages computed by a $O(T(n))$ time-bounded Turing machine and $DSPACE(S(n))$ is the class of languages computed by a $O(S(n))$ space Turing machine.

!!! warning

    $D$ 代表 deterministic （确定）。确定性图灵机每次状态转移只有一种。

    $N$ 代表 non-deterministic （非确定）。非确定性图灵机每次状态转移可能有多种，机器随机选择其中的一种进行转移。



Why $O$ notation here?

1. Compressing several tapes symbols to one class by an arbitrary linear factor.

2. WLOG, one work tape can simulate $k$ work tapes by special marking char.

   也就是说可以用一个标记字符把一个工作纸带分成 $k$ 份。

:book:Definition of **time/space-constructible**: $T$ is time-constructible if there is a $O(T(n))$ time-bounded Turing machine computing the function $1^n\mapsto 1^{T(n)}$. $S$ is space-constructible if there is a $O(S(n))$ time-bounded Turing machine computing the function $1^n\mapsto 1^{S(n)}$.

# Universal Turing Machines

Universal Turing machine has a fixed amount of work tapes and a fixed tape alphabet.

Set of states $Q$ is identified with positive integers from $1$ to $|Q|$.

$\Sigma$ and $\Gamma$ is also identified with positive integers.

$\delta$ can be encoded by its function table.

Given: encoding of a Turing machine $M$ with $k$ work tapes with an encoding of an input $x$ to $M$. (encoding of $x$ is concatenation of each char in $x$).

:dart:Theorem 1. For any $k\ge1$, there exist a Turing machine $U_k$ with $k+1$ work tapes s.t. when given as input an encoding of a Turing machine $M$ with $k$ work tapes together with an encoding of an input $x$, simulates the computation of $M$ on input $x$ using time $O(time_M(x))$ and space $O(space_M(x))$.

"Simulates" here means $U_{k+1}$ provides same output, terminal state as $M$.

# Sublogarithmic Space

Usually assume that $S(n)\ge\log n$.

What if $S(n)=o(\log n)$? In this case Turing machine can't even be able to describe the position of the input tape head.

$O(1)$ space-bounded Turing machine is equivalent to a two-way finite automaton.

To accept non-regular language, the minimal space should be $O(\log\log n)$.

**Storage configuration**: a state of $M$, position of tape heads, non-black portions of the work tapes. # storage configuration: at most $|Q|s^k|\Gamma|^{s+k-1}=2^{O(s)}$.

:dart:Theorem 2. Let $M$ be a $S(n)$ space-bounded Turing machine for a function $S(n)=o(\log\log n)$. Then $M$ is in fact $O(1)$ space-bounded.

> Proof via contradiction.
>
> Storage configuration is periodic.
>
> $M$ will produce at most $2^{2^{O(s)}}$ crossing sequences.

# Turing Machine Configurations

~~Configuration 被翻译为“格局”……无力吐槽~~

A configuration of a Turing machine $M$ with $k$ work tapes:

1. State of $M$
2. Positions of $k+1$ tapes head
3. Non-blank portions of the work tapes

Observation: If a Turing machine repeats a configuration, it will enter an infinite loop, infinitely repeating the computation taking place between two repeating configurations.

# The Space Hierarchy Theorem

Idea: More resources means more can be computed.

:dart:Theorem 3: Let $S_2(n)\ge\log n$ be space constructible. If $S_1(n)=o(S_2(n))$, then
$$
DSPACE(S_1(n))\subsetneq DSPACE(S_2(n)).
$$

!!! info

    中文简单解释一下：图灵机的可用渐进存储空间越多，那么解决的问题也就越多。



> It suffices to construct a $O(S_2(n))$ space bounded Turing machine $M$ s.t. $L(M)\not\in DSPACE(S_1(n))$.
>
> Input $x$ of length $n=|x|$:
>
> 1. Mark $S_2(n)$ cells of work tape using the space constructability of $S_2$. During the remaining computation:
>    - Ensuring that computation does not use more than $S_2(n)$ additional cells of work tape. This is done by immediately halting (reject).
>    - Ensuring that computation does not run for more than $2^{2S_2(n)}$ steps, by keeping time with a binary counter on $2S_2(n)$ bits. (accept)
> 2. Check if the input is on the form $x=0^i1w$, here $w$ is an encoding of a Turing machine with a single work tape. Otherwise halt by rejecting.
> 3. Simulate $M'$ on input $x$ using the universal Turing machine $U_1$. If this causes $M'$ to halt, halt and reject if $M'$ accepts. Otherwise accept.
>
> Then analysis $M$ and $M'$......

# Tape Reduction and Oblivious Turing Machines

A $T(n)$ time-bounded computation of a Turing machine with **any number** of work tapes can be simulated by a $O(T(n)^2)$ time-bounded Turing machine with a **single** work tape. (Simple divide work tape into $k$ tracks.)

This is used to prove $DTIME(T_1(n))\subsetneq DTIME(T_2(n))$ when $T_2(n)>n$ is time-constructible and $(T_1(n))^2=o(T_2(n))$.

:book:Definition of **oblivious**: Let $M$ be a Turing Machine. $M$ is oblivious if for any $n\ge1$ and any input $x$ of length $n=|x|$, during computation with input $x$ and until $M$ halts, the location of all tape heads of $M$ is a function of $n$ and the number $t$ of steps of the computation so far.

It is easy to modify the $O(T(n)^2)$ to be oblivious by first copying the input to an additional track, and thereafter keeping the input head stationary.

:dart:Theorem 4: Let $T(n)\ge n$ and suppose $M$ is a $T(n)$ time-bounded Turing machine with $k$ work tapes. Then there exist a $O(T(n)\log T(n))$ time-bounded oblivious Turing machine $M'$ with $2$ work tapes s.t. $L(M)=L(M')$.

> Hard proof.

# The Time Hierarchy Theorem

:dart:Theorem 5: Let $T_2(n)\ge n$ be time constructible. If $T_1(n)\ge n$ satisfies $T_1(n)\log T_1(n)=o(T_2(n))$, then
$$
DTIME(T_1(n))\subsetneq DTIME(T_2(n)).
$$

> Proof by tape reduction.

# Time and Space Complexity Classes

There are some standard complexity classes given by natural classes of time and space bounds.
$$
L=DSPACE(\log n)\\
P=\bigcup_{k>0}DTIME(n^k)\\
PSPACE=\bigcup_{k>0}DSPACE(n^k)\\
EXP=\bigcup_{k>0}DTIME(2^{n^k})
$$
Trivially, $L\subseteq P\subseteq PSPACE\subseteq EXP$.

A space bounded Turing machine cannot repeat a configuration without entering an infinite loop, thus $DSPACE(S(n))\subseteq DTIME(2^{O(S(n))})$ for $S(n)>\log n$.

**Unknown: whether any of the inclusions are strict.** But time and space hierarchy theorems give that $L\subsetneq PSPACE$ and $P\subsetneq EXP$.

$L$ means $LOGSPACE$, $P$ denotes $PTIME$, $EXP$ denotes $EXPTIME$.

Obviously(???really???), $DTIME(T(n))\subseteq DSPACE(T(n))$, showing that space is more "valuable than" time. More precisely, $DTIME(T(n))\subseteq DSPACE(T(n)/\log T(n))$ for $T(n)\ge n$.

# The Padding Technique

Whether $L\subseteq P$ and $PSPACE\subseteq EXP$ are strict is related.

:dart:Theorem 6: If $L=P$, then $PSPACE=EXP$.