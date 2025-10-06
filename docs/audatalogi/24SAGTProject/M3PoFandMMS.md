---
title: Fair Division 3 Price of Fairness and MMS Property
author: tinsir888
date: 2024/3/1
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: cc139c6f
---

Objective: Explore the concept of the Price of Fairness and Maximin Share (MMS) property.

Activities: Read and present paper [*Optimal Bounds on the Price of Fairness for Indivisible Goods*](https://arxiv.org/abs/2007.06242) (2020) and [*Approximation Algorithms for Maximin Fair Division*](https://arxiv.org/abs/1703.01851) (2020).

# Paper on PoF

Price of Fairness measures the worst-case loss of social welfare due to fairness constraints.

This paper: Both EF1 and ${1\over2}$-MMS guarantees has PoF $O(\sqrt n)$, where $n$ is number of agents.

- For ${1\over2}$-MMS, this bound holds for additive valuations.
- For EF1, this bound holds for more general class of subadditive valuations.

## Introduction

EF family ~~omitted here~~

MMS family

- $3\over4$-MMS always exist.

Fair is not the only objective of interest, another criterion is the *social welfare*.

Tradeoff between fairness and social welfare is measure by *price of fairness*, the supremum **ratio** of the **maximum welfare of any allocation** to the **maximum welfare of any allocation satisfying the desired fairness notion**. This measures the factor by which welfare may be lost to achieve the desired fairness.

### Related Work

Caragiannis et al. Initiated PoF: PoF of Envy-freeness is between $\Omega(\sqrt n)$ and $O(n)$.

Beretsimas et al. Closed the gap, bound the PoF to $\Theta(\sqrt n)$, matching upper bound can be achieved by maximizing the Nash welfare.

For **Indivisible goods and additive valuations**

- Bei et al. showed PoF of EF1 is between $\Omega(\sqrt n)$ and $O(n)$.

### Contributions

Price of EF1 is $O(n)$: subadditive valuation function

Price of PROP1: $\Theta(n)$

Price of ${1\over2}$-MMS: $\Theta(\sqrt n)$. For a fixed $\epsilon\gt0$, a $(\frac{1}{2}-\epsilon)$-MMS allocation with welfare with $O(\sqrt n)$ factor of the optimal can be computed in polynomial time.

## Preliminaries

Set $[m]$ of indivisible goods

Set $[n]$ of agents

**Agent valuations**. Each agent $i\in[n]$ has non-negative valuation over goods.

2 valuation classes:

- Additive $v_i(A+B)=v_i(A)+v_i(B)$

- Subadditive $v_i(A+B)\le v_i(A)+v_i(B)$

  (Additive $\subset$ Subadditive)

**Oracle access**.

- Value query: Input $i$ and $S\subset[m]$ output $v_i(S)$.
- Demand query: Input $i$ and price $p_g\in\mathbb R_{\ge0}$ for each $g\in[m]$, output a profit-maximizing set $S^*\in\arg\max_{S\in[m]}v_i(S)-\sum_{g\in S}p_g$.

**Allocations**. ~~omitted here~~

**Fairness.** ~~omitted here~~

**Social welfare**. The SW of allocation $\mathcal A$, is defined as the sum of the values that the agents derive from the allocation: $SW(\mathcal A)=\sum_{i=1}^nv_i(A_i)$. Maximum social welfare allocation: $\mathcal W^*=(W_1^*,W_2^*,\cdots,W_n^*)$.
$$
\mathcal W^*\in\arg\max_{\mathcal A\in\Pi_n([m])}SW(\mathcal A)
$$
$OPT$ denote the optimal social welfare, i.e., $OPT=SW(\mathcal W^*)$.

**Price of fairness**.
$$
PoF={SW(\mathcal W^*)\over\max_{\mathcal A\in\text{Fair Allocation}}SW(\mathcal A)}
$$
**Scaling**. This paper considers both scaled and unscaled valuations of agents' valuations.

:question:**What does scaling here means**

> To ensure that agent valuations are on the same scale, much of the literature on fair division assumes that agent valuations are *scaled*, i.e. $v_i([m])=1\forall i\in[n]$. Nothing that unscaled valuations are common in other areas of social choice.
>
> In my words, *scaled* means for all agents, their valuations for all the goods are the same.
>
> ==My question: Must the valuation here be additive?==

## Price of EF1

:dart::thinking:**Theorem 1** The price of EF1 is $O(\sqrt n)$ for **scaled** subadditive valuations and $\Theta(n)$ for **unscaled** subadditive valuations. Both bounds are tight even when the valuations are additive.

### An Absolute Welfare Guarantee

First, we show that when agents have subadditive valuations $\{v_i\}_{i\in[n]}$ (not necessarily scaled), there always exists an EF1 allocation $\mathcal A$ with social welfare $SW(\mathcal A)\ge{1\over2n}\sum_{i=1}^nv_i([m])$.

2 implications:

$\because\sum_{i=1}^nv_i([m])$ is a trivial upper bound on the optimal social welfare OPT, the result establishes am $O(n)$ upper bound on the price of EF1.

- For unscaled valuations, this is exactly the bound.
- For scaled valuations, we improve this to $O(\sqrt n)$. Since $\sum_{i=1}^nv_i([m])=n$ under scaled valuations, the result gives $SW(\mathcal A)\ge1/2$. Hence, if $OPT=O(\sqrt n)$, then we have the desired $O(\sqrt n)$ upper bound.

This paper will analyze the case when $OPT=\Omega(\sqrt n)$.

This paper also give an algorithm to produce absolute welfare guarantee.

#### Pseudo code for Algorithm `ALF-EF1-ABS`:

Input: Fair-division instance $\mathcal I=\lang[n],[m],\{v_i\}_{i\in[n]}\rang$ with value-query oracle access to the subadditive valuations $v_i$s.

Output: An EF1 allocation $\mathcal B$ with $SW(\mathcal B)\ge{1\over2n}\sum_{i\in[n]}v_i([m])$.

1. Consider the weighted bipartite graph $G=([n]\cup[m],[n]\times[m])$ with weight of each edge $(i,g)\in[n]\times[m]$ set as $v_i(g)$. Let $\pi$ be a maximum-weight matching in $G$ that matches all nodes in $[n]$.
2. Construct the partial allocation $\mathcal B'$ s.t. $\mathcal B_i'=\{\pi(i)\}$ for each $i\in[n]$.
3. Use the algorithm of Lipton et al to extend the partial EF1 allocation $\mathcal B'$ into a complete EF1 allocation $\mathcal B$ s.t. $v_i(B_i)\ge v_i(B_i')$ for each agent $i\in[n]$.s
4. return allocation $\mathcal B$



:thinking:**Lemma 1**. Let $\mathcal I=\lang[n],[m],\{v_i\}_{i\in[n]}\rang$ be a fair-division instance in which agent valuations are subadditive. Then, given value-query oracle access to the valuations, `ALG-EF1-ABS` algorithm efficiently computes an EF1 allocation $\mathcal B$ with social welfare $SW(\mathcal B)\ge{1\over2n}\sum_{i\in[n]}v_i([m])$.

> For each agent $i\in[n]$, sort the goods as $g_{i_1},\cdots,g_{i_m}$ in a non-decreasing order of their value to agent $i$ i.e. $v_i(g_{i_1})\ge\cdots\ge v_i(g_{i_m})$, and let $G_i=\{g_{i_1},\cdots,g_{i_n}\}$ be the set of $n$ most valuable goods to agent $i$.
>
> Consider a subgraph $G'=([n]\cup[m],E')$ of the weighted bipartite graph $G$ constructed in line 1 of the algorithm, in which we only retain the edges $E'=\{(i,g):i\in[n],g\in G_i\}$ (i.e. from each agent $i$ to her $n$ most valuable goods). Because $G'$ is **$n$-left-regular**, it can be decomposed into $n$ disjoint left-perfect matchings $M_1,\cdots,M_n$, this is an easy consequence of Halls' theorem. Due to the pigeonhole principle, one of $M_1,\cdots,M_n$ must have weight at least $1\over n$. We have
> $$
> \sum_{i\in[n]}v_i(\pi(i))=SW(\mathcal B')\ge{1\over n}\cdot\sum_{i\in[n],g\in G_i}v_i(g)
> $$
> $\mathcal B'$ assigns a single good to each agent; hence, it is trivially EF1. The algorithm makes sure that $\mathcal B$ is the extension of $\mathcal B'$ s.t. $v_i(B_i)\ge v_i(B_i')$. Thus,
> $$
> SW(\mathcal B)\ge{1\over n}\sum_{i\in[n]}\sum_{g\in G_i}v_i(g)
> $$
> 
>
> $\mathcal B$ is EF1, thus for each $i,j\in[n]$, there exists $S_j\subseteq B_j$ with $|S_j|\le1$ s.t. $v_i(B_i)\ge v_i(B_j\diagdown S_j)\ge v_i(B_j)-v_i(S_j)$ (**subadditive** for last inequality). Summing this inequality over $j\in[n]$, we get
> $$
> n\cdot v_i(B_i)\ge\sum_{j\in[n]}v_i(B_j)-\sum_{j\in[n]}v_i(S_j)\\
> \ge v_i([m])-\sum_{j\in[n]}v_i(S_j)\ge v_i([m])-\sum_{g\in G_i}v_i(g),
> $$
> where the penultimate inequality holds because $B_j$s form a partition of $[m]$ and the last inequality follows from subadditivity. $S_j$s are disjoint and have cardinality at most $1$, and $G_i$ is the set of $n$ most valuable goods to $i$. Summing over $i\in[n]$, we get
> $$
> n\cdot\sum_{i\in[n]}v_i(B_i)=n\cdot SW(\mathcal B)\ge\sum_{i\in[n]}v_i([m])-\sum_{i\in[n]}\sum_{g\in G_i}v_i(g).
> $$
> Substituting equation, we get the desired bound
> $$
> n\cdot SW(\mathcal B)+n\cdot SW(\mathcal B)\ge\sum_{i\in[n]}v_i([m])-\sum_{i\in[n]}\sum_{g\in G_i}v_i(g)+\sum_{i\in[n]}\sum_{g\in G_i}v_i(g)\\
> =\sum_{i\in[n]}v_i([m])
> $$
> which implies $SW(\mathcal B)\ge{1\over 2n}\sum_{i\in[n]}v_i([m])$ as needed.

### The Case of High Optimal Welfare

Lemma 1 focuses on fair division with scaled subadditive valuations in which the optimal social welfare is $\Omega(\sqrt n)$, which means we can sacrifice $O(\sqrt n)$ of the welfare in OPT, obtain $O(\sqrt n)$ approximation to the remaining welfare through an EF1 allocation, and yet achieve $O(\sqrt n)$ approximation to OPT.

In particular, this paper presents an algorithm that efficiently finds an EF1 allocation $\mathcal A$ with social welfare $SW(\mathcal A)\ge{OPT-2\sqrt n\over12\sqrt n}$.

$\mathcal W^*$ corresponds to optimal social welfare $OPT$.

However, for subadditive valuations, computing such an allocation is NP-hard under both value queries and demand queries.

Using a polynomial number of value queries, it's $\Theta(\sqrt m)$ best possible approximation to the optimal social welfare. (Too loose)

Starting with high-welfare allocation $\mathcal W$ returned by Feige's Algorithm, the algorithm works as follows: It first indexes $m$ goods as $g_1,\cdots,g_m$ s.t. the goods n each $W_i$ receive consecutive indices. Alternatively, consider a line graph $L=([m],E)$ over the set of goods with edges $E=\{(g_k,g_{k+1}):k\in[m-1]\}$. Then each $W_i$ induces a connected subgraph of $L$.

Definition of *line graph*: Let $L=([m],E)$ be a line graph over the goods, we say that $S\subseteq[m]$ is a **connected bundle** in $L$ if $S$ induces a connected subgraph of $L$. Given a partial allocation $\mathcal P$, define $\mathcal U(\mathcal P)$ as the set of connected components of $L$ that remain after moving the allocated goods $\cup_{i\in[n]}P_i$. We refer to $U\in\mathcal U(\mathcal P)$ as an **unassigned connected bundle**.

Algorithm `ALG-EF1-HIGH` builds a partial allocation $\mathcal P$ by giving each agent $i$ her most valuable good from $W_i$. This allocation satisfies: **EF1**, and **each $P_i$ is a connected bundle in $L$**. (trivial) The algorithm then iteratively updates $\mathcal P$ to improve its social welfare while maintaining both properties.

In particular, at every iteration, the algorithm computes the set of unassigned connected bundles $\mathcal U(\mathcal P)$. Removing $n$ connected bundles from $\mathcal P$ can create at most $n+1$ unassigned connected bundles.

If there is an unassigned connected bundle $U$ that some agent envies, then the algorithm finds an *inclusion-wise minimal* subset of $U$ that is envied and allocates it to an envious agent.

- This paper argues that inclusion-wise minimality preserves the EF1 of $\mathcal P$.
- This paper also argues that this iterative process terminates at a partial allocation $\mathcal P$ that satisfies the desired social welfare guarantee.

`ALG-EF1-HIGH` is extended from Lipton et al, which computes EF1 allocation without losing social welfare.

~~Pseudo code omitted here.~~

:thinking:**Lemma 2**. When `ALG-EF1-HIGH` is run on a fair division instance $\mathcal I=\lang[n],[m],\{v_i\}_{i\in[n]}\rang$ with subadditive valuations, the following hold regarding the partial allocation $\mathcal P^t$ constructed after $t$ iterations of the while loop.

1. If $t\ge1$, then for each agent $i\in[n]$, either $P_i^t=P_i^{t-1}$ or $v_i(P_i^t)\ge v_i(P_i^{t-1})$.
2. For each agent $i\in[n]$, $P_i$ is a connected bundle under the line graph $L$ constructed in checking whether there exists agent envying a $U\in\mathcal U(\mathcal P)$.
3. $P^t$ is EF1.

> 1\. and 2\. are trivial.
>
> For 3\., using induction.

:thinking:**Lemma 3**. When `ALG-EF1-HIGH` is run on a fair division instance $\mathcal I$ with subadditive valuations, the following hold:

1. The while loop terminates after $T=O(nm^2)$ iterations.
2. The partial allocation $\mathcal P^T$ constructed at the end of the while loop satisfies $v_i(P_i^T)\ge v_i(U)$ for every agent $i\in[n]$ and unassigned connected bundle $U\in\mathcal U(\mathcal P^T)$.

> $O(m^2)$ possible connected bundles in $L\Rarr$ an agent's assignment can only be updated $O(m^2)$ times. There are $n$ agents in total.
>
> The while loop would not have terminated if $v_i(P_i^T)\lt v_i(U)$ for any agent $i$ and any unassigned connected bundle $U$.

:thinking:**Lemma 4**. Given a fair division instance $\mathcal I$ with **scaled** subadditive valuations, `ALG-EF1-HIGH` terminates in polynomial time and returns an EF1 allocation $\mathcal A$ satisfying
$$
SW(\mathcal A)\ge{OPT\over12\sqrt n}-{1\over6}.
$$
where $OPT$ is the optimal social welfare achievable in instance $\mathcal I$.

> Feige's algorithm satisfies $SW(\mathcal W)\ge{1\over2}OPT$. Let the while loop in `ALG-EF1-HIGH` terminate with partial allocation $\mathcal P$; that is $\mathcal P=\mathcal P^T$, where $T$ is # of iterations. Lemma 3 ensures that the while loop terminates. It suffices to show
> $$
> \sqrt n+6\sqrt n\cdot SW(\mathcal P)\ge SW(\mathcal W).
> $$
> By lemma 2, $\mathcal P$ is EF1. Also, note that the algorithm of Lipton et al can extend EF1 allocation $\mathcal P$ into a complete EF1 allocation $\mathcal A$ with $SW(\mathcal A)\ge SW(\mathcal P)$. Hence the inequality above along with $SW(\mathcal W)\ge{1\over2}OPT$ completes the proof of the lemma 4.
>
> Due to lemma 2, each $P_i$ is a connected bundle in the line graph $L$. Hence, removing the goods allocated in $\mathcal P$ creates at most $n+1$ connected components, i.e. $|\mathcal U(\mathcal P)|\lt n+1$. Let $\mathcal T(\mathcal P)=\{P_j:j\in[n]\}\cup\mathcal U(\mathcal P)$ be the collection of the assigned and unassigned in $L$ created by $\mathcal P$. ==Hence $|\mathcal T(\mathcal P)|\lt2n+1$.== For each agent $i\in[n]$, let $\mathcal Q_i$ denote the bundles in $\mathcal T(\mathcal P)$ that intersect with $W_i$, i.e., $\mathcal Q_i=\{S\in\mathcal T(\mathcal P):S\cap W_i\neq\empty\}$. write $q_i=|\mathcal Q_i|$.
>
> While $\mathcal W$ forms a partition of $L$ into at most $n$ connected components. This, along with the observation that $L$ is a line graph, can be used to bound the total number of intersections between the two. We can move a marker from left to right, keeping track of the bundle from $\mathcal W$ and the bundle from $\mathcal T(\mathcal P)$ that contain the current good. Every time the marker encounters a new intersection, it must have entered either a new bundle from $W$ or a new bundle from $\mathcal T(\mathcal P)$. Thus, we have
> $$
> \sum_{i\in[n]}q_i\le(2n+1)+n=3n+1.
> $$
> Next, we partition the set of agents $[n]$ based on their $q_i$ value. Let $H=\{i\in[n]:q_i\gt3\sqrt n\}$ and $H^c=\{i\in[n]:q_i\le3\sqrt n\}$. Above inequality implies $|H|\le\sqrt n$. This along with the observation that the valuation are scaled, gives us
> $$
> \sum_{i\in H}v_i(W_i)\le|H|\le\sqrt n
> $$
> Next, we bound $v_i(W_i)$ for $i\in H^c$. By definition of $\mathcal Q_i$, we have $v_i(W_i)=\sum_{S\in\mathcal Q_i}v_i(S\cap W_i)$. We break the sum into 2 parts, $S\in\mathcal U(\mathcal P)$ or $S\in\mathcal P$.
>
> 1. If $S\in\mathcal U(\mathcal P)$, by lemma 3, we have $v_i(S\cap W_i)\le v_i(S)\le v_i(P_i)\le 2v_i(P_i)$.
> 2. If $S\in\mathcal P$, then $S=P_j$ for some agent $j\in[n]$. Let $\hat P_j=P_j\cap W_i$. By definition of $\mathcal Q_i$, $\hat P_j\neq\empty$. $\because\mathcal P$ is EF1 and $v_i$ is monotone, agent $i$ does not envy the bundle $P_j$ up to one good in it. Thus, there exists a good $\hat g\in\hat P_j$ s.t. $v_i(P_i)\ge v_i(\hat P_j\diagdown\{\hat g\})\ge v_i(\hat P_j)-v_i(\hat g)$, where the last transition uses subadditivity of $v_i$. Further, $\hat g\in\hat P_j\subseteq W_i$. The initialization and lemma 2 ensure that $v_i(\hat g)\le v_i(P_i^0)\le v_i(P_i)$. Combining with the previous step, we have $v_i(S\cap W_i)=v_i(\hat P_j)\le2v_i(P_i)$.
>
> Thus, for $i\in H^c$, we have $v_i(W_i)\le|\mathcal Q_i|\cdot2v_i(P_i)\le3\sqrt n\cdot2v_i(P_i)$. Thus, we get
> $$
> SW(\mathcal W)\le\sqrt n+6\sqrt n\sum_{i\in H^c}v_i(P_i)\le\sqrt n+6\sqrt n\cdot SW(\mathcal P)
> $$
> which is desired. Q.E.D.

### Proof of Theorem 1

#### Proof for upper bounds

For **unscaled** valuations, lemma 1 shows that there exists an EF1 allocation $\mathcal B$ with $SW(\mathcal B)\ge{1\over2n}\sum_{i\in[n]}v_i([m])\ge{1\over2n}OPT$, which yields the desired $O(n)$ bound.

For an instance $\mathcal I$ with **scaled** valuations, we consider 2 cases: $OPT\le8\sqrt n$ or $OPT\gt8\sqrt n$.

Case 1: $OPT\le8\sqrt n$. $\because$ valuations are scaled. Lemma 1 implies that the EF1 allocation $\mathcal B$ returned by `ALG-EF1-ABS` satisfies $SW(\mathcal B)\ge{1\over2n}\sum_{i\in[n]}v_i([m])={1\over2}$. Hence, $SW(\mathcal B)\ge{1\over16\sqrt n}OPT$,

Case 2: $OPT\gt8\sqrt n$. Then ${1\over6}\lt{OPT\over48\sqrt n}$. Now lemma 4 implies that the EF1 allocation $\mathcal A$ returned by `ALG-EF1-HIGH` satisfies
$$
SW(\mathcal A)\ge{OPT\over12\sqrt n}-{1\over6}\ge{OPT\over12\sqrt n}-{OPT\over48\sqrt n}={OPT\over16\sqrt n}
$$

#### Proof for lower bounds

For **scaled** valuations, $\Omega(\sqrt n)$ is proved by Bei et al.

For **unscaled** valuations, consider the case $m=n$, following additive valuations: the first agent has value $n$ for each good, while the other agents have value $1/n$ for each good. The optimal social welfare is allocating all goods to the first agent, yielding $OPT=n^2$. In Contrast, any EF1 allocation gives exactly one good to each agent, thus achieving welfare less than $n+1$, this implies $\Omega(n)$ bound.



$\because$ EF1 $\Rarr$ Prop1, thus the PoF also holds for Prop1. (PoF for Prop1 is $\Theta(\sqrt n)$ for scaled additive valuations and $\Theta(n)$ for unscaled additive valuations.

## Price of $1/2$-MMS

~~I don't want to dive deep into the mathematical proofs~~:face_with_head_bandage::sweat:

~~Das ist zu schade. Ich bin sehr müde.~~

:dart::thinking:**Theorem 2**. The price of $1\over2$-MMS is $\Theta(\sqrt n)$ for scaled additive valuations and $\Theta(n)$ for unscaled additive valuations.

### An Absolute Welfare Guarantee

First, this paper shows that when agents have additive valuations, there always exists a $1\over2$-MMS allocation $\mathcal A$ with social welfare $SW(\mathcal A)\ge{1\over3n}\sum_{i=1}^nv_i([m])$. Algorithm `ALG-MMS-ABS` computes such an allocation efficiently.

The result is two fold

1. For both scaled and unscaled valuations, this establishes that the price of $1\over2$-MMS is $O(n)$. (trivial)
2. For unscaled valuations, this is precisely the bound.
3. For scaled valuations, we need to improve this to $O(\sqrt n)$.



Pseudo code for `ALG-MMS-ABS`:

Input: A fair division instance $\mathcal I$, additive valuations $\{v_i\}_{i\in[n]}$.

Output: A $1\over2$-MMS allocation $\mathcal B$ with $SW(\mathcal B)\ge{1\over3n}\sum_{i=1}^nv_i([m])$.

1. Initialize set of agents $A=[n]$, set of goods $G=[m]$ and bundles $B_i=\empty$ for all $i\in[n]$.
2. while there exists agent $i\in A$ and good $g\in G$ s.t. $v_i(g)\ge{1\over2|A|}v_i(G)$ do
   - set $(i',g')\in\arg\max_{(i,g)\in A\times G:v_i(g)\ge{1\over2|A|}v_i(G)}v_i(g)$
   - set $B_{i'}=\{g'\}$ and update $A\gets A\diagdown\{i'\}$ along with $G\gets G\diagdown\{g'\}$
3. end while
4. efficiently compute a Prop1 allocation $(B_i)_{i\in A}$ of the fair division instance $\lang A,G,\{v_i\}_{i\in A}\rang$
5. return allocation $\mathcal B=(B_1,\cdots,B_n)$.

The algorithm is a refinement of the algorithm of Amandatidis et al. for computing a $1\over2$-MMS allocation: in while loop, we use $\arg\max$ to break ties. They had proven the algorithm always produce $1\over2$-MMS. It remains to prove that the desired welfare holds for results.

To analyze the bounds, we let $t$ denote the number of iterations of the while loop in `ALG-MMS-ABS`. We re-index the agents s.t. agent $1$ through $t$ receive, in order, a good in this while loop. Each agent $i\in[t]$ is assigned a good in $i$-th iteration of the loop. The remaining $n-t$ agents receive a bundle through the Prop1 allocation computed after while loop.

:thinking:**Lemma 5**. Consider a fair division instance $\mathcal I$ with the re-indexing of the agent method above, and $t$ denoting the number of agents assigned a good in while loop of algorithm. Let $\mathcal B=(B_1,\cdots,B_k)$ denote the allocation returned by the algorithm, then we have
$$
\sum_{k=1}^{i-1}{1\over n-k+1}\cdot v_k(B_k)+{1\over n-i+1}\cdot v_i([m]\diagdown\cup_{k=1}^{i-1}B_k)\ge{1\over n}\cdot v_i([m])
$$
for all $i\in[t]$, and
$$
\sum_{k=1}^t{1\over n-k+1}\cdot v_k(B_k)+{1\over n-t}\cdot v_i([m]\diagdown\cup_{k=1}^tB_k)\ge{1\over n}\cdot v_i([m])
$$
for all $i\in[n]\diagdown[t]$.

> For each agent $i\in[n]$, we establish the following inequality for every index $j\in\{0,\cdots,\min\{i-1,t\}\}$ via an ==induction== on $j$. Then instantiating $j=\min\{i-1,t\}$ yields the desired result.

:thinking:**Lemma 6**. Given any fair division instance $\mathcal I$ with additive valuations, `ALG-MMS-ABS` efficiently computes $1\over2$-MMS allocation $\mathcal B$ with social welfare $SW(\mathcal B)\ge{1\over3n}\sum_{i=1}^nv_i([m])$.

> Proof using lemma 5.
>
> By summing the inequality in lemma 5, across all agents yields the desired welfare bound.

### The Case of High Optimal Welfare

This paper proposes  new algorithm, `ALG-MMS-HIGH`, to compute $O(\sqrt n)$ welfare on $1\over2$-MMS.

Computing $MMS_i$ for each agent $i$ is NP-hard, there exists a polynomial-time approximation scheme (PTAS) for it. For a fixed $\epsilon\in(0,1)$, this PTAS can compute an estimate $Z_i\in[(1-\epsilon)MMS_i,MMS_i]$ for each agent $i$ in polynomial time. We pass these estimates as input to `ALG-MMS-HIGH`, which runs in polynomial time and yields a $({1\over2}-\epsilon)$-MMS allocation with the desired welfare guarantee.

:thinking:**Lemma 7**.

Given a fair division instance $\mathcal I$ with scaled additive valuations, and, for a fixed $\epsilon\in[0,1)$. an estimate $Z_i\in[(1-\epsilon)MMS_i,MMS_i]$ for each agent $i$, `ALG-MMS-HIGH` efficiently computes a $({1\over2}-\epsilon)$-MMS allocation $\mathcal B$ with the property that $3\sqrt n\cdot SW(\mathcal B)+4\sqrt n\ge OPT$. ($OPT$ is the optimal social welfare in $\mathcal I$)

> Proof by lemma 9 and lemma 10.

The agents are partitioned into 3 sets, $\Gamma_{MMS},\Gamma_{single},\Gamma_{hard}$, where agent $i$ is paced into a set depending on how the estimate $Z_i$ of $MMS_i$ relates to $v_i(W^*_i)$.
$$
\Gamma_{MMS}=\{i\in[n]:Z_i\ge{2\over3\sqrt n}v_i(W^*_i)\}\\
\Gamma_{single}=\{i\in[n]:Z_i\lt{2\over3\sqrt n}v_i(W_i^*)\and\exist g\in W_i^*,v_i(g)\ge{1\over3\sqrt n}v_i(W^*_i)\}\\
\Gamma_{hard}=\{i\in[n]:Z_i\lt{2\over3\sqrt n}v_i(W_i^*)\and\forall g\in W_i^*,v_i(g)\lt{1\over3\sqrt n}v_i(W_i^*)\}
$$
For an agent $i\in\Gamma_{single}$, giving her a single good from $\arg\max_{g\in W_i^*}v_i(g)$ gives her value at least ${1\over3\sqrt n}v_i(W_i^*)\gt{1\over2}Z_i$. Thus the algorithm begins by giving each such agent $i$ her most valuable good from $W_i^*$ and adding her directly in set $P$ (permanent). (another set is $T$ temporary)

Next, for $i\in\Gamma_{MMS}$, giving her value at least ${1\over2}\cdot Z_i$ also guarantees that her value is at least ${1\over3\sqrt n}v_i(W_i^*)$; thus the algorithm solely aims to give such agents ${1\over2}\cdot Z_i$ value.

The situation is more tricky for agents in $\Gamma_{hard}$.

:thinking:**Lemma 8**. Let $\mathcal I$ be a fair division instance with additive valuations. Let $\ell\in[n]$ be an agent. Let $\{B_a\}_{a\in S}$ be a collection of bundles assigned to a subset of agents $S\subseteq[n]\diagdown\{\ell\}$ with the property that, for all $a\in S$, we either have $|B_a|=1$ or $v_\ell(B_a)\le MMS_\ell$. Then the value of agent $\ell$ for the unassigned goods satisfies
$$
v_\ell([m]\diagdown\cup_{a\in S}B_a)\ge(n-|S|)\cdot MMS_\ell
$$

> ~~omitted~~

:thinking:**Lemma 9**. The following hold at any stage during the execution of `ALG-MMS-HIGH`:

1. $P\cap T=\empty$.
2. If $i\in P,v_i(B_i)\ge{1\over3\sqrt n}v_i(W_i^*)$ and $B_i$ is never updated afterwards.
3. If $i\in P\cup T,v_i(B_i)\ge{1\over2}\cdot Z_i$, and $i$ is never removed from $P\cup T$ afterwards.
4. $T\subseteq\Gamma_{hard}$.

> ~~omitted~~

:thinking:**Lemma 10**. `ALG-MMS-HIGH` terminates in polynomial time, and at its termination, the following hold.

1. $P\cup T=[n]$
2. $|T|\le4\sqrt n$

> Each iteration of the loop, a new agent is added to $P\cup T$, and will never be removed once added.
>
> ~~proof for 2nd proposition omitted.~~

### Proof of Theorem 2

For **unscaled valuations**: lemma 6 shows that `ALG-MMS-ABS` returns a $1\over2$-MMS allocation $\mathcal B$ with social welfare $SW(\mathcal B)\ge{1\over3n}\sum_{i=1}^nv_i([m])$. Because OPT is trivially upper bounded as $OPT\le\sum_{i=1}^nv_i([m])$, we have that the price of $1\over2$-MMS is at most $3n=O(n)$. (To show this bound is tight, this paper gives an instance)

For **scaled valuations**: lemma 6 implies `ALG-MMS-ABS` returns a $1\over2$-MMS allocation $\mathcal B$ with $SW(\mathcal B)\ge{1\over3n}\sum_{i=1}^nv_i([m])={1\over3}$ for scaled valuations. Hence, if $OPT\le5\sqrt n$, then the $1\over2$-MMS allocation returned by `ALG-MMS-ABS` gives a $15\sqrt n$ approximation to the optimal social welfare.

Otherwise, suppose $OPT\ge5\sqrt n$. Then by lemma 7, we know that `ALG-MMS-HIGH` returns an allocation with social welfare $SW(\mathcal B)\ge{OPT-4\sqrt n\over 3\sqrt n}\ge{OPT-4/5OPT\over3\sqrt n}-{OPT\over15\sqrt n}$. Hence, in this case, the $1\over2$-MMS  allocation returned by `ALG-MMS-HIGH` provides a $15\sqrt n$ approximation to $OPT$.

(For lower bound, this paper also gives an instance)

## Discussion

Price of EF1: $O(\sqrt n)$ when valuations are subadditive

- $\Theta(\sqrt n)$ even if valuations are additive.

Price of $1\over2$-MMS is $\Theta(\sqrt n)$ when agent valuations arer additive.

Research outlook:

1. Analyze the price of $1\over2$-MMS under more general valuation classes.

   Consider other combinations of fairness and efficiency guarantees:

   - EF1 + PO (Pareto optimality), EF1 + fPO (fractional Pareto optimality)
   - Price of $\alpha$-MMS for $\alpha\gt{1\over2}$. At least $3\over4$-MMS always exists.
   - Price of $\alpha$-GMMS. GMMS $\Rarr$ MMS. $1\over2$-GMMS always exists.
   - Price of EFX. It remains to be solved the existence of EFX for agents more than 3.

2. Analyze the PoF when the items are chores, （简单来说就是物品的估值可以为负）or a mixture of goods and chores.

The formal definitions fairness and corresponding prices are well-understood within fair division, but they are much less explored within other scenarios, such as social choice theory (voting or matching).

# Paper on MMS

Additive and submodular valuation

> A valuation function is considered **submodular** if adding an item to a smaller set increases its value more than adding the same item to a larger set.

$2\over3$-MMS always exists and can be found in polynomial time. This paper complement these results by developing a simple and efficient algorithm that achieves the same approximation guarantee.

This paper approximates MMS under *submodular valuations*. It shows that when the valuations of agents are **non-negative, monotone and submodular**, then a $0.21$-MMS is guaranteed to exist.
$$
0.21\approx{1\over3}(1-{1\over e})
$$


## Introduction

:bulb:**Personal comment**: A very clear explanation **original idea** for the Maximin Share: Let agent $i$ partition the goods into $n$ bundles, then let others choose before $i$ choose a bundle. Then agent $i$ has incentive to **maximize the the bundle with minimal value** she partitioned.

This paper extends the previous work into both *additive* and *submodular* valuations.

### Main Results and Techniques

#### Additive Valuations

This paper presents a polynomial time algorithm that finds a $2\over3$-MMS under additive valuations. The algorithm is *simple* and *combinatorial*.

The algorithm consists of two parts. First, reducing the problem of finding a maximin fair allocation to a restricted setting where the agents value goods in the same order. This reduction preserves maximin fairness in the sense that the $g$-th good in the reduced instance corresponds to the right to select a good in round $g$.

With the reduction, this paper develops an efficient algorithm to find $2\over3$-MMS allocation for ordered instances. To obtain the result, do some modification on **envy-graph** algorithm.

(An arbitrary EF1 doesn't necessarily imply approx-MMS) But with the order, an instance of the EF1 algorithm indeed finds an allocation with desired maximin fairness guarantee.

#### Submodular Valuations

When valuations are non-negative, monotone and submodular, then a $0.21$-MMS is guaranteed to exist and can be efficiently computed via a simple round-robin algorithm.

This paper analyzes this simple algorithm by employing the concept of **multilinear extensions**.

For each agent $i$, the expected value of a random subset is at least $1-{1\over e}$ times the maximin share of $i$.

Since computing the maximin share is NP-hard, the algorithm works with estimates $\tau_i$ for agent $i$ of the maximin shares. Each estimate $\tau_i$ is initialized to be greater than the corresponding maximin share. The methods ensures none of them significantly smaller than the maximin shares.

In our algorithm, we first perform a **preprocessing step** wherein high-valued goods are assigned as singleton bundles. In particular, if for an agent $i$ there exists an unallocated good of value at least $0.21\tau_i$, ==then we allocate the good to the agent and remove $i$ (and the now assigned good) from consideration.== The leftover (low-valued) goods are partitioned among the remaining agents (who have not received a good yet) in a **round-robin manner**. In each round, the participating agents greedily select an unallocated good one after the other, i.e. in each round, every agent $i$ is assigned a good that leads to the maximum possible increase in $i$'s valuation.

Under submodular valuations, a uniformly random allocation is $(1-{1\over e})$ times $i$'s maximin share.



Remark: additive $\subseteq$ submodular. We can get stronger approximation guarantee on additive valuations.

Chores (goods with negative values): $4\over3$-approximate maximin fair allocation can be efficiently computed when agents have additive valuations for chores.

The envy graph algorithm is applied similarly between goods version and chores version.

### Related Work and Subsequent Work

~~omitted~~

## Notation and Preliminaries

Agents $[n]$, goods $[m]$.

Valuation of an agent $i$ over a set $S\in[m]$ goods: $v_i(S)$

Additive valuations: $v_i(A+B)=v_i(A)+v_i(B),A\cap B=\empty$.

Non-negative valuation: $v_i(S)\ge0,v_i(\empty)=0$.

Monotonicity: $v_i(A)\le v_i(B)\forall A\subseteq B$.

Submodularity: $v_i(B\cup\{g\})-v_i(B)\le v_i(A\cup\{g\})-v_i(A)\forall A\subseteq B$

> Allocate good in smaller bundles yields higher increase.

Marginal values w.r.t. $H\subseteq[m]$: $f_H(S)=f(H\cup S)-f(H)\forall S\subseteq [m]$

All the $n$-partition w.r.t. $[m]$: $\Pi_n([m])$.

Definition on Maximin Share, $\alpha$ approximation on Maximin Share: ~~omitted here~~

Definition on EFX: ~~omitted here~~

## Additive Valuations

In this section, the paper presents an **new** algorithm that efficiently finds a $2\over3$-MMS under **additive valuations**.

Contribution: the algorithm is simple and combinatorial.

:dart::thinking:**Theorem 1**. Given a set of $n$ agents with additive valuations $\{v_i\}_{i\in[n]}$ for a set of $m$ indivisible goods, we can find a partition $\mathcal A$ in polynomial time that satisfies
$$
v_i(A_i)\ge{2n\over3n-1}\mu_i
$$
for all $i\in[n]$. Here $\mu_i$ is the maximin share of agent $i$.

Proof in two parts. First, we show the problem can be reduced to a restricted setting where the agents value the goods in the same order. Second, we develop a $2\over3$-approximation for this restricted setting.

### The Reduction of Bouveret and Lemaître

An instance is an **ordered instance** iff $\exist$ a total ordering $\prec$ over $[m]$ s.t. $\forall i\in[n]$ and $g,g'\in[m]$, s.t. $g\prec g'$, we have $v_{i,g}\ge v_{i,g'}$. i.e. $v_{i,1}\ge v_{i,2}\ge\cdots v_{i,m}$.

Given a fair division $I=([n],[m],\{v_i\}_{i\in[n]})$, the algorithm constructs an ordered instance as follows:

1. For every agent $i$, $\exist$ a permutation $\sigma_i:[m]\rarr[m]$ s.t. $\forall g,g'\in[m]$ with $g\lt g'$, we have $v_{i,\sigma_i(g)}\ge v_{i,\sigma_i(g')}$. Using these permutations, we define a new valuation function $v_i'$ for every agent $i$ by setting $v_{i,g}'=v_{i,\sigma_i(g)}$ for all $g\in[m]$.

   i.e. for $i$, the value of $g$-th good in the new instance is equal to the $g$-th largest valuation of $i$ in the original instance.

   Denote $I'=([n],[m],\{v_i'\}_{i\in[n]})$ as the ordered instance of $I$. Note that we can find the ordered instance of $I$ in $O(nm\log m)$ time.

2. Bouveret and Lemaître showed that if there is an allocation $\mathcal A'$ that guarantees every agent her maximin share in $I'$, then there is an allocation $\mathcal A$ that guarantees every agent her maximin share in $I$.

   Moreover, given a maximin fair allocation $\mathcal A'$ for $I'$, a maximin fair allocation $\mathcal A$ for $I$ can be found in polynomial time.

#### Pseudo code for `ALGBL`

Input: $I$ with $\mathcal A'$ for the ordered instance $I'$.

Output: $\mathcal A$

1. For all $i\in[n]$ and $g\in A_i'$ set $p_g=i$. // this defines a sequence of agents
2. Initialize allocation $\mathcal A$ with $A_i=\empty$, for all $i\in[n]$. And initialize $R\gets[m]$.
3. for $g=1$ to $m$ do
   - pick $k\in\arg\max_{g'\in R}\{v_{p_g}(g')\}$
   - update $A_{p_g}\gets A_{p_g}\cup\{k\}$ and $R\gets R\diagdown\{k\}$
4. return $\mathcal A$

:dart::thinking:**Theorem 2**. Given a maximin fair division instance $I$ and scalars $\{\alpha_i\in\mathbb R\}_{i=1}^n$, let $I'$ be the ordered instance of $I$. If there exists an allocation $\mathcal A'$ that satisfies $v_i'(A_i')\ge\alpha_i$ for all $i\in[n]$, then there exists an allocation $\mathcal A$ s.t. $v_i(A_i)\ge\alpha_i$ for all $i\in[n]$. Furthermore, given $I$ and $\mathcal A'$, `ALGBL` computes the allocation $\mathcal A$ in polynomial time.

> Obviously polynomial time. We only need to show it computes required allocation.
>
> Let $k_g$ denote the good allocated in the $g$-th iteration of the second for-loop in `ALGBL`. Now considering agent $i$, suppose that $g\in A_i'$ then $k_g\in A_i$. Note that, for any $g\neq g'$, since a good is removed from the set $R$ after it is allocated. Before the $g$-th iteration of the second for loop in `ALGBL`, exactly $g-1$ goods had been allocated.
>
> Therefore, $k_g$ is among the top $g$ goods for agent $i$. Hence, the condition that $v_i'(A_i')\ge\alpha_i$ gives us $v_i(A_i)\ge\alpha_i$ for all $i$. Q.E.D.

Corollary: Given a maximin fair division instance $I$ and $\alpha\in\mathbb R$ write $I'$ to denote the ordered instance of $I$ and $\mu_i$ to denote the maximin share of agent $i$ in $I$. If there exists an allocation $\mathcal A'$ satisfying $v_i'(A_i')\ge\alpha\mu_i'$, there also exists $\mathcal A$ satisfying $v_i(A_i)\ge\alpha\mu_i$. And can be computed in polynomial time.

### Envy Graph Algorithm

Envy-graph Elimination algorithm, ~~omitted here~~.

`ALGEG` algorithm returns an EFX allocation. Similar to Top Trading Cycle algorithm.

Pseudo code ~~omitted here~~.

### Majorization

The concept is sued to establishing the desired approximation guarantee.

A multi-set $X$ is related to another multi-set $Y$ iff the prefix sums of the sorted version $X$ are at least as large as the prefix sums of the sorted version of $Y$.

Definition of majorization: A multi-set $X=\{x_i\in\mathbb R|1\le i\le n\}$ is said to majorize another multi-set $Y=\{y_i\in\mathbb R|1\le i\le n\}$ iff
$$
\sum_{i=1}^kx_{(i)}\ge\sum_{i=1}^ky_{(i)}\forall1\le k\lt n\\
and\\
\sum_{i=1}^nx_{(i)}=\sum_{i=1}^ny_{(i)}
$$
Here $x_{(i)}$ and $y_{(i)}$ is the $i$-th largest element of $X$ and $Y$ respectively.

:thinking:**Proposition 1**. (easy)

Let $v,v'\in\mathbb R$ be two elements of a multi-set $A$ of real numbers. In addition, let $u,u'\in\mathbb R$ satisfy $u+u'=v+v'$ and $|u-u'|\lt|v-v'|$. Then $A$ majorizes $(A\diagdown\{v,v'\})\cup\{u,u'\}$.

> This is trivial and obvious.
>
> 因为 $u,u'$ 中的最大值小于 $v,v'$ 中的最大值，这使得 $A$ 的前缀和加到 $v,v'$ 中最大值的时候一定会大于等于 $(A\diagdown\{v,v'\})\cup\{u,u'\}$ 加到相应的位置上的前缀和。

### Proof of Theorem 1

It remains to show that $v_1(A_1)\ge{2n\over3n-1}\mu_1$. (An analogous argument establishes the desired bound, $v_i(A_i)\ge{2n\over3n-1}\mu_i\forall i\in[n]$.

Let $\mathcal A$ be the return of `ALGEG`. Consider the set of goods that have value $\ge{1\over2}v_i(A_1)$, specifically define $\tau=\arg\min\{j|v_1(g_j\le{1\over2}v_1(A_1)\}$. Let $H$ denote the set of high valued goods $H=\{g_1,g_2,\cdots,g_{\tau-1}\}$. In addition, write $\mathcal P=(P_1,P_2,\cdots,P_n)$ to denote the partial allocation that `ALGEG` computes for $H$.

Let $t$ denote the smallest iteration count at which `ALGEG` assigns a good to a bundle of size 2. Hence, every bundle in the partial allocation  of the first $t-1$ goods is of size at most 2.

`ALGEG` updates the partial allocation by adding goods to existing bundles. This implies that the cardinalities of bundles is always increasing. Thus for each final $A_j$, there $\exist$ $P_j$ in the partial allocation $\mathcal P$ s.t. $P_j\subseteq A_j$. $v_1(A_1)\ge v_1(P_1)$.

Let $\mathcal Q$ be the partial allocation considered by `ALGEG` at the beginning of the $t$-th iteration. The definition of $t$ ensures that $|\mathcal Q_i|\le2\forall i\in[n]$. In the following, we will show that $\tau\le t$ ,then we get bound $|P_i|\lt2\forall i\in[n]$. This leas to the inequality $\tau\le2n$.

......(已经有两个物品了，在添加这个物品时，由于根据算法越到后面添加的越小，所以 $v_1(A_1)\ge2v_1(g_t)$。根据定义 $g_\tau$ 是最小标号的满足不等式的，所以 $\tau\le t$)

:heavy_check_mark:Claim 1. In the partial allocation returned by `ALGEG`, $\mathcal A$, the following inequality holds for all bundles $A_i$ that satisfy $A_i\cap\{g_\tau,\cdots,g_m\}\neq\empty$
$$
v_1(A_1)\ge{2\over3}v_1(A_i)
$$

> Proof by property of EFX: $v_1(A_1)\ge v_1(A_i)-v_1(g_a)$. $v_1(A_1)\ge2v_1(g_\tau)\ge2v_1(g_a)$.

:heavy_check_mark:Claim 2. The partial allocation $\mathcal P$ consists of the following bundles:
$$
\{g_1\},\{g_2\},\cdots,\{g_{n-h}\},\{g_{n-h+1},g_{n+h}\},\{g_{n-h+2},g_{n+h-1}\},\cdots,\{g_{n-1},g_{n+2}\},\{g_n,g_{n+1}\}
$$

> Proof by analyzing behavior of the algorithm.

:heavy_check_mark:Claim 3. Every partial allocation of $H$ majorizes $\mathcal P$.

> Proof by using proposition 1.
>
> ......
>
> $\mathcal P$ minimize the sum of the largest $t$ bundles.



Let $\ell$ be # of bundles in $\{A_2,A_3,\cdots,A_n\}$, that do not contain any good with index greater than $\tau-1$. For every such bundle $A_i$, there exists a unique bundle $P_j$ in $\mathcal P$ s.t. $A_i=P_j$. By reindexing, that $P_{n-\ell+1},P_{n-\ell+2},\cdots,P_n$ are these $\ell$ bundles in $\mathcal A$ that do not satisfy the condition in claim 1.

Let $\mathcal M$ denote a partition of $[m]$ that achieves the maximin share for player $1$. Consider the partial allocation $\{M_i\cap H\}_{i=1}^n$ and index the set s.t. valuation is in ascending order. Claim 3 implies $\{M_i\cap H\}_{i=1}^n$ majorizes $\mathcal P$. Hence, $\sum_{i=n-\ell+1}^nv_1(M_i\cap H)\ge\sum_{i=n-\ell+1}^nv_1(P_i)$. This inequality and the fact the valuations are monotone lead to the following useful bound:
$$
\sum_{i=n-\ell+1}^nv_1(M_i)\ge\sum_{i=n-\ell+1}^nv_1(P_i)
$$
Recall that $P_{n-\ell+1},P_{n-\ell+2},\cdots,P_{n}$ are bundles in $\mathcal A$, and the remaining $n-\ell$ bundles of $\mathcal A$ satisfy the inequality $v_1(A_1)\ge{2\over3}v_1(A_i)$. Since $v_1$ is additive, we have $\sum_{i=1}^nv_1(M_i)=\sum_{i=1}^nv_1(A_i)$. Therefore, inequality above provides the following bound:
$$
\sum_{i=1}^{n-\ell}v_i(M_i)\le\sum_{i=1}^{n-\ell}v_1(A_i)\\
\le v_1(A_1)+(n-\ell-1){3\over2}v_1(A_1)
$$
Overall, this inequality implies that $v_1(A_1)$ is at least $2(n-\ell)\over3n-1$ times the average of the $n-\ell$ sets $M_{\ell+1},\cdots,M_n$. Hence, $v_1(A_1)]\ge{2n\over3n-1}\min_{j\in[n]}v_i(M_j)={2n\over3n-1}\mu_1$. This completes the proof.

## Submodular Valuations

$0.21$-MMS is guaranteed to exist and can be found in polynomial time for submodular valuations.

### Finding an Approximate Maximin Fair Allocation

We execute the subroutine `RoundRobin` with threshold $\tau_i$ instead of the actual maximin share $\mu_i$. 

`RoundRobin` takes as input threshold, $\tau_i$s, allocates high-valued goods as singleton bundles. And then partition the remaining goods in a round-robin fashion.

This paper argues: for each agent $i$, if $\tau_i\le\mu_i$, the bundle $P_i$ allocated to $i$ by `RoundRobin` satisfies $v_i(P_i)\ge\frac{1}{3}(1-\frac{1}{e})\tau_i$. 

**This guarantee holds independently for each agent $i$.**

Algorithm `ALGSUB` starts by setting thresholds $\tau_i$s to be more than the maximin shares. Then, it decreases the threshold for agent $i$, if the partition returned by `RoundRobin` does not satisfy $v_i(P_i)\ge\frac{1}{3}(1-\frac{1}{e})\tau_i$ i.e. $\tau_i\gt\mu_i$, hence decreasing the threshold is justified.

Pseudo code for `RoundRobin`: input include $\{\tau_i\}$

Pseudo code for `ALGSUB`: process of decreasing $\tau_i$

:question:==How to initialize $\tau_i$???==

:dart::thinking:**Theorem 3**. Given a MMS allocation with non-negative, monotone, submodular valuations, the `ALGSUB` finds, in polynomial time, an allocation satisfies $v_i(P_i)\ge0.21\mu_i$ for all $i\in[n]$. Here $\mu_i$ is the maximin share of agent $i$.

Theorem 3 is about the main result for submodular valuations.

> Proof by lemma 1.

:thinking:**Lemma 1**.

For a given a maximin fair division instance with $m$ indivisible goods and $n$ agents with non-negative, monotone and submodular valuations, let $\mathcal P$ be the allocation returned by `RoundRobin` with threshold $\tau_i\ge0$. Then, for each agent $i$ whose input threshold satisfies $\tau_i\le\mu_i$, it holds that
$$
v_i(P_i)\ge\frac{1}{3}(1-\frac{1}{e})\tau_i\ge0.21\tau_i
$$

> Fix an agent $i$ and under assumption that $\tau_i\le\mu_i$.
>
> Proof by analyzing behavior of `RoundRobin`.
>
> Very sophisticated:open_mouth:

### Multilinear Extensions

Definition of multilinear extension: For a function $f:2^{[m]}\mapsto\mathbb R$, the multilinear extension $F:[0,1]^{[m]}\mapsto\mathbb R$ is defined as follows:
$$
F(x)=\mathbb E_{R\sim x}[f(R)]=\sum_{R\subseteq[m]}f(R)\left(\prod_{g\in R}x_g\prod_{g\in[m]\diagdown R}(1-x_g)\right)
$$
sampling from $x\in[0,1]^m$ corresponds to selecting a random subset $R\subseteq[m]$ in which each $g\in[m]$ appears independently with probability $x_g$.

**Fractional allocation**: $n$-tuple $\chi=(x^1,x^2,\cdots,x^n)$ with $x_i\in[0,1]^m$ iff $\sum_{i=1}^nx_g^i\le1$ for all $g\in[m]$.

A binary fractional allocation corresponds to a partial allocation.

:thinking:**Lemma 2**. (Proportionality) Let $v_i:2^{[m]}\mapsto\mathbb R_+$ denote non-negative, monotone, submodular valuations of agents $i\in[n]$ over $m$ indivisible goods. Then the fractional allocation $\omega=(u^1,u^2,\cdots,u^n)$ in which $u^i=(\frac{1}{n},\frac{1}{n},\cdots,\frac{1}{n})\in[0,1]^m$ for all $i\in[n]$, satisfies $V_i(u^i)\ge(1-\frac{1}{e})\mu_i$ for all $i\in[n]$. Here $V_i:[0,1]^m\mapsto\mathbb R_+$ is the multilinear extension of $v_i$, and $\mu_i$ is the maximin share of agent $i$.

> ~~hard proof, omitted~~

## Conclusion

Algorithm proposed in this paper: approximately fair and sequenceable.

Sequenceable allocation: allocations that can be obtained by ordering the agents and then letting them select their most valued unallocated good one after the other.