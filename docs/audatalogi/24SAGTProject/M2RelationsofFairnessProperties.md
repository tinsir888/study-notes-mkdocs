---
title: Fair Division 2 Relations of Fairness Properties
author: tinsir888
date: 2024/2/16
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: b1884aba
---

Objective: Investigate relations between several fairness properties and their approximations.

Activities: Read and present the paper *Multiple birds with one stone* (2020).

# Abstract

Algorithm proposed, achieves $(\phi-1)$-EFX and $\frac{2}{\phi+2}$-GMMS. ($\phi={\sqrt5+1\over2}$)

> Previous work: $1\over2$-EFX. $1\over2$-GMMS. EF1 + $2\over3$-PMMS.

GMMS PMMS EFX allocation always exist when # of goods $\le2\times$# of agents.

# Introduction

Relaxation of fairness notion: EF → EFX, EF1

Approximation of relaxed fairness notion: $\alpha$-EFX...

*A good approximation of any one of EF1, EFX, MMS, PMMS doesn't necessarily imply particularly strong guarantees for any of the others.* Thus it's desirable to achieve approximation of several fairness notions simultaneously.

## Main Contribution

Algorithm proposed in this paper:

1. $0.618$-EFX
2. $0.553$-GMMS
3. EF1
4. $0.667$-PMMS

Algorithm is based on draft algorithm, envy-cycle elimination. (parametric version)

GMMS PMMS EFX always exist and can be found efficiently when # of goods $\le2\times$# of agents. non-trivial problem

## Related Work

~~Skipped here~~

# Preliminaries

$N=\{1,2,\cdots,n\}$ set of $n$ agents.

$M$ set of $m$ indivisible items.

Valuation function should be monotone and additive: $\forall S\subseteq M,v_i(S)=\sum_{g\in S}v_i(g)$ and $(\forall i\in N)\land(\forall g\in M),v_i(g)\ge0$.

Assume that we have already know the valuation function for each agent.

**All the goods should be allocated**, i.e., no free disposal.

Allocation: $\mathcal A=(A_1,\cdots,A_n)$ where $A_i\cap A_j\neq\emptyset$ and $\bigcup_iA_i=M$.

- $A_i$ is the bundle allocated to agent $i$,

$\Pi_n(M)$: set of all partitions of a set $M$ into $n$ bundles.

## Fairness Concepts (Review)

Family of Envy-freeness

- EF, EF1, EFX, $\alpha$-EFX

- EF → EFX

Family of Maximin Share (worst-case guarantee)

- Maximin Share $\mu_i$

  - To find an allocation $\mathcal A$ s.t., for each agent $i$ it maximizes $\min_{A_j\in\mathcal A}v_i(A_j)$

    $\Leftrightarrow\mu_i(n,S)=\max_{\mathcal A\in\Pi_n(S)}\min_{A_j\in\mathcal A}v_i(A_j)$

- $\alpha$-MMS $\forall i\in N,v_i(A_i)\ge\alpha\cdot\mu_i(n,M)$

- $\alpha$-P(Pairwise)MMS: For each pair $i,j\in N,v_i(A_i)\ge\alpha\cdot\mu_i(2,A_i\cup A_j)$

- $\alpha$-G(Groupwise)MMS: For each subset $N'\subseteq N$, $\forall i\in N',v_i(A_i)\ge\alpha\cdot\mu_i(|N'|,\cup_{j\in N'}A_j)$

  Thus, **GMMS$\Rightarrow$MMS, MMS$\not\rightarrow$PMMS** (GMMS is for all the subset of agents, MMS is only for all the agents)

## Known EF1 Algorithms

### Envy-cycle Elimination Algorithm

Partial allocation $\mathcal P\subsetneq M$.

Envy graph $G_\mathcal P=(N,E_\mathcal P)$ where $(i,j)\in E_\mathcal P$ iff agent $i$ currently envies agent $j$.

In each step, an agent that no one envies receives the next available good. 

Tie-breaking: lexicographically order.

:book::thinking:Theorem 1: Let $\mathcal P$ be any EF1 partial allocation and $M'=M\diagdown\cup_{i=1}^nP_i$. Then,

> (a) **At the end of each iteration of the for loop, the resulting partial allocation is EF1.** Thus the algorithm terminates with EF1 allocation.
>
> (b) Fix an agent $i$, let $A_i$ be the bundle assigned to $i$ at the end of some iteration of the for loop. If $A_i'$ is assigned to $i$ at the end of a future iteration, then $v_i(A_i')\ge v_i(A_i)$ **MONOTONICITY!**
>
> (Proof by additive and monotone valuation function.)

Pseudo code for Envy-cycle Elimination$(N,\mathcal P,M')$ $M'$ denotes set of unallocated goods.

1. Construct the envy graph $G_\mathcal P$.

2. for every $g\in M'$, in lexicographic order do

   - While there is no node of in-degree 0 in $G_\mathcal P$ do

     - Find a cycle $j_1\rightarrow j_2\rightarrow\cdots\rightarrow j_r\rightarrow j_1$ in $G_\mathcal P$
     - $B=P_{j1}$
     - for $k=1$ to $r-1$ do
       - $P_{jk}=P_{jk+1}$
     - $P_{jr}=B$.

     // find a envy-cycle and eliminate it iteratively.

   - Let $i\in N$ be a node of in-degree $=0$

   - $P_i=P_i\cup\{g\}$

   - Update $G_\mathcal P$

3. return $\mathcal P$

### Round Robin Algorithm

Round-robin$(N,\mathcal P,M',\ell,\tau)$ also outputs EF1 allocation: $\ell$ denotes an order of $N$, $\tau$ denotes number of steps

1. $k=1$

2. while $M'\neq\emptyset$ and $\tau\gt0$ do

   - $g=\arg\max_{h\in M'}v_{\ell[k]}(h)$

     **In the while loop, each round player $\ell[k]$ get currently the maximal value of good.**

   - $P_{\ell[k]}=P_{\ell[k]}\cup\{g\}$

   - $M'=M'\diagdown\{g\}$

   - $k=(k+1)\mod n$

   - $\tau=\tau-1$

3. return $(\mathcal P,M')$

### Draft and Eliminate Algorithm

Draft and Eliminate$(N,M)$ also outputs EF1 allocation: **Combination of ECE & RR**

1. $(\ell,n')=$ Preprocessing$(N,M)$
2. Let $\mathcal A=(A_1,\cdots,A_n)$ with $A_i=\emptyset$ for each $i\in N$
3. $(\mathcal A,M')=$ Round-robin$(N,\mathcal A,M,\ell,n)$
4. Reverse $\ell$: $\ell^R=(\ell[n],\ell[n-1],\cdots,\ell[1])$
5. $(\mathcal A,M')=$ Round-robin$(N,\mathcal A,M',\ell^R,n-n')$
6. $\mathcal A=$ Envy-cycle Elimination$(N,\mathcal A,M')$
7. return $\mathcal A$

:book::thinking:Theorem 2

> Let $\ell$ be any ordering of $N$ and $\mathcal P_\emptyset=(\emptyset,\cdots,\emptyset)$, Round-robin algorithm produces an EF1 allocation in polynomial time.
>
> :raised_hand:Time complexity analysis:
>
> - Number of loop iterative is $\le\min(m,\tau)\le O(m)$
>
> - $\arg\max$ is $O(|M'|)=O(m)$,
>
> - The rest part in the loop should be $O(1)$.
>
>   Thus total complexity should be $O(m^2)$

# A Simple Universally Fair Algorithm

Draft and Eliminate Algorithm. It suffices to run only two rounds of the round-robin algorithm, once w.r.t. $\ell$ and once w.r.t. reverse of $\ell$. **(WHY?)** Finally it runs the envy-cycle elimination algorithm on the remaining instance.

## Preprocessing

Preprocessing$(N,M)$ step is facilitate the presentation and the analysis of the algorithm:

1. $L=\emptyset;A=N;k=1$

   **//$L$ contains the agents that get to pick first in 1st round-robin at the expense of not choosing a second good in 2nd round-robin.**

2. while $A\neq\emptyset$ do

   - Let $i$ be the lexicographically first agent of $A$

   - $h_i=\arg\max_{g\in M}v_i(g)$

   - $t_i=m-|M|+1$

   - Let $R=(N\diagdown(A\cup L))\cup\{i\}$

   - $j=\arg\max_{t\in R}v_i(h_t)$

   - if $\phi\cdot v_i(h_i)\lt v_i(h_j)$ then **here: $\phi=\frac{\sqrt5+1}{2}\approx1.618$**

     **//If an agent envies someone that chose before her by a factor grater than $\phi$, then she is moved to a position of high priority in the ordering that is created.**

     **//The agents moved to the first positions during this process are guaranteed a good of high value in first round-robin. To counterbalance their advantage, they are not allowed to pick a second good later in second round-robin.**

     - $h_i=h_j$
     - $L=L\cup\{i\}$
     - $\ell[k]=i$
     - $k=k+1$
     - $A=(A\diagdown\{i\})\cup\{j\}$

   - else

     - $A=A\diagdown\{i\}$
     - $M=M\diagdown\{h_i\}$

3. for every $i\in N\diagdown L$ in order of increasing time stamp $t_i$ do

   - $\ell[k]=i$
   - $k=k+1$

4. return $(\ell,[L])$

:question: After preprocessing, have we $A\cup L=N$?

This preprocessing part reorders $N$ so that the first few agents are quite happy with their pick in the first round of the round-robin subroutine. For the remaining agents, we make sure that they get a second good before we move to the envy-cycle elimination algorithm. To do so in a balanced way, these agents pick goods in reverse order.

The resulting partial allocation, where everyone receives one or two goods, turns out to have all the fairness properties we want to achieve at the end e.g. it's $(\phi-1)$-EFX w.r.t. currently allocated goods. Starting from there and then applying the envy-cycle elimination algorithm on the remaining instance maintains these properties.

# Fairness Guarantees of Draft-and-Eliminate 

## About Preprocessing Step

:book::thinking:Lemma 1: At the end of the execution of preprocessing with input $(N,M)$, each agent $i$ is associated with a single good $h_i$, so that

a) $v_i(h_i)\gt\phi\cdot v_i(g)$, for any $i\in L$ and $g\in M\diagdown\cup_{k=1}^n\{h_k\}$.

>Fix some $i\in L$ and consider the last iteration of the while loop where $i$ was the lexicographically first agent of $A$. During which $i$ was added to $L$.
>
>The initial good associated with $i$: $h_i^{old}$ during this iteration.
>
>The final good associated with $i$: $h_i$.
>
> $\because$ $i$ was added to $L$ eventually. $\therefore$ condition "$\phi\cdot v_i(h_i)\lt v_i(h_j)$" was true, i.e.,
>
>$i$'s favorite good among the ones associated to an agent not in $L$, say $h_j$, was more than $\phi$ times more valuable than $h_i^{old}$, we have $v_i(h_j)\gt\phi\cdot v_i(h_i^{old})\ge\phi\cdot v_i(g)$ for any good $g$ that was not associated to an agent at the time.
>
>Number of unassociated goods during the execution only decreases, thus the last inequality also holds for any good $g$ that was not associated to any agent till the end.
>
>By $h_i=h_j$, we conclude that $v_i(h_i)\gt\phi\cdot v_i(g)$ $\forall g\in M\diagdown\cup_{k=1}^n\{h_k\}$.

b) $\phi\cdot v_i(h_i)\ge v_i(h_j)$ for any $i,j\in N\diagdown L$.

>Fix some $i,j\in N\diagdown L$. Note that both $i,j$ may be considered multiple times during preprocessing, i.e., they may be removed and added back to $A$ in several times.
>
>Consider 2 cases:
>
>1. Last time $i$ was considered before last time $j$ was considered ($t_i\lt t_j$ at the end)
>
>   the desire inequality is obvious as agent $i$ is associated with her favorite good among the available goods, $h_i$, before agent $j$, i.e., $v_i(h_i)\ge v_i(h_j)$.
>
>2. Last time $i$ was considered after last time $j$ was considered ($t_i\gt t_j$ at the end)
>
>   suppose that $\phi\cdot v_i(h_i)\lt v_i(h_j)$. Then during the last iteration that $i$ was considered, the if statement is true and $i$ would be irrevocably added to $L$, which contradicts the choice of $i$.
>
>Q. E. D.

:book::thinking:Lemma 2: The partial allocation produced in first round-robin is $\mathcal (\{h_1\},\{h_2\},\cdots,\{h_n\})$, where $h_i$s are in Lemma 1.

==The preprocessing step can be combined with 1st round-robin!==

> Observation: $\ell$ is used in first round-robin subroutine is not the same with the order that goods get assigned to agents during the preprocessing step, even when one takes into account that moving agents into $L$ is similar to changing their order.
>
> First, using **induction**, we show that agents in $L$ get assigned to them the same goods they would if there were to choose first according to $\ell$.
>
> For agents in $N\diagdown L$. First observe that, on termination, agents in $N\diagdown L$ all have distinct time stamp assigned in second round-robin. 
>
> By time stamp of agent $i$, name the final value of $t_i$. Now fix some $i\in N\diagdown L$. 
>
> - In **preprocessing**, agent $i$ gets assigned her favorite good available if we exclude the goods assigned to **some agents** in $L$, and to all the agents in $N\diagdown L$ with time stamp less than $t_i$.
>
> - In **round-robin**, agent $i$ receives her favorite good available if we exclude the goods allocated to **all the agents** in $L$, and to all the agents in $N\diagdown L$ with time stamp less than $t_i$. However, in this scenario the extra agents from $L$ that pick before $i$ choose goods that $i$ does not find attractive enough. Indeed those goods are available in the first scenario, yet $i$ does not prefer them to $h_i$.
>
>   Therefore, in this scenario $i$ also picks $h_i$ and $A_i=\{h_i\}$ after first round-robin.

:thinking:Proposition: Draft-and-elimination returns an EF1 allocation.

> By Theorem 2, it suffices to show that the partial allocation produced after second round-robin is EF1. Fix two distinct agents $i,j\in N$. If $j\in L$, then $A_j=\{h_j\}$. Clearly, $v_i(A_i)\ge v_i(A_j\diagdown\{h_j\})=0$. On the other hand, if $j\in N\diagdown L$, then $A_j=\{h_j,h_j'\}$. Since agent $i$ picked $h_i$ when $h_j'$ was still available, $v_i(h_i)\ge v_i(h_j')$. So we have $v_i(A_i)\ge v_i(h_i)\ge v_i(A_j\diagdown\{h_j\})$.

## Envy-freeness up to Any Good

:book::thinking:Theorem 3: The allocation $\mathcal A$ returned by Draft-and-elimination is a $(\phi-1)$-EFX allocation. :warning:

>Consider the allocation $\mathcal A$ returned by algorithm, and fix two distinct agents $i,j\in N$. If $|A_j|=1$, then clearly, $v_i(A_i)\ge\max_{g\in A_j}v_i(A_j\diagdown\{g\})=0$.
>
>So assume that $|A_j|\ge2$ and let $h$ be the last good added to $A_j$. At time this happened, $A_j$ may belonged to an agent $j'$ other than $j$. Finally, let $A_i^{old},A_{j‘}^{old}$ be bundles of $i$ and $j'$ respectively, right before $h$ was allocated. (i.e. $h\in A_{j'}^{old}$ at first)
>
>Note that $A_i^{old}$ may not necessarily be a subset of $A_i$ due to the possible swaps imposed by envy-cycle elimination. But part (b) of theorem 1 implies $v_i(A_i)\ge v_i(A_i^{old})$.
>
>We consider 4 cases, depending on whether $i\in L$ and on the type of step during which $h$ was added to $A_{j'}^{old}$.
>
>1. $i\in L$ and $h$ added in second round-robin.
>
>   We have $A_i^{old}=\{h_i\}$, as well as $j'\in N\diagdown L$ and $A_j=\{h_{j'},h'_{j'}\}$. This immediately implies that $v_i(A_i^{old})\ge\max\{v_i(h_{j'}),v_i(h'_{j'})\}$. Thus $v_i(A_i)\ge\max_{g\in A_j}v_i(A_j\diagdown\{g\})$.
>
>2. $i\in L$ and $h$ added in envy-cycle elimination.
>
>   By the way that envy-cycle-elimination chooses who to give the next good to, we know that right before $h$ was added, no one envied $j'$. In particular, $v_i(A_i^{old})\ge v_i(A_{j'}^{old})$. We further have $v_i(A_i^{old})\ge v_i(h_i)\gt\phi\cdot v_i(h)$, where the last inequality directly follows from part (a) of lemma 1. Putting everything together,
> 
> $$
>   v_i(A_j)=v_i(A_{j'}^{old})+v_i(h)\le(1+\phi-1)v_i(A_i^{old})\le\phi\cdot v_i(A_i)\\
>   \Leftrightarrow v_i(A_i)\ge(\phi-1)\cdot v_i(A_j)
> $$
>
>3. $i\not\in L$ and $h$ added in second round-robin.
>
>   We have $i,j'\in N\diagdown L$ and $A_j=\{h_{j'},h'_{j'}\}$. If $\ell[i]\lt\ell[j']$, then we proceed in a way similar to case 1:
>
> $$
>   v_i(A_i)\ge v_i(A_i^{old})\ge v_i(h_i)\ge\max\{v_i(h_{j'}),v_i(h'_{j'})\}.
> $$
>
>   So, assume that $\ell[i]\gt\ell[j']$. This means $v_i(h_i')\ge v_i(h_{j'}')$. We have
>
> $$
>   v_i(A_i)\ge v_i(A_i^{old})\ge v_i(h_i)+v_i(h_i')\ge{1\over\phi}v_i(h_{j'})+v_i(h'_{j'})\\
>   \ge{1\over\phi}(V_i(h_{j'})+v_i(h'_{j'}))=(\phi-1)v_i(A_j)
> $$
>
>   where the third inequality directly follows from part (b) from lemma 1.
>
>4. $i\not\in L$ and $h$ added in envy-cycle elimination.
>
>   Arguing like in case 2, we have $v_i(A_i^{old})\ge v_i(A_{j'}^{old})$. Moreover, by the way round-robin works, we know that $v_i(h_i)\ge v_i(h'_i)\ge v_i(h)$. In particular, $v_i(h)\le{1\over2}v_i(\{h_i,h_i'\})\le{1\over2}v_i(A_i^{old})$. Putting things together, we have
>
> $$
>   v_i(A_j)=v_i(A_{j'}^{old})+v_i(h)\le(1+\frac{1}{2})v_i(A_i^{old})\le\phi\cdot v_i(A_i)\\
>   \Leftrightarrow v_i(A_i)\ge(\phi-1)v_i(A_j)
> $$
>
>The analysis is tight.

## Groupwise Maximin Share Fairness

Every exact EFX allocation is also a $4\over7$-GMMS allocation. (Amanatidis et al.)

:book::thinking:Lemma 3: Suppose $\mathcal T\in\Pi_n(M)$ is an $n$-maximin share defining partition for agent $i$. Then, for any set of goods $S$, such that there exists some $j$ with $S\subseteq T_j$, it holds that $\mu_i(n-1,M\diagdown S)\ge\mu_i(n,M)$.

:book::thinking:Theorem 4: The allocation $\mathcal A=(A_1,\cdots,A_n)$ returned by Draft and Eliminate Algorithm is a $2\over\phi+2$-GMMS allocation.

> ~~To difficult for me!~~ :sob:
>
> Omitted

:book::thinking: Theorem 5: Suppose we modified Draft and Eliminate Algorithm by changing $\phi$ in preprocessing to $3\over2$. Then the resulting allocation is a $4/7$-GMMS allocation. It is also a $3/5$-EFX, $2/3$-PMMS, and EF1 allocation.

> ~~To difficult for me!~~ :sob:
>
> Omitted

Analysis are probably not tight, because both theorems is heavily based on EF and EFX guarantees we get in the various different cases.

:question:Suspect: modified draft and eliminate algorithm produces $2/3$-GMMS.

## Pairwise Maximin Share Fairness

:book::thinking:Theorem 6: The allocation $\mathcal A=(A_1,\cdots,A_n)$ returned by Draft and Eliminate Algorithm is a $2/3$-PMMS allocation.

> Proof based on proof of theorem 3.

$2/3$ factor is tight. But if we manipulate envy-graph as following, the approximation ratio goes up to ${2\over\phi+1}\approx0.764$: ~~Detail omitted~~

:book::thinking:Theorem 7: Supposed we modified Draft and Eliminate Algorithm by using the $\phi-{1\over2}$-adjusted envy graph in Envy-graph Elimination. Then the resulting allocation is a $4\phi-2\over2\phi+3$-PMMS and a $2\over2\phi-1$-EF1 allocation. Moreover, the theorem 3 and theorem 4 are not affected.

> Proof omitted.

# GMMS, PMMS, and EFX with a few Goods

For the exact versions of fairness notions,

## Draft Pack and Eliminate Pseudo Code

1. Let $\ell=(1,2,\cdots,n)$ and $\mathcal A=(\emptyset,\cdots,\emptyset)$
2. $(\mathcal A,M')=$ Round-Robin$(N,\mathcal A,M,\ell,n-1)$
3. Create 2 virtual goods $p,q$, s.t. $\forall i\in N:v_i(q)=\min_{g\in M'}$ and $v_i(p)=v_i(M')-v_i(q)$
4. Allocate $p$ to agent $n$
5. $\mathcal A=$ Envy-Cycle Elimination$(N,\mathcal A,\{q\})$
6. Give to the final owner of $p$ her two favorite goods from $M'$ and to the final owner of $q$ the remaining good
7. return $\mathcal A$.

:book::thinking:Theorem 8: For instances with $m\le n+2$, a GMMS allocation always exists and can be efficiently computed.

> When $m\le n$, we arbitrarily allocate one good to each agent, till there are no goods left, to produce $\mathcal A=(A_1,\cdots,A_n)$. Fix a subset $S$ of agents, and let $i\in N$. Then combined bundle of all agents in $S$ either contains strictly less than $|S|$ goods or exactly $|S|$ goods.
>
> - In the first case, we trivially have $\mu_i(|S|,\cup_{j\in S}A_j)=0$.
>
> - In the second case, we have $\mu_i(|S|,\cup_{j\in S}A_j)=\min_{g\in\cup_{j\in S}A_j}v_i(g)$.
>
> In both cases, we have $v_i(A_i)\ge\mu_i(|S|,\cup_{j\in S}A_j)$, and $\mathcal A$ is a GMMS allocation.

For $m\gt n$, there are some simple observations.

:book::thinking:Lemma 4: Let $Q\subseteq N$ and $T\subseteq M$ s.t. $|T|=2|Q|-1$. Then, for any $i\in Q$, we have $\max_{g\in T}v_i(g)\ge\mu_i(|Q|,T)$.

> By the pigeonhole principle, in any possible partition of $T$ in $|Q|$ parts, at least one bundle will have at most $1$ goods. So, in any $|Q|$-maximin share partition of $T$ for an agent $i\in Q$, her worst bundle's worth is upper bounded by $\max_{g\in T}v_i(g)$.

:book::thinking:Lemma 5: Let $i\in N$ and $T=\{g_1,g_2,g_3,g_4\}\subseteq M$ s.t. $v_i(g_1)\ge v_i(g_2)\ge v_i(g_3)\ge v_i(g_4)$. Then

1. $v_i(\{g_1,g_4\})\ge\mu_i(2,T)$
2. $\max\{v_i(g_1),v_i(\{g_2,g_3\})\}\ge\mu_i(2,T)$

> Long proof...

Corollary: When $m\le n+2$, we can efficiently find PMMS and EFX allocations.

> Proof omitted...

# What should I get from this paper?

EF1$\Leftarrow$EFX$\Leftarrow$PMMS, GMMS

EF1: Always exist.

EFX: $0.618$

PMMS: $2/3$

GMMS: Not always exist.

We are trying to relax the properties as less as possible so that a single allocation satisfies all relaxation, and if possible, with allocation computed is **polynomial** time.