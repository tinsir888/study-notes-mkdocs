---
title: Fair Division 5 Is EF1 Compatible with Pareto-optimality? Introduction to EFX.
author: tinsir888
date: 2024/3/15
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: '28335034'
---

Objective: Examine the compatibility of EF1 with Pareto-optimality, introducing the concept of EFX.

Activities: Read and present the paper [*The Unreasonable Fairness of Maximum Nash Welfare*](https://dl.acm.org/doi/10.1145/3355902). Ioannis et al. ACM Transactions on Economics and Computation 2019

*It's worth to mention that Iannis received [Kalai Prize 2024](https://gametheorysociety.org/kalai-prize-2024/) for this paper* :fireworks:

> Hint from Iannis: Ignore appendix, experiments. Focus on the proof, paraphrase it in my own words, a easy way! 



**Maximum Nash welfare** (abbr. MNW) selects an allocation that maximize the **product** of utilities.

Not only for divisible goods, MNW is also fair in indivisible setting.

MNW selects allocation that are EF1! It also compatible with other fairness like MMS.

# Introduction

MNW exhibits an elusive combination of **fairness** and **efficiency**.

## Main Results

MNW solution always outputs an allocation that is EF1 as well as Pareto optimal.

> EF1 is easy, but EF1 + PO is hard.

MNW solution always guarantees $\frac{2}{1+\sqrt{4n-3}}$-MMS. (the approximation is tight!)

MNW solution guarantees $\frac{\sqrt5-1}{2}$-PMMS. (the approximation is also tight!)

Computing MNW allocation is NP-hard for unscaled valuations. Solution: scaled valuations.

## Related Work

EF1: Envy-cycle elimination, Round-Robin

Approximate CEEI

Maximizing Nash welfare with divisible goods: Envy-free, Pareto optimal.

MNW is NP-hard. Some approximation MNW in polynomial time. MNW is APX-hard: a constant-factor approximation is the best one can hope to achieve in polynomial time.

# Model

$\mathcal N=[n]$ players.

$\mathcal M=[m]$ indivisible goods.

Each player has a valuation function $v_i$ for the goods.

- $v_i(\emptyset)=0$
- additive (main results can be generalized to submodular valuations)

$\Pi_k(S)$ denote the set of ordered partitions of $S$ into $k$ bundles. $S\subseteq \mathcal M$.

A feasible allocation $\mathbf A=(A_1,\cdots,A_n)\in\Pi_n(\mathcal M)$ is a partition of the goods that assigns a subset $A_i$ to player $i$. Thus the utility to $i$ is $v_i(A_i)$.

Definition of Envy Freeness (EF), EF1, MMS: ~~omitted~~

Definition of Pareto Optimality (PO): An allocation $\mathbf A$ is called PO  if no alternative allocation $\mathbf A'\in\Pi_n(\mathcal M)$ can make some players strictly better off without making any player strictly worse off. Formally,
$$
\forall\mathbf A'\in\Pi_n(\mathcal M),(\exists i\in\mathcal N,v_i(A_i')\gt v_i(A_i))\Rightarrow(\exists j\in\mathcal N,v_j(A_j')\lt v_j(A_j)).
$$

# Maximum Nash Welfare is EF1 and PO

Draft mechanism: Round-robin. It guarantees output is EF1, but not Pareto optimal.

Maximizing the utilitarian welfare (sum of utilities to the players) or the egalitarian welfare (minimum utility for any player) is not EF1.

Maximizing these objectives subject to the constraint that the allocation is EF1 violates PO.

Starting point: By assuming the goods are divisible, compute a CEEI allocation. Then come up with an intelligent rounding scheme to allocate each good to one of the players who received some fraction of it.

CEFI may not exist for indivisible goods, but one can still maximize the Nash welfare over all feasible allocations. This solution is referred to as **maximum Nash welfare** (MNW) solution, achieves EF1 + PO.

:question:**What is CEEI?**

>All players are given an equal budget. The equilibrium is an allocation coupled with prices for the goods, s.t. each player buys her favorite bundle of goods for the given pieces, and the market clears.

**Definition of MNW**. The Nash welfare of allocation $\mathbf A\in\Pi_n(\mathcal M)$ is defined as $NW(\mathbf A)=\prod_{i\in\mathcal N}v_i(A_i)$. Given valuations $\{v_i\}_{i\in\mathcal N}$, the MNW solution selects an allocation $\mathbf A^{MNW}$ maximizing the Nash welfare among all feasible allocations, i.e.,
$$
\mathbf A^{MNW}\in{\arg\max}_{\mathbf A\in\Pi_n(\mathcal M)}NW(\mathbf A)
$$
Zero Nash welfare is highly unlikely to appear in practice.

Computing MNW consists of 2 stages:

1. Finding a largest set of players $S$ to which one can simultaneously provide a positive utility.
2. Finding an allocation of the goods to players in $S$ that maximizes their product of utilities.

## MNW Pseudo Code

Input: $\mathcal N,\mathcal M,\{v_i\}_{i\in\mathcal N}$

Output: $\mathbf A^{MNW}$

1. $S\in\arg\max_{T\subseteq\mathcal N:\exists\mathbf A\in\Pi_n(\mathcal M),\forall i\in T,v_i(A_i)\gt0}|T|$; // a largest set of players that can be simultaneously given a positive utility
2. $\mathbf A^*\gets\arg\max_{\mathbf A\in\Pi_{|S|}(\mathcal M)}\prod_{i\in S}v_i(A_i)$; // The MNW allocation to players in $S$
3. $\mathbf A_i^{MNW}\gets A_i^*,\forall i\in S$;
4. $\mathbf A_i^{MNW}\gets\emptyset,\forall i\in\mathcal N\diagdown S$; // Players in $\mathcal N\diagdown S$ do not receive any goods

An allocation is a MNW allocation if it can be selected by the MNW solution.

:dart::thinking:**Theorem 1**. Every MNW allocation is EF1 and PO for additive valuations over indivisible goods.

> Never copy proof from paper again:book:Try to capture the main argument of proof.
>
> PO is trivial. Because increase some $v_i$ without decreasing any $v_j$ violates optimality of Nash welfare under $\mathbf A$.
>
> EF1 is proved by contradiction.
>
> Assume $\mathbf A$ is not EF1, there exist $i,j$, in which $i$ envies $j$ after removing removing any single goods from $A_j$.
>
> Let $g^*=\arg\min_{g\in A_j,v_i(g)\gt0}\frac{v_j(g)}{v_i(g)}$. Denote $\mathbf A'$: after moving $g^*$ from $A_j$ to $A_i$.
>
> It suffices to show $NW(\mathbf A')\gt NW(\mathbf A)$ (By manipulating two inequalities of $g^*$ and Non-EF1.), which gives the desired contradiction as Nash welfare is optimal under $\mathbf A$.
>
> Additionally, we need to discuss the special case where $NW(\mathbf A)=0$.

## General Valuations

Is EF1 and PO can be achieved simultaneously for

1. subadditive;
2. superadditive;
3. submodular (special case of subadditive);
4. supermodular (special case of superadditive) :question:

:dart::thinking:**Theorem 2**. For 1. subadditive; 2. supermodular; 3. superadditive valuations over indivisible goods, there exist instances that do not admit allocations that are EF1 + PO.

> ~~Proof omitted.~~

**This paper didn't solve this question for ==submodular== valuations.** It only shows MNW is not EF1 + PO for submodular valuations. The paper relaxes the EF1 condition, Marginal Envy Freeness up to One Good (MEF1), showing that every MNW allocation satisfies MEF1 + PO.

> subadditive: $\forall A\cap B=\emptyset,v(A)+v(B)\ge v(A\cup B)$.
>
> submodular: $\forall B\subseteq A\land g\notin A,v(A\cup\{g\})-v(A)\le v(B\cup\{g\})-v(B)$
>
> superadditive: $\forall A\cap B=\emptyset,v(A)+v(B)\le v(A\cup B)$.
>
> supermodular: $\forall B\subseteq A\land g\notin A,v(A\cup\{g\})-v(A)\ge v(B\cup\{g\})-v(B)$

**Definition of MEF1**. An allocation $A\in\Pi_n(\mathcal M)$ is MEF1 if
$$
\forall i,j\in\mathcal N,\exists g\in A_j,v_i(A_i)\ge v_i(A_i\cup A_j\diagdown\{g\})-v_i(A_i)
$$
For additive valuations, trivially MEF1 = EF1. For submodular valuations, the marginal value $v_i(A_i\cup A_j\diagdown\{g\})-v_i(A_i)\le v_i(A_j\diagdown\{g\})$.

:dart::thinking:**Theorem 3**. Every MNW allocation satisfies MEF1 + PO for submodular valuations over indivisible goods.

> ~~Proof omitted.~~

# Maximum Nash Welfare is Approximately MMS

## Approximate MMS in Theory

:dart::thinking:**Theorem 4**. Every MNW allocation is $\pi_n$-MMS for additive valuations over indivisible goods, where
$$
\pi_n=\frac{2}{1+\sqrt{4n-3}}.
$$
This factor $\pi_n$ is tight, i.e., for every $n\in\mathbb N$ and $\epsilon\gt0$, there exists an instance with $n$ player having additive valuations in which no MNW allocation is $(\pi_n+\epsilon)$-MMS.

> First, prove that an MNW allocation is $\pi_n$-MMS (lower bound), and later prove tightness of the approximation ratio $\pi_n$ (upper bound).
>
> Lower bound:
>
> - Let $\mathbf A$ be an MNW allocation. Assume $NW(\mathbf A)\gt0$, then handle the case of $NW(\mathbf A)=0$.
>
> - Fix a player $i$, for a player $j\in\mathcal N\diagdown\{i\}$, let $g_j^*=\arg\max_{g\in A_j}v_i(g)$ denote the most valuable good in $A_j$ to $i$.
>
> - Then, prove the **lemma**:
>   $$
>   v_i(A_j\diagdown\{g_j^*\})\le\min\{v_i(A_i),\frac{(v_i(A_i))^2}{v_i(g_j^*)}\}
>   $$
>   $v_i(A_j\diagdown\{g_j^*\})\le v_i(A_i)$ since $\mathbf A$ is MNW.
>
>   To prove $v_i(A_j\diagdown\{g_j^*\})\le\frac{(v_i(A_i))^2}{v_i(g_j^*)}$, we need manipulate the MNW properties:
>
>   1. moving $g_j^*$ from $j$ to $i$ should not increase the Nash welfare.
>   2. moving all goods in $A_j$ except $g_j^*$ from $j$ to $i$ should also not increase Nash welfare.
>
>   Do some transforms to 1. and 2. inequalities, then yield the results.
>
> :bomb::skull: :bomb::skull: :bomb::skull:Finding upper bound on MMS guarantee.
>
>   - Assume all the goods except goods in $T=\{g_j^*|v_i(g_j^*)\gt MMS_i\land j\in\mathcal N\diagdown\{i\}\}$ become divisible.
>
>     Let each item in $T$ be a bundle itself, let others divided into $n-|T|$ equal-valued (w.r.t $i$) bundles.
>
>     Note that all bundles have value $\ge MMS_i$. **inequality (i)**
>
>   - From lemma above, we bound $v_i(A_j\diagdown\{g_j^*\})$ in inequality (i). Then do some transforms and substitute variables.
>
>   -  :bomb::skull: :bomb::skull: :bomb::skull: After that, we need do Mathematical analysis.
>
>     We will get $\beta=\frac{MMS_i}{v_i(A_i)}\le\frac{1}{\pi_n}$.
>
>   For case $NW(\mathbf A)=0$, it's easy to prove $MMS_i=0$.
>
> - Proof of the upper bound (no $\pi_n+\epsilon$ guarantee) and lower bound.
>
>   :skull_and_crossbones::skull_and_crossbones::skull_and_crossbones:~~OMG I have no patience. This is TERRIBLE!!!~~

Best approximation of MMS: $\frac{2}{3}+O(\frac{1}{n})$. Whereas MNW approximates $\Theta(\frac{1}{\sqrt n})$. When number of $n$ is small (in real world), the worst-case approximation guarantee is good.

## Approximate Pairwise MMS, in Theory

Definition of PMMS and its approximation: ~~omitted~~.

Neither PMMS nor MMS implies the other.

Definition of EFX: ~~omitted~~.

:dart::thinking:**Theorem 5**. For additive valuation over indivisible goods:
$$
EF\stackrel{(1)}\Rightarrow PMMS\stackrel{(2)}\Rightarrow EFX\stackrel{(3)}\Rightarrow EF1\\
PMMS\stackrel{(4)}\Rightarrow\frac{1}{2}-MMS
$$

> (1) (3) is easy.
>
> (2) is proved by contradiction.
>
> (4) is complicated:
>
> $\mathbf A$ is PMMS. Consider $i$ and $j$. There are only 2 possible cases:
>
> 1. $A_j$ has at most one good that player $i$ values positively.
> 2. $v_i(A_j)\le2\cdot v_i(A_i)$.
>
> Assume otherwise (not 1. and not 2.), then consider $g^*$ that is the least valuable among player $i$'s positively valued goods in $A_j$. In that case, player $i$ could partition $A_i\cup A_j$ into $(A_i\cup\{g^*\},A_j\diagdown\{g^*\})$ and ensure that her pairwise MMS value is strictly more than $v_i(A_i)$, which contradicts that $\mathbf A$ is PMMS.
>
> If no player in $\mathcal N\diagdown\{i\}$ falls into case 2., it's easy to see the MMS guarantee of player $i$ is at most $v_i(A_i)$, if some players fall into 2. (denoted set $S\subseteq\mathcal N\diagdown\{i\}$), we need to bound the MMS guarantee of $i$ by assuming all the goods allocated to $S\cup\{i\}$ are divisible. However, this still gives an MMS guarantee of at most $2\cdot v_i(A_i)$, because each player in $j\in S\cup\{i\}$ satisfies $v_i(A_j)\le2\cdot v_i(A_i)$. Thus, the MMS guarantee of player $i$ is at most $2\cdot v_i(A_i)$, which implies $\mathbf A$ is ½-MMS.

:dart::thinking:**Theorem 6**. Every MNW allocation is $\Phi$-PMMS, where $\Phi=\frac{\sqrt 5-1}{2}\approx0.618$. This factor is tight.

> One property of a MNW allocation $\mathbf A$: Take the goods allocated to $i$ and $j$, i.e., $\mathcal M'=A_i\cup A_j,\mathcal N'=\{i,j\}$. Then the allocation given by $A_i$ and $A_j$ is also an MNW allocation for the reduced instance of allocating the set of $\mathcal M'$ to player $\mathcal N'$.
>
> Hence, the allocation in $\pi_2$-MMS in the reduced instance.
>
> Therefore, we conclude that MNW allocation is $\Phi$-PMMS as $\pi_2=\Phi$.
>
> Tightness? For a given $n\in\mathbb N,\epsilon\gt0$, we simply use the example of upper bound in theorem 4 after replacing $\pi_n$ by $\pi_2$ in the valuations of players.

## Approximate MMS and PMMS in Practice

~~Omitted.~~

# Implementation

Some experiments.

~~Omitted!!!~~

# Discussion

MNW = EF1 + PO (for additive valuations)

MNW provides $\Theta(\frac{1}{\sqrt n})$-MMS, whereas an arbitrary EF1 and PO allocation only provides $\frac{1}{n}$-MMS.

In paper, assumed the goods are indivisible, but results can be extended to a mix of divisible and indivisible goods.

When all goods are divisible, MNW, CEEI, and proportional Fairness are the same. But not true for indivisible settings: CEEI, PF may not exist.

Open question: is it possible to relate the MNW solution to the CEEI solution when the goods are indivisible?

This paper didn't solve the strategyproofness on MNW, because

1. The only strategyproof and PO solutions are dictatorial - UNFAIR.
2. Users are usually not aware of each other's preference, don't know the behavior of allocation algorithm.