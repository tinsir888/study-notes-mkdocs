---
title: 算法和计算复杂度 2 Nondeterminism and Determinism
author: tinsir888
date: 2024/7/2
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: c9e8cfc2
---

The sequence of transitions is called *computation history*.

Non-deterministic Turing machine will crush also when there is no possible next step according to the transition relation.

# Non-deterministic Time and Space Complexity

Worst-case definition of time and space to solve a issue by non-deterministic Turing machine.

Let $M$ be a non-deterministic Turing machine. For given input $x\in\Sigma^*$,

- $time_M(x)$ is the **maximum number of computation steps** that $M$ performs on input $x$ until halting.
- $space_M(x)$ is the **maximum number of cell** on work tapes that are some point scanned by a tape head during computation.

:book:Definition of *non-deterministic* complexity classes:

> Let $T:\mathbb N\rightarrow\mathbb N$ and $S:\mathbb N\rightarrow N$ be functions with $T(n)\ge n$.
>
> $NTIME(T(n))$ is the class of languages computed by a $O(T(n))$ time-bounded non-deterministic Turing machine.
>
> $NSPACE(T(n))$ is the class of languages computed by a $O(S(n))$ space-bounded non-deterministic Turing machine.

# Non-deterministic Hierarchy Theorems

:dart:Theorem 7 (Non-deterministic Space Hierarchy Theorem). Let $S_2(n)\ge\log n$ be space constructible. If $S_1(n)=o(S_2(n))$, then

$$
NSPACE(S_1(n))\subsetneq NSPACE(S_2(n)).
$$

!!! info

   和 [上一课](https://tinsir888.github.io/posts/7f7103e4.html) 的定理 3 一样。存储空间越多，“识别的语言”也就越多。



~~Proof by  Immerman–Szelepcsényi theorem.~~

For non-deterministic time: rely on technique "delayed diagonalization". 延迟对角化

:dart:Theorem 8 (Time-bounded non-deterministic Turing machine). Let $T(n)\ge n$ and suppose $M$ is a $T(n)$ time-bounded non-deterministic Turing machine with $k$ work tapes. Then there exists a $O(T(n))$ time-bounded non-deterministic Turing machine with $2$ work tapes s.t. $L(M)=L(M')$.

:dart:Theorem 9 (Non-deterministic Time Hierarchy Theorem). Let $T_2(n)\ge n$ be time constructible and non-decreasing. If $T_1(n)$ satisfies $T_1(n+1)=o(T_2(n))$, then

$$
NTIME(T_1(n))\subsetneq NTIME(T_2(n))
$$

!!! info

    花的时间越长，解决问题就越多。



Proof by constructing $M$ s.t. $L(M)\not\in NTIME(T_1(n))$.

# Relating Deterministic and Non-deterministic Computation

:dart:Theorem 10. Let $T(n)\ge n$. Then $NTIME(T(n))\subseteq DSPACE(T(n))$.

:dart:Theorem 11. Let $S(n)\ge\log n$. Then $NSPACE(S(n))\subseteq DTIME(2^{O(S(n))})$.

# Savitch's Theorem

Better than $NSPACE(S(n))\subseteq DSPACE(2^{O(S(n))})$.

:dart:Theorem 12 (Savitch's Theorem). Let $S(n)\ge\log n$. Then
$$
NSPACE(S(n))\subseteq DSPACE(S(n)^2).
$$
Hard proof

# The Immerman–Szelepcsényi Theorem

Non-deterministic space is closed under the operation of taking complements.

$coNSPACE(S(n))$: The class of complement languages of languages in $NSPACE(S(n))$.

:dart:Theorem 13 (Immerman–Szelepcsényi theorem). Let $S(n)\ge\log n$. Then
$$
coNSPACE(S(n))=NSPACE(S(n)).
$$

# Non-deterministic Time and Space Complexity Classes

$$
NL=NSPACE(\log n)\\
NP=\bigcup_{k\gt0}NTIME(n^k)\\
NEXP=\bigcup_{k\gt0}NTIME(2^{n^k})
$$

Relations:

- Due to Savitch's theorem, there is no need for defining $NSPACE$.

- $NL\subseteq NP\subseteq PSPACE\subseteq NEXP$.
- By non-deterministic time and space hierarchy theorems: $NL\subsetneq PSPACE$, $NP\subsetneq NEXP$.

Thus, $L\subseteq NL\subseteq P\subseteq NP\subseteq PSPACE\subseteq EXP\subseteq NEXP$.

## Summary of Complexity Classes

1. **L (Logarithmic Space)**
   - **Definition**: $L$ consists of all decision problems that can be solved by a deterministic Turing machine using $O(\log n)$ space.
   - **Example**: Determining if a path exists between two nodes in an undirected graph using depth-first search with a space-efficient representation.
2. **NL (Non-deterministic Logarithmic Space)**
   - **Definition**: $NL$ consists of all decision problems that can be solved by a non-deterministic Turing machine using $O(\log n)$ space.
   - **Example**: Determining if a path exists between two nodes in a directed graph (also known as the reachability problem).
3. **P (Polynomial Time)**
   - **Definition**: $P$ consists of all decision problems that can be solved by a deterministic Turing machine in polynomial time, $O(n^k)$ for some constant $k$.
   - **Example**: Sorting an array using algorithms like Merge Sort or Quick Sort.
4. **NP (Non-deterministic Polynomial Time)**
   - **Definition**: $NP$ consists of all decision problems for which a given solution can be verified in polynomial time by a deterministic Turing machine. Equivalently, problems solvable by a non-deterministic Turing machine in polynomial time.
   - **Example**: The Boolean satisfiability problem (SAT).
5. **PSPACE (Polynomial Space)**
   - **Definition**: $PSPACE$ consists of all decision problems that can be solved by a deterministic Turing machine using polynomial space.
   - **Example**: Generalized games like chess, checkers, and go with a polynomial bound on the number of moves.
6. **EXP (Exponential Time)**
   - **Definition**: $EXP$ consists of all decision problems that can be solved by a deterministic Turing machine in exponential time, $O(2^{p(n)})$ for some polynomial $p(n)$.
   - **Example**: Solving the traveling salesman problem using a brute-force search over all possible paths.
7. **NEXP (Non-deterministic Exponential Time)**
   - **Definition**: $NEXP$ consists of all decision problems solvable by a non-deterministic Turing machine in exponential time, $O(2^{p(n)})$ for some polynomial $p(n)$.
   - **Example**: Certain advanced combinatorial problems that require guessing an exponentially long solution and verifying it in exponential time.

We don't know whether these inclusions are strict. But we usually assume that all the inclusions are strict.

$P=NP$ implies $EXP=NEXP$; $EXP\neq NEXP$ implies $P\neq NP$.