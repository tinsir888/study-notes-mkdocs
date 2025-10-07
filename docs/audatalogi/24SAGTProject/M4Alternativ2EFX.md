---
title: Fair Division 4 Alternatives to EFX
author: tinsir888
date: 2024/3/8
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: a41b3387
---

Objective: Investigate alternative fairness concepts for allocating indivisible items.

Activities: Read and present the paper [*New Fairness Concepts for Allocating Indivisible Items*](https://www.ijcai.org/proceedings/2023/284) by Ioannis et al. IJCAI2023

# :candy:Bonus: Methodology

After reading some papers in fair division with indivisible items. I have come up with a naive :eyeglasses: methodology about research in this field.

1. **Definition of Fairness Notions:** This initial phase entails defining various fairness notions and exploring how these concepts can be adapted or extended to the context of indivisible items.
2. **Existence of Fair Allocation:** Here, the focus lies on establishing the existence of fair allocations. This step often involves rigorous theorem proving or setting forth conditions under which fair allocations are guaranteed to exist. Mathematical analysis, combinatorial arguments, and computational complexity analysis may all come into play here. (I recognize this to be a particularly challenging step from my current perspective.)
3. **Computational Feasibility:** Assuming the existence of fair allocations is assured, the next question revolves around the ease of computing such allocations. The objective here is to ascertain whether these allocations can be computed efficiently, ideally within polynomial time complexity. This phase typically involves grappling with a plethora of lemmas and theorems, much akin to the challenges encountered in Step 2.
4. **Exploration of Latent Relations Among Fairness Notions:** Finally, it's crucial to delve into the underlying relationships among different fairness notions, uncovering their interconnectedness and potential implications.

# Abstract

Fair notions:

- EFX existence for more than 3 agents - open problem

- MMS not always exist

This paper proposes two new fairness concepts:

- epistemic EFX, abbr. EEFX
- minimum EFX share fairness, abbr. MXS

EEFX and MXS always exist and can be computed efficiently for additive valuations.

# Introduction

## Contribution

3 desideratum:

1. Intuitively close to envy-freeness and proportionality
2. always feasible
3. efficiently computable (polynomial time)

## EEFX

Epistemic EFX. An allocation is EEFX if $\forall$ agent, it is possible to shuffle the items in the remaining bundles so that she becomes "EFX-satisfied".

~~An example given.~~

EEFX is particularly relevant in environments in which each agent knows the allocation instance but her knowledge of the allocation is limited to the contents of her bundle.

The the agent is as **optimistic** as possible to evaluate her bundle. ~~Which means she doesn't know the partition and allocation for items not allocated to her.~~

 She compares it with each bundle in the allocation of the **remaining items that would be best possible for her.**

In contrast to EEFX: EF, EF1, EFX are *knowledge-sensitive*.

## MXS

Minimum envy-freeness up to any good share

It's a **threshold** for each agent.

The minimum EFX share of an agent is the minimum value she has among all allocations in which she is EFX-satisfied.

Similar in spirit to PROP and MMS.

## Relations Among Them

Every MMS is EEFX, every EEFX is MXS, every MXS is PROP1.
$$
MMS\subset EEFX\subset MXS\subset PROP1
$$
This paper also presents a polynomial-time algorithm for EEFX (ofc MXS).

Key ideas:

- ordered instance
- envy-cycle elimination
- same algorithm in [Paper on MMS (read last week)](https://tinsir888.github.io/posts/cc139c6f.html) to compute $\frac{2}{3}$-MMS.

- computing the minimum EFX share is NP-hard. Thus any efficient algorithm for computing MXS allocation cannot make use of the minimum EFX share.

# Definitions and Notation

$[k]$ denotes $\{1,2,\cdots,k\}$

Fair division instance $\mathcal I$:

- $n$ agents $[n]$
- $m$ indivisible items $[m]$
- each agent $i$ has $v_i(g)\forall g\in[m]$. 
- **additive** valuations

Ordered instance: The valuations of all agents for the items have a common ordering, i.e.,
$$
\forall i,i'\in[n]\land g,g'\in[m],v_i(g)\ge v_i(g')\Leftrightarrow v_{i'}(g)\ge v_{i'}(g')
$$
An allocation $X=(X_1,X_2,\cdots,X_n)$ in which $X_i\cap X_j=\emptyset\forall i\neq j$ and $\bigcup_{i=1}^nX_i=[m]$.

$\Gamma$ denotes the set of all allocations of a given instance.

Definition of EFX: ~~omitted~~

Definition of **EEFX** and **EEFX certificates**: For a fair division instance, an allocation $X$ is called EEFX if for every agent $i$, there exists an allocation $Y$ s.t. $Y_i=X_i$ and agent $i$ is EFX-satisfied with $Y$. We refer to such an allocation $Y$ as an **EEFX certificate** of agent $i$ for bundle $X$.

In other words, an allocation $X$ is EEFX if there exists an EEFX certificate $\forall$ agent w.r.t. her bundle in $X$.

Trivially, EFX is EEFX: $EFX\subset EEFX$.

Definition of MMS: ~~omitted~~

Definition of **EFX-satisfied** w.r.t. $i$:
$$
EFX_i=\{Z\in\Gamma:v_i(Z_i)\ge\max_{g\in Z_j:v_i(g)\gt0}v_i(Z_j\diagdown\{g\}),\forall j\in[n]\}
$$
Definition of **MXS**: For a fair division instance, minimum EFX share for $i$ is
$$
MXS_i=\min_{Z\in EFX_i}v_i(Z_i).
$$
$Z$ is an MXS allocation if $v_i(Z_i)\ge MXS_i\forall i\in[n]$.

Definition of PROP1: ~~omitted~~

# Relations to Other Fairness Concepts

$$
MMS\stackrel{\text{Theorem 2}}\Longrightarrow EEFX\stackrel{\text{Theorem 1}}\Longrightarrow MXS\stackrel{\text{Theorem 3}}\Longrightarrow PROP1
$$

:dart::thinking:**Theorem 1**. $EEFX\Rightarrow MXS$.

>Let $X$ denote an EEFX allocation.
>
>Fixing an agent $i$, we will prove $v_i(X_i)$ is at least as high as her minimum EFX share.
>
>By definition, $X$ is EEFX, $\exists$ an EEFX certificate $Y=(Y_1,\cdots,Y_n)$ for agent $i$ s.t. $Y_i=X_i$ and $Y\in EFX_i$, thus
>$$
>v_i(X_i)=v_i(Y_i)\ge\min_{Z\in EFX_i}v_i(Z_i)=MXS_i.
>$$
>Q. E. D.

$MXS\not\Rightarrow EEFX$. ~~By giving an example.~~

:dart::thinking:**Theorem 2**. $MMS\Rightarrow EEFX$.

> For an allocation $Z$ and an agent $i$.
>
> $r^{Z,i}$ denotes the $(n-1)$-entry vector with entries the values $v_i(Z_j)$ for $j\in[n]\diagdown\{i\}$, sorted in non-decreasing order, i.e., the vector $r^{Z,i}=\langle r_1^{Z,i},r_2^{Z,i},\cdots,r_{n-1}^{Z,i}\rangle$ satisfies $r_t^{Z,i}\le r_{t+1}^{Z,i}$ for $t\in[n-2]$.
>
> Let $X$ be an MMS allocation in the fair division instance and $i\in[n]$ any agent. Let $Y$ be an allocation with $Y_i=X_i$ so that $r^{Y,i}$ is lexicographically minimum.
>
> It remians to prove that: $Y$ is an EEFX certificate for agent $i$ and bundle $X_i$, which means that the allocation is also EEFX.
>
> - Assume otherwise. Then by definition, there exists $j_1\in[n]\diagdown\{i\}$ so that for the item $g\in Y_{j_1}$ for which agent $i$ has the minimum non-zero value among the items in $Y_{j_1}$, it holds
>   $$
>   v_i(X_i)\lt v_i(Y_{j_1})-v_i(g)
>   $$
>
> - Now, assume that $v_i(Y_j)\gt v_i(X_i)$ for every $j\in[n]\diagdown\{i\}$. Then for the allocation $Z'$ that is obtained from $Y$ after removing item $g$ from bundle $Y_{j_1}$ and adding it to bundle $Y_i$, we obtain an allocation in which $v_i(Z_j')\gt v_i(X_i)\ge MMS_i$ for every $j\in[n]$. This follows by assumption for $j\in[n]\diagdown\{i,j_1\}$, since $v_i(g)\gt0$ for $j=i$, and by above inequality for $j=j_1$ since $v_i(Z_{j_1}')=v_i(Y_{j_1})-v_i(g)\gt v_i(X_i)$. The existence of allocation $Z'$ contradicts the fact that allocation $X$ is MMS.
>
> - So there must be $j_2\in[n]\diagdown\{i,j_1\}$ s.t. $v_i(Y_{j_2})\le v_i(X_i)$. Now consider the allocation $Z''$ obtained after removing item $g$ from bundle $Y_{j_1}$ and adding it to bundle $Y_{j_2}$. Notice that $v_i(Z''_j)=v_i(Y_j)$ for $j\in[n]\diagdown\{j_1,j_2\}$, $v_i(Z''_{j_1})=v_i(Y_{j_1})-v_i(g)\le v_i(Y_{j_1})$ and $v_i(Z''_{j_2})=v_i(Y_{j_2})+v_i(g)\le v_i(X_i)+v_i(g)\le v_i(Y_{j_1})$, using the definition of $j_2$ and inequality above, $r^{Z'',i}$ is lexicographically smaller than $r^{Y,i}$ and furthermore, $Z''_i=X_i$, contradicting the assumption on $Y$. Q. E. D.

EEFX allocations always exist, but not for MMS.

:dart::thinking:*​*​**Theorem 3**. An MXS allocation in a fair division instance is also PROP1.

>Consider a fair division instance and let $X$ be an MXS allocation.
>
>The proportionality up to one good constraints are satisfied for every agent $i$ with $MXS_i=PS_i$.
>
>Now, consider an agent $i$ with $MXS_i\lt PS_i$, we will show $X$ satisfies the PROP1 constraints for agent $i$ as well.
>
>Assume otherwise: $v_i(X_i)\lt PS_i-v_i(g)$ for every item $g\in[m]\diagdown X_i$. Let $Y$ be an allocation in $EFX_i$ s.t. $v_i(Y_i)=MXS_i\lt PS_i$:
>
>- Since $\sum_{j\in[n]}v_i(Y_j)=n\cdot PS_i$, there $\exists k\in[n]\diagdown\{i\}$ s.t. $v_i(Y_k)\gt PS_i$.
>
>- By assumption inequality, we have $v_i(Y_k)\gt v_i(X_i)$, meaning that $\exists g^*$ that belongs to $Y_k$ but not to $X_i$. By the definition of $EFX_i$, we have
> $$
>  MXS_i=v_i(Y_i)\ge v_i(Y_k)-v_i(g^*)\gt PS_i-v_i(g^*)
> $$
>  and using assumption inequality, we get
> $$
>  v_i(X_i)\lt PS_i-v_i(g^*)\lt MXS_i,
> $$
>  contradicting the fact that allocation $X$ is MXS.

The opposite implication is not true.

# Existence and Efficient Computation of EEFX

:dart::thinking:**theorem 4**. In any fair division instance, an EEFX allocation exists and can be computed in polynomial time.

## Pseudo-code for Computing EEFX Allocations

Input: A fair division instance $\mathcal I$

Output: An allocation $X$ in $\mathcal I$ that satisfies EEFX

1. $\mathcal I'\gets$ `Order`$(\mathcal I)$

   This step creates an ordered instance $\mathcal I'$ by modifying instance $\mathcal I$. Instance $\mathcal I'$ has the same set of agent as $\mathcal I$. The items of $\mathcal I$ are arbitrarily renamed as $g_1,\cdots, g_m$ and the valuation $v_i'$ of agent $i\in[n]$ is defined as follows:

   - for $j\in[m]$, the valuation $v_i'(g_j)$ of agent $i$ for the item $g_j$ is the $j$-th highest value in the multiset $\{v_i(g_1),\cdots,v_i(g_m)\}$.

2. $X'\gets$ `EvcyCycleElimination`$(\mathcal I')$

3. $L\gets$ `PickingSequence`$(\mathcal I',X')$

   It takes as input instance $\mathcal I'$ and allocation $X'$ and computes the picking sequence $L=[L_1,\cdots,L_m]$ as follows:

   - For $j\in[m]$, $L_j$ is the agent who gets item $g_j$ in allocation $X'$ i.e., $g_j\in X'_{L_j}$

4. $X\gets$ `Pick`$(\mathcal I,L)$

   This step is executed with input the instance $\mathcal I$ and picking sequence $L$ to compute the allocation $X$ as follows:

   - Pick runs $m$ rounds, one for each item.
   - In round $j$, agent $L_j$ picks the highest-valued item that has not been allocated to any agent in round before.

5. return $X$



To prove theorem 4, we need lemma 1,2 and 3.

:thinking:**Lemma 1**. The allocation $X'$ of instance $\mathcal I'$ is EFX.

>The application of the envy cycle elimination algorithm on ordered fair division instances produces an EFX allocation.

:thinking:**Lemma 2**. For every agent $i\in[n]$, there exists a bijection $\pi_i:[m]\rightarrow[m]$ s.t. the following are true:

- $\pi_i(g)\in X_i$ and $v_i(\pi_i(g))\ge v_i'(g)\forall g\in X_i'$.
- $\pi_i(g)\not\in X_i$ and $v_i(\pi_i(g))\le v_i'(g)\forall g\notin X_i'$.

>Let $i\in[n]$ be an agent. We will refer to the item using the remaining $g_1,\cdots,g_m$ used by routine `Order`. Let $\sigma_i:[m]\rightarrow[m]$ be a permutation s.t. $g_{\sigma_i(j)}$ is agent $i$'s $j$-th most valuable item according to valuation function $v_i$.
>
>Formally, for every $j_1,j_2\in[m]$ s.t. $j_1\lt j_2$, we have $v_i(g_{\sigma_i(j_1)})\ge v_i(g_{\sigma_i(j_2)})$, the tie between items $g_{\sigma_i(j_1)}$ and $g_{\sigma_i(j_2)}$ is resolved in favour of $g_{\sigma_i(j_1)}$ during the execution of routine `Pick` in step 4 of algorithm. By the difinition of the ordered instance $\mathcal I'$, it holds that $v_i(g_{\sigma_i(j)})=v_i'(g_j)$ for every $j\in[m]$.
>
>We define $\pi_i$: For every item $g_j\in X_i'$, define $\pi_i(g_j)$ to be item picked in round $j$ of the execution of `Pick` in step 4 of algorithm. For $k=1,\cdots,m-|X_i'|$, consider the item $g$ that is $k$-th in the ordering $g_1,\cdots,g_m$, ignoring the items in $X_i'$. Set $\pi_i(g)$ to be the $k$-th item in the ordering $g_{\sigma_i(1)},\cdots, g_{\sigma_i(m)}$, ignoring the items in $X_i$.
>
>$\pi_i$ is a bijection. By definition of picking sequence $L$, for every item $g_j\in X_i'$, it is agent $i$ who picks at round $j$ of the execution of `Pick` at step 4 of algorithm, and thus $\pi_i(g_j)\in X_i$. The definition of $\pi_i(g_j)$ for every $g_j\not\in X_i'$ ensures that $\pi_i(g_j)\not\in X_i$,
>
>......

:thinking:**Lemma 3**. Allocation $X$ is EEFX.

>Consider agent $i$ and let $\pi_i$ be the bijection define in lemma 2. Define allocation $Y$ with $Y_j=\{\pi_i(g):g\in X_j'\}$ for $j\in[n]$, Since $\pi_i$ is a bijection, allocation $Y$ is well-defined. Also by lemma 2, $Y_i=X_i$. It remains to prove that $Y$ is an EEFX certificate for $i$ with $X_i$.
>
>Let $j\neq i$ and $g^*$ be the item of bundle $X_j'$ s.t. $\pi_i(g^*)$ has minimum non-zero value according to $v_i$. Thus proving $v_i(Y_i)\ge v_i(Y_j\diagdown\pi_i(g^*))$ is enough to complete the proof.
>
>Since $g^*\not\in X_i'$ and $v_i(\pi_i(g^*))\gt0$, lemma 2 implies that $\pi(g^*)\gt0$. Then, the fact that $X'$ is EFX w.r.t. the valuations $v'$ implies
>$$
>v_i'(X_i')\ge v_i'(X_j'\diagdown\{g^*\})
>$$
>Now the properties of $\pi_i$ from lemma 2 yield
>$$
>v_i(Y_i)=\sum_{g\in X_i'}v_i(\pi_i(g))\ge\sum_{g\in X_i'}v_i'(g)=v_i'(X_i')
>$$
>and
>$$
>v_i'(X_j'\diagdown\{g^*\})=\sum_{g\in X_j'\diagdown\{g^*\}}v_i'(g)\ge\sum_{g\in X_j'\diagdown\{g^*\}}v_i(\pi_i(g))\\
>=v_i(Y_j\diagdown\{\pi_i(g^*\})
>$$
>By applying above 3 equations, we get
>$$
>v_i(Y_i)\ge v_i(Y_j\diagdown\{\pi_i(g^*)\}).
>$$

:question:Verifying whether a given allocation is EEFX is currently an open problem.

Corollary: In any fair division instance there exists a Pareto-optimal MXS allocation. Furthermore, an MXS allocation can be computed in polynomial time.

# Computing the Minimum EFX Share

Algorithm computing EEFX allocations is without computing the minimum EFX share of any agent at any point of its execution.

:dart::thinking:**Theorem 5**. Computing the minimum EFX share of the agents in a fair division instance is NP-hard.

> Proof by developing a polynomial time reduction from the NP-hard probelm of `BalancedPartition`:
>
> - Given a set $N$, containing $2t$ positive integer values s.t.
>   $$
>   \sum_{h=1}^{2t}x_h=2T\forall t,T\in\mathbb N
>   $$
>
> - Does there exist a balanced partition of $N$ s.t. an equipartition $(S,N\diagdown S$) of the elements in $N$ (i.e. $|S|=t$) s.t.
>   $$
>   \sum_{h\in S}x_h=\sum_{h\in N\diagdown S}x_h?
>   $$

# Conclusion & Outlook

EEFX & MXS

EFX~0~: $v_i(g)$ can $=0$. Similarly, EEFX~0~, MXS~0~.

MMS does not imply EEFX~0~.

:question:Open problem: Are there EEFX allocations with better MMS guarantees possible? Cam they be computed in polynomial time? Combining EEFX with other fairness properties. Are there EEFX allocations that are also EF1?

Is Pareto-optimal EEFX allocations exist?

EEFX has a low price of fairness.

The main result holds for chores as well.

How about non-additive valuations?