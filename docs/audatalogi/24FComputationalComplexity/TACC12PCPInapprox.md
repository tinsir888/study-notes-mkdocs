---
title: 算法和计算复杂度 12 PCP-based Inapproximability
author: tinsir888
date: 2024/11/1
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 375beefd
---

For $MAXCLIQUE$ problem. A constant factor **polynomial-time** approximation algorithm doesn't exist unless $NP\in DTIME(2^{(\log n)^{O(1)}})$, based on the theorem that $NP\subseteq PCP((\log n)^{O(1)},(\log n)^{O(1)})$.

Use a different approach the final $PCP$ theorem implies that a constant factor polynomial-time approximation algorithm for $MAXE3SAT$ doesn't exist unless $P=NP$.

Optimization is hard $\Rightarrow$ A corresponding **gap** version of decision problem is hard.

:book:Definition 53. An $NP$-optimization problem $\Pi$ is given by a polynomial $p$, a language $L\in P$, a polynomial-time computable function $val$ called **objective function**, and a choice of being either a maximization problem or a minimization problem. For a given $x$, define the set $F(x)$ of **feasible** solution by
$$
F(x)=\{y:|y|\le p(|x|)\land\langle x,y\rangle\in L\}.
$$
Assume that $F(x)\neq\emptyset$ for any $x$. The function $val$ takes as input $x$ and $y\in F(x)$ and assigns a value $val(x,y)\ge0$ to the feasible solution $y$ to instance $x$.

Denote $val_x(y)$ for $val(x,y)$.

- If $\Pi$ is a maximization problem, $OPT(x)=\max_{y\in F(x)}val_x(y)$.
- If $\Pi$ is a minimization problem, $OPT(x)=min_{y\in F(x)}val_x(y)$.

A feasible solution $y\in F(x)$ s.t. $val_x(y)=OPT(x)$ is called an **optimal solution** for $x$.



**"Gap-version" reduction**: The corresponding decision problem is that of asking whether $OPT(x)\ge K$ in case of a maximization problem or whether $OPT(x)\le K$ in case of a minimization problem.

When this is $NP$-hard, it follows that there is no polynomial-time algorithm that on input $x$ computes an optimal solution for $x$ unless $P=NP$.

:book:Definition 54. Let $\Pi$ be an $NP$-optimization problem and let $\rho:\mathbb N\to\R_+$ be a function. A $\rho$-approximation algorithm $A$ is an algorithm that on input $x$ gives as output $y\in F(x)$ s.t. $val_x(y)\ge\rho(|x|)OPT(x)$ in case of a maximization problem $\Pi$ or such that $val_x(y)\le\rho(x)OPT(x)$ in case of a minimization problem $\Pi$.

Let $\Pi$ be an $NP$-maximization problem and let $a,b:\mathbb N\to\R_+$ be functions s.t. $0\lt a(n)\lt b(n)$ for all $n$. Then define the following promise decision problem.

$(a,b)-GAP-\Pi$:

- Input: $x$ s.t. either $OPT(x)\le a(|x|)$ or $OPT(x)\ge b(|x|)$.
- Question: Is $OPT(x)\ge b(|x|)$.

:book:Definition 55. We say that $(a,b)-GAP-\Pi$ is $NP$-hard if there is a polynomial-time computable function $f$ s.t.

1. If $x\in SAT$ then $f(x)$ is an instance of $\Pi$ and $OPT(f(x))\ge b(|f(x)|)$.
2. If $x\not\in SAT$ then $f(x)$ is an instance of $\Pi$ and $OPT(f(x))\le a(f(|x|))$.

:thinking:Proposition 19. Suppose that $(a,b)-GAP-\Pi$ is $NP$-hard. Then there is no polynomial-time $\rho$-approximation for $\Pi$ s.t. $\rho(n)\gt\frac{a(n)}{b(n)}$ unless $P=NP$.

## The FGLSS reduction

~~这真的是人能想出来的规约么~~

Let $L\in PCP_{c,s}(r(n),q(n))$ and let $V$ be a corresponding $PCP$ verifier.

WLOG, $V$ makes $q(n)$ queries to the proof on input $x$ of length $n$.

For an input $x$, we define a graph $G(x)$ from $V$ calledd the $FGLSS$ graph. The set of vertices $G(x)$ are all pairs $(y,z)\in\{0,1\}^{r(n)}\times\{0,1\}^{q(n)}$.

A pair of distinct vertices $(y_1,z_1)$ and $(y_2,z_2)$ are connected by an edge **iff there exist a proof $\pi$ s.t. the following holds**:

1. Using $y_1$ in place of the random bits $V^\pi(x)$ accepts and the positions read from the proof $\pi$ contain exactly the contents of $z_1$.
2. Using $y_2$ in place of the random bits $V^\pi(x)$ accepts and the positions read from the proof $\pi$ contain exactly the contents of $z_2$.

:eyes:For a fixed input $x$, the positions read from the proof are determined just by the string $y\in\{0,1\}^{r(n)}$ of random bits.

Thus in the graph $G(x)$ there are no edges between $(y,z_1)$ and $(y,z_2)$ for $z_1\neq z_2$.

A **clique** in $G(x)$ must consist of vertices $(y_1,z_1),\cdots,(y_k,z_k)$ for which all the strings $y_1,\cdots,y_k$ are distinct.

Now define $MAXCLIQUE$

!!! info

    Clique，中文翻译为“完全子图”。最大完全子图就是要找包含点数最多的完全子图。这个问题是 NP-complete 的。



- Instance: A graph $G$.
- Output: Clique in $G$ of maximum size.

:thinking:Lemma 20. Let $V$ be a $PCP$ verifier using $r(n)$ random bits and querying $q(n)$ symbols from a proof $\pi\in\{0,1\}^*$. For an input $x$ we have

$$
OPT(G(x))=2^{r(n)}\max_\pi\Pr[V^\pi(x)=yes].
$$

:thinking:Corollary 13. Let $L\in PCP_{c,s}(r(n),q(n))$ and let $V$ be a corresponding PCP verifier. Then using time $2^{O(r(n)+q(n))}n^{O(1)}$ on input $x$ of length $n$ a graph $G(x)$ with $2^{r(n)+q(n)}$ vertices can be computed s.t.

- If $x\in L$ then $OPT(G(x))\ge c2^{r(n)}$.
- If $x\not\in L$ then $OPT(G(x))\le s2^{r(n)}$.

Since $NP\in PCP(O(\log n),O(1))$, there is a $\delta\gt0$ s.t. $(\frac{1}{2}\delta n,\delta n)-GAP-MAXCLIQUE$ that for $MAXCLIQUE$ there is no $(\frac{1}{2}+\epsilon)$-approximation algorithm, for any $\epsilon\gt0$, unless $P=NP$.

:dart:Theorem 54. For any constant $\rho\gt0$, there is no polynomial time $\rho$-approximation algorithm for $MAXCLIQUE$ unless $P=NP$.

Actually, it is possible to do success amplification in a randomness efficient manner using random walks on expander graphs. This leads to $NP\subseteq PCP_{1,1/n}(O(\log n),O(\log n))$ which leads to a stronger result.

:dart:Theorem 55. There exist a constant $\epsilon\gt0$ s.t. there is no $\frac{1}{n^\epsilon}$-approximation algorithm for $MAXCLIQUE$ unless $P=NP$.

By using a weaker version of theorem 52 that computing $FGLSS$ takes $2^{(\log n)^{O(1)}}$ time, we have a weaker version of theorem 54.

:dart:Theorem 56 (weaker than 54). For any constant $\rho\gt0$, there is no $\rho$-approximation algorithm for $MAXCLIQUE$ running in time $2^{(\log n)^{O(1)}}$ unless $NP\neq\subseteq DTIME(2^{(\log n)^{O(1)}})$.

:dart:Theorem 57. For any constant $\epsilon\gt0$ there is no polynomial time $\frac{1}{n^{1-\epsilon}}$-approximation algorithm for $MAXCLIQUE$ unless $P=NP$.

## Constraint Satisfaction Problem

:book:Definition 56. A constraint satisfaction problem (CSP) is given by a finite set $\Sigma$, an integer $q$ and a list of functions $\Phi=(\varphi_1,\varphi_2,\cdots,\varphi_m)$ where $\varphi_i$ are called constraints, $q$ the arity, and $\Sigma$ the alphabet. An input $x\in\Sigma^n$ satisfies the constraint $\varphi_i$ if $\varphi_i(x)=1$. Define
$$
val(\Phi)=\frac{1}{m}\max_x|\{i\in\{1,\cdots,m\}|\varphi_i(x)=1\}|.
$$
Let $MAX_qCSP_\Sigma$ be the optimization problem where the input is any CSP with $q$-ary constraints over an alphabet $\Sigma$.

When $\Sigma=\{0,1\}$, we called it $MAX_qCSP$.

$MAXE3SAT$ problem can be phrased as $CSP$ with 3-ary constraint over the binary alphabet.

$MAXE3SAT$:

- Instance: CNF formula $\varphi$ where each clause contains 3 distinct variables.
- Output: Assignment $x$ maximizing the fraction of satisfied clauses.

The existence of an $\epsilon\lt1$ such that $(\epsilon,1)-GAP-MAXE3SAT$ is $NP$-hard.

:dart:Theorem 58. For fixed $s\lt c$ we have that $NP\subseteq PCP_{c,s}^\Sigma(O(\log n),q)$ iff $(s,c)-GAP-MAX_qCSP_\Sigma$ is $NP$-hard.

:dart:Theorem 59. There exist $\epsilon\lt1$ s.t. $(\epsilon,1)-GAP-MAXE3SAT$ is $NP$-hard.