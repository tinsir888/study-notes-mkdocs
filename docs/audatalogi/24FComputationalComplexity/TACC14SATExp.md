---
title: 算法和计算复杂度 14 SAT Problem
author: tinsir888
date: 2024/11/10
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 58f23662
---

Boolean satisfiability is prototypical $NP$-complete problem.

There is no algorithm deciding $SAT$ in worst case in polynomial time unless $P=NP$.

We always assume that $P\neq NP$.

For $SAT$ problem, SOTA algorithm uses time $2^{(1-\frac{1}{O(\log(m/n))})n}m^{O(1)}$.

For special case of $kSAT$, there are faster algorithms, but all take time $2^{(1-\frac{c}{k})n}$ for some constant $0\lt c\lt k$.

# Schöning's kSAT Algorithm

该算法在上学期随机算法课最后一节课 [马尔可夫链和随机游走](https://tinsir888.github.io/posts/34c1542e.html) 中讲过，故此处略过。

分析过程很恶心。

# The Sparsification Lemma

:dart:Lemma 22. For all $\epsilon\gt0$ and integers $k$, there exists a constant $C=C(\epsilon,k)$ and an algorithm that given as input a $kCNF$ $\Phi$ in $n$ variables runs in time $tn^{O(1)}$, where $t\le2^{\epsilon n}$, and give as output a list of $kCNF$ formulas $\Psi_1,\cdots,\Psi_t$ each with at most $Cn$ clauses such that

$$
\Phi(x)=\bigvee_{i=1}^t\Psi_i(x),
$$

for all $x\in\{0,1\}^n$.

## Reduce to Hitting Set Problem

Recall $HittingSet$ problem. Consider a universe $U$ of elements and a collection $\mathcal S$ of subsets of $\mathcal U$. We say that $T\subseteq\mathcal U$ is a hitting set for $\mathcal S$ if $S\cap T\neq\emptyset$ for all $S\in\mathcal S$. We denote by $\sigma(\mathcal S)$ the collection of all hitting sets of $\mathcal S$.

$kHittingSet$ problem:

- Instance: Collection $\mathcal S$ of subsets of a universe $\mathcal U=2^U$.
- Output: Hitting set $T$ for $\mathcal S$ minimizing $|T|$.

We denote by $kHittingSet$ the restriction to collections of subsets of size at most $k$.

We can transform $kSAT$ into $kHittingSet$ problem:

- $\mathcal C$ is the subsets of $\mathcal U$ corresponding to the clauses of $\Phi$.
- An assignment to the variables of $\Phi$ can be viewed as a subset of $\mathcal U$.
- $\mathcal A$ is the collection of the $n$ sets $\{x_i,\overline{x_i}\}$ for $i=1,\cdots,n$. Note that assignments to the variables of $\Phi$ are in one-to-one correspondence to hitting sets of $\mathcal A$.
- Defining $\mathcal S_\Phi=\mathcal C\cup\mathcal A$ 

Observation: There is a one-to-one correspondence between the set of satisfying assignments of $\Phi$ and hitting sets of $\mathcal S_\Phi$ of size $n$.

Any collection of subsets $\mathcal T$ over $\mathcal U$ can be viewed as a CNF formula by letting each subset define a clause.

:book:Definition 57. Let $\mathcal T$ and $\mathcal S$ be collections of subsets. We say that $\mathcal T$ is a **restriction** of $\mathcal S$ if for every $S\in\mathcal S$ there exists $T\in\mathcal T$ s.t. $T\subseteq S$.

!!! info

    也就是集合的集合 $\mathcal S$ 中的每一个元素（是一个集合）是集合的集合 $\mathcal T$ 中对应有一个元素（是一个集合）的子集。



~~很绕，不是吗？~~

Clearly, if $\mathcal T$ is a restriction of $\mathcal S$, then $\sigma(\mathcal T)\subseteq\sigma(\mathcal S)$. Any satisfying assignment of CNF formula given by $\mathcal T$ is also a satisfying assignment of the CNF formula given by $\mathcal S$.

:thinking:Lemma 23 (Hitting set correspondence). For all $\epsilon\gt0$ and integers $k$, there exists a constant $C=C(\epsilon,k)$ and an algorithm that given as input collection $\mathcal S$ of subsets of size at most $k$ of a universe $\mathcal U$ of size $n$ runs in time $tn^{O(1)}$, where $t\le 2^{\epsilon n}$, and give as output a list of restrictions $\mathcal T_1,\cdots,\mathcal T_t$ each with at most $Cn$ subsets of $\mathcal U$ which are all of size at most $k$ s.t.

$$
\sigma(\mathcal S)=\bigcup_{i=1}^t\sigma(\mathcal T_i).
$$

We call a subset of $\mathcal U$ of size $j$: $j$-set.

The algorithm is a branching algorithm where each leaf of the recursion tree will give a restriction of $\mathcal S$.

To branch, the algorithm looks for a large collection of subsets of the **same size** that has a non-empty intersection, and branches on whether or not the common intersection will be hit by the hitting set. We define such collections of sets as **flowers**.

:book:Definition 58. Let $S_1,\cdots,S_c$ be $j$-sets.

- We say that these sets are a **flower** if $H=\bigcap_{i=1}^cS_i\neq\emptyset$.
- We call $H$ the **heart** of the flower and sets $(S_1\diagdown H),\cdots,(S_c\diagdown H)$ the **petals**.
- Note that all petals have the same size $j-|H|$, which we denote as **petal-size**.

!!! info

    定义一朵花：一组大小为 $j$ 的集合，它们的交集为空。

    花心：这组集合的交集。对应定义花心的大小。

    花瓣：每个集合去除花心。对应定义花瓣的大小。



The algorithm will be controlled by integer parameters $2=\theta_0\lt\theta_1\lt\cdots\lt\theta_{k-1}$. We make $\theta_i$ increase rapidly as a parameter of $i$.

:book:Definition 59. Let $S_1,\cdots,S_c$ be a flower of $j$-sets and petal-size $i$.

- The flower is **large** if $c\ge\theta_i$.
- The flower is **good** if
  - it is a large flower chosen s.t. $j$ is chosen minimally
  - and if more than one large flower of $j$-sets exists then chosen s.t. $i$ is chosen minimally.

!!! info

    如果一朵花含有集合的个数足够大，则称这朵花是“大”的。

    如果一朵花是“大”的，并且其中包含的集合大小、花瓣大小都是最小的，则称这朵花是“好”的。



~~很形象的定义，但还是很绕~~

The algorithm will eliminate sets of a collection $\mathcal T$ that contain other sets of the collection. If $S,T\in\mathcal T$ are such that $S\subsetneq T$, we say that $T$ is **eliminated** by $S$.

Let $\pi(\mathcal T)$ denote the collection of minimal sets of $\mathcal T$, i.e., the sets of $\mathcal T$ that are not eliminated by sets of $\mathcal T$.

## Pseudo code of Reduce Algorithm

`Reduce`$(\mathcal S)$

Input: Collection $\mathcal S$ of subsets of size at most $k$ of a universe $\mathcal U$.

Output: Restrictions $\mathcal T_1,\cdots,\mathcal T_t$ of s.t. $\sigma(\mathcal S)=\bigcup_{i=1}^t\sigma(\mathcal T_i)$.

1. $\mathcal S\gets\pi(\mathcal S)$.
2. If $\mathcal S$ has no large flower, return $\{\mathcal S\}$
3. Pick good flower $S_1,\cdots,S_c$ with heart $H=\bigcap_{i=1}^cS_i$.
4. return `Reduce`$(S\cup\{H\})\bigcup$`Reduce`$(\mathcal S\cup\{S_1\diagdown H,\cdots,S_c\diagdown H\})$.

## Correctness of Reduce

:thinking:Lemma 24. If `Reduce`$(\mathcal S)=\{\mathcal T_1,\cdots,\mathcal T_t\}$ then $\sigma(\mathcal S)=\bigcup_{i=1}^t\sigma(\mathcal T_i)$.

> Each $\mathcal T_i$ is a restriction of $\mathcal S$. Thus $\sigma(\mathcal T_i)\subseteq\sigma(\mathcal S)\forall i$.
>
> Note that $\sigma(\pi(\mathcal T))=\sigma(\mathcal T)$ for any collection $\mathcal T$. It follows that eliminating sets from $\mathcal S$ does not eliminate any hitting sets of $\mathcal S$.
>
> Suppose that $T$ is a hitting set of $\mathcal S$ and let $S_1,\cdots, S_c$ be a flower of $\mathcal S$ with heart $H$. If $T\cap H\neq\emptyset$, then $T$ is a hitting set of $S\cup\{H\}$. If $T\cap H=\emptyset$ then $T$ must be a hitting set of $\mathcal S\cup\{S_1\diagdown H,\cdots,S_c\diagdown H\}$. Thus any hitting set of $\mathcal S$ is preserved in at least one recursive branch.

It's easy to see that `Reduce` always terminates.

It remains to establish qualitative properties of `Reduce`: Every restriction output by `Reduce` is **sparse**, i.e., contains at most $Cn$ sets, for a constant $C=C(k,\epsilon)$.

:thinking:Lemma 25. Let $C=\sum_{i=1}^k(\theta_{i-1}-1)$. Every restriction $\mathcal T$ output by `Reduce` contains at most $(\theta_{i-1}-1)n$ many $i$-sets, and therefore at most $Cn$ sets in total.

> Prove by contradiction. Use pigeonhole principle.

The number of restriction output by `Reduce` should be bounded by the number of leaves $t$ in the recursion tree.

:thinking:Lemma 26. Let $0\lt m\lt n$ be integers. It holds that

$$
\sum_{i=1}^m{n\choose i}\le2^{H(\frac{m}{n})\cdot n}.\\
$$

$$
H(p)=p\log_2(1/p)+(1-p)\log_2(1/(1-p))
$$

:thinking:Corollary 14. A binary tree of depth $h$ in which every root-to-leaf path follows the right child at most $\gamma h$ times has at most $2^{H(\gamma)h}$ leafs.

> Proof by Lemma 26.

When $\gamma\le1/2$ we have $H(\gamma)\le2\gamma\log_2(1/\gamma)$, thus we use the simpler upper bound $2^{(2\gamma\log_2(1/\gamma))h}$ on the number of leaves.

:thinking:Lemma 27. If `Reduce`$(\mathcal T)$ recursively calls `Reduce`$(\mathcal T')$ by adding an $i$-set to $\mathcal T$, then $\mathcal I_i(\mathcal T')$ holds. Furthermore in all recursive calls `Reduce`$(\mathcal T'')$ that follows, the invariant $\mathcal I_i(\mathcal T'')$ holds.

~~没咋看懂这个引理~~

:thinking:Lemma 28. Any set $S$ can eliminate at most $2(\theta_{i-1}-1)$ of the added $i$-sets.

:thinking:Lemma 29. Let $1\lt i\lt k$ and suppose $\beta_{i-1}$ is s.t. at most $\beta_{i-1}n$ sets of size at most $i-1$ that are added along a given path of the recursion tree of `Reduce`. Then the number of $i$-sets added along that path is at most $(2\beta_{i-1}+1)(\theta_{i-1}-1)n$.

:thinking:Corollary 15. At most $\beta_in$ sets of size at most $i$ are added along any path of the recursion tree of `Reduce`, for all $1\le i\lt k$.

:thinking:Lemma 30. Along any path of the recursion tree of `Reduce` at most $\beta_in/\theta_i$ families are created by adding petals of size $i$ for every $1\le i\lt k$.

We set $\theta_i$ in such a way that the ratio $\theta_i/\beta_i$ is equal to an integer constant $\alpha$, to be fixed later, for $1\le i\le k$. Thus

$$
\beta_i=4\beta_{i-1}\theta_{i-1}=4\alpha(\beta_{i-1})^2\\
$$

$$
\Rightarrow \beta_i=(4\alpha)^{2^{i-1}-1},\theta_i=\alpha(4\alpha)^{2^{i-1}-1}\forall 1\le i\le k
$$

:thinking:Corollary 16. Along any path of the recursion tree of `Reduce` at most $kn/\alpha$ families are created by adding petals.

~~让我缓缓~~

# The Exponential Time Hypothesis

A stronger hypothesis than $P\neq NP$: Exponential time is required to decide $SAT$.

**Exponential time hypothesis** (aka ETH): $kSAT$ requires worst-case linear exponential time, for all $k\ge3$.

A strengthening of this hypothesis, **strong exponential time hypothesis**: it is not possible to save an exponential factor $2^{\epsilon n}$ for a fixed $1\gt\epsilon\gt0$ independent of $k$

:book:Definition 60. Define $s_k=\inf\{\delta|kSAT\text{ is solvable in time }O(2^{\delta n}\}$. The ETH is the statement that $\forall k\ge3:s_k\gt0$, and SETH is the statement that $s_{\infty}=\lim_{k\to\infty}s_k=1$.

:thinking:Proposition 20. For any $\epsilon\gt0$ and integer $k$ there is $C\gt0$ s.t. if there is an algorithm $A$ for solving $3SAT$ in time $2^{\delta n}n^{O(1)}$ there is an algorithm $B$ for $kSAT$ running in time $O(2^{(\epsilon+\delta kC)n}n^{O(1)})$.

> Input: $kCNF:\Phi$ on $n$ variables. the algorithm $B$ first invokes the algorithm of the Lemma 22 to compute $kCNF$ formulas $\Psi_1,\cdots,\Psi_t$ in time $tn^{O(1)}$, where $t\le2^{\epsilon n}$, and each $\Psi_i$ has hat most $Cn$ clauses.
>
> $\Phi$ is satisfiable iff at least one of the formula $\Psi_i$ are satisfiable.
>
> $B$ may apply the standard reduction from $kSAT$ to $3SAT$, obtaining $3CNF$ formula $\Psi_1',\cdots,\Psi_t'$, each having at most $n+Cn(k-3)\le kCn$ variables.
>
> Using algorithm $A$ each $\Psi_i'$ can be checked for satisfiability in time $2^{\delta(kCn)n^{O(1)}}$. This together means that $B$ solves $kSAT$ in time as claimed.

:thinking:Corollary 17. The following holds:

1. ETH is equivalent to $s_3\gt0$.
2. SETH implies ETH.

:thinking:Proposition 21. ETH is equivalent to the existence of $\delta\gt0$ s.t. no algorithm can solve $3SAT$ in time $2^{\delta(n+m)}n^{O(1)}$, where $n$ is the number of variables and $m$ the number of clauses.

Consider the independent set problem:

- Instance: Graph $G=(V,E)$, integer $K$.
- Question: Does $G$ have an independent set of size at least $K$?

:dart:Theorem 60. If ETH holds, then there exists $\delta\gt0$ s.t. no algorithm can solve the independent set problem in time $2^{\delta n}n^{O(1)}$, where $n$ is the number of vertices of the given graph $G$.

> Standard reduction from $3SAT$ to independent set problem.