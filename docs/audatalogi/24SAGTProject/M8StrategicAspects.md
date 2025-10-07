---
title: Fair Division 8 Strategic Aspects of Fair Divison
author: tinsir888
date: 2024/5/3
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: fd26edcc
---

Objective: Investigate strategic aspects of fair division, considering Pure Nash equilibria and fairness.

Activities: Review the paper [Allocating Indivisible Goods to Strategic Agents: Pure Nash Equilibria and Fairness](https://pubsonline.informs.org/doi/full/10.1287/moor.2022.0058).

# Abstract

What if the agents are strategic?

Goal: whether there exist mechanisms that have *pure Nash Equilibria*. If so, what is the fairness guarantee for these equilibria?

Focus on EF1, EFX. The answer is positive.

2 algorithms:

- round-robin (computing EF1 allocation). Its pure Nash equilibria are EF1 w.r.t. true values.
- cut and choose algorithm (computing EFX allocation for 2 agents). The corresponding allocations not only are EFX, but also satisfy MMS. A weaker result holds for any mechanism for two agents that always has pure Nash equilibria, which all induce EFX allocations.

# Introduction

This paper is interested in exploring the problem from a game-theoretic perspective.

Assume that the agents are strategic, i.e., **agent may misreport its true valuation for the goods to end up with a bundle of higher total value.**

The algorithm takes the input values (which are probably misreported) the agents declare.

The existence of truthful mechanisms. Even for 2 agents, truthfulness and fairness are incompatible by providing impossibility results for every non-trivial fairness notion. So next natural question: **Is it possible to have non-truthful mechanisms that are guaranteed to have equilibria with these equilibria always inducing fair allocations?**

## Contributions

The class of mechanisms that are implementable in polynomial time have pure Nash equilibria for every instance and provide some fairness guarantee as the allocations they produce in their equilibria is non-empty.

- Round-robin has pure Nash equilibria for every instance, and these equilibria induce allocations that are known properties of round-robin with a novel recursive construction of "nicely structured" bid profiles.
- Mod-Cut&Choose has pure Nash equilibria for every instance with 2 agents, and these equilibria induce allocations that are always EFX and MMS w.r.t. the underlying true values. For the case of 2 agents, MMS allocations are always EFX allocations; i.e., MMS fairness is stronger. In non-strategic setting, for any $\epsilon\gt 0$, there are instances in which the output of Mod-Cut&Choose is not $(5/6+\epsilon)$-MMS allocation.
- A weaker result. All mechanisms that have pure Nash equilibria for every instance with 2 agents and these equilibria induce allocations that are always EFX provide stronger MMS guarantees in these allocations than generic EFX allocations do.

## Related Works

~~Omitted~~

# Preliminaries

Goods $M$, agents $N$.

Valuations $\mathbf v$ are additive, non-negative.

All the goods must be allocated.

Allocation $A$.

Preference $\succcurlyeq_i,\succ_i$

## Fairness Notions

EF, EF1, EFX

PROP

MMS, $\alpha$-MMS

:dart::thinking:**Theorem 1**. For $n=2$, any MMS allocation is also an EFX allocation.

> For $n=2$, MMS allocation is indeed PMMS allocation. Any PMMS allocation is EFX allocation.

:dart::thinking:**Theorem 2**. For $n=2$, any EFX is also $2/3$-MMS allocation. The bound is tight for any $m\ge4$.

## Mechanisms and Equilibria

An allocation mechanism $\mathcal M$ is an algorithm that takes its input from the agents and allocates all the goods to them.

Assume each agent $i$ reports a bid vector $b_i$, where $b_{ij}\ge0$ is the value agent $i$ claims to have for good $g_j$. $\mathcal M$ takes bid profile $\mathbf b$ of bid vectors and outputs an allocation $\mathcal M(\mathbf b)$.

Assume that the agents are strategic (They may misreport their true values in order to get a bundle with higher value).

Hence, in general, $\mathbf b_i\neq(v_i(g_1),\cdots,v_i(g_m))$.

Focus: **Fairness guarantees of the pure Nash equilibria** of the mechanisms.

Best response.

The profile $\mathbf b$ is pure Nash equilibria (PNE) if for each $i\in N$, $\mathbf b_i$ is a best response to $\mathbf b_{-i}$.

When $\mathbf b$ is a PNE and the allocation $\mathcal M(\mathbf b)$ has a fairness guarantee, we say that $\mathbf b$ is EF1.

Some remarks:

1. Here we don't consider too much about the complexity of mechanism.
2. Any PNE of any $\alpha$-approximation mechanism for computing MMS allocation is an $\alpha$-MMS allocation. This is also true for PROP.

# Fairness of Nash Equilibria of Round-Robin

Round-Robin Mechanism takes a bid profile as input. Then RR should also take a permutation $N$ as an input to determine the priority of the agents.

WLOG, let the permutation be $1,2,\cdots,n$.

It's known that RR outputs EF1 allocation when all agents have additive valuation functions.

Pseudo-code: Omitted, can refer to [here](https://tinsir888.github.io/posts/b1884aba.html).

:thinking:**Lemma 1**. If $\mathbf b_i$ is the truthful bid of agent $i$, then the allocation $\mathcal A$ returned by Round-Robin is EF1 from $i$'s perspective. Moreover, if $i=1$, then $\mathcal A$ is EF from agent $1$'s perspective, i.e., $\forall j\in N,v_1(A_1)\ge v_1(A_j)$.

==Truth-telling is not a Pure Nash equilibria.== Example omitted.

RR is known to have PNE for any instance in which no agent values two goods exactly the same and at least some such equilibria are easy to compute. Strict preference ranking is convenient as it reduces the number of corner case with which one has to deal. This results can be extended to general additive valuation functions.

For only 2 agents, all PNE of round-robin are EF1 w.r.t. the real valuation functions. Complex even for $n=3$.

:dart::thinking:**Theorem 3**. For any fair division instance with 2 agents, every PNE of RR is EF1 w.r.t. $v_1,v_2$.

> Assume there exists a PNE $\mathbf b=(b_1,b_2)$ s.t., in the allocation $(A_1,A_2)$ returned by RR($\mathbf b$), at least one of the agents is not EF1-satisfied.
>
> - If agent 1 is not EF1-satisfied, then is not EF-satisfied either. Because PROP and EF are equivalent for $n=2$, we have $v_1(A_1)\lt v_1(M)/2$. According to Lemma 1, no matter what agent 2 bids, if agent 1 reports true values, the resulting allocation is EF from the agent's perspective. So, if $(A_1',A_2')$ is the allocation after agent 1 deviates to the agent's true values, it is EF-satisfied to agent 1, which contradicts the assumption before.
>
> - If agent 2 is not EF1-satisfied. Let $h_1$ be the good that agent 1 takes during the first round of RR and $g^*\in\arg\max_{h\in A_1}v_2(h)$ be the highest valued good in $A_1$ according to agent 2. 
>
>   Since agent 2 is not EF1-satisfied, $v_2(A_2)\lt v_2(A_1\diagdown\{g^*\})\lt v_2(A_1\diagdown\{h_1\})$. This implies that $(A_1\diagdown\{h_1\},A_2)$ of $M\diagdown\{h_1\}$ is not EF-satisfied to agent 2.
>
>   Thus $v_2(A_2)\le v_2(M\diagdown\{h_1\})/2$ by the **equivalence between EF and PROP for $n=2$.**
>
>   Then suppose agent 2 deviates to reporting the agent's true values and let $(A_1',A_2')$ be the resulting allocation. Since the allocation of $h_1$ is not affected by deviation, it is still given to agent 1 in 1st step of RR. Thus the execution of the mechanism would be exactly the same if the input $v_2$ had higher priority than agent 1. This would result in EF w.r.t agent 2 to $(A_1'\diagdown\{h_1\},A_2')$. We have $v_2(A_2')\ge v_2(A_1'\diagdown\{h_1\})$. Therefore $v_2(A_2)\ge v_2(M\diagdown\{h_1\})/2\gt v_2(A_2)$ contradicts the fact that $\mathbf b$ is PNE.

The proof doesn't work for $n\ge3$ because there is no equivalence between EF and PROP.

## Nash Equilibria of Round-Robin for Any Number of Agents

Agent 1 have no envy toward any other bundle whenever agent 1 bids truthfully. Agent 1 is EF-satisfied.

No matter what agent 1 bids, it is possible to "replace" the agent with an imaginary version of agent, who

1. does not affect the allocation
2. bids truthfully
3. considers the bundles of the allocation to be as valuable as the original agent 1 though they were

:dart::thinking:**Theorem 4**. For any fair division instance $\mathcal I$, every PNE of round-robin is EF1 w.r.t. $\mathbf v$.

> Proving it reduces to showing that the agent who picks first in the round-robin is EF-satisfied as long as the agent bids a best response to other agent' bids.
>
> From theorem 5, we know for PNE input, allocation returned by round-robin is EF-satisfied to agent 1.
>
> Fix an agent $\ell,\ell\ge2$. For $i\in[\ell-1]$, let $h_i$ be the good that agent $i$ claims to be the agent's favorite among the goods that are available when it is the agent's turn in the first round, that is $h_i=\arg\max_{h\in M\diagdown\{h_1,\cdots,h_{i-1}\}}b_i(h)$. Before $\ell$ is first assigned a good, all goods in $H=\{h_1,\cdots,h_{\ell-1}\}$ have been allocated.
>
> Consider the instance $\mathcal I'=(N,M\diagdown H,\mathbf v')$ in which all goods in $H$ are missing.
>
> Consider round-robin on such instance, it starts with agent $\ell$ and the follows the indices in increasing order.
>
> Claim that the bid $\mathbf b_\ell'$ is a best response for agent $\ell$ assuming that the restricted bid vectors of all the other agents are fixed.
>
> ......
>
> Proof is analogous to induction?

:dart::thinking:**Theorem 5**. For any fair division instance $\mathcal I$, if the reported bid vector $\mathbf b_1$ of agent 1 is a best response to the bid vector $\mathbf b_{-1}$ of all other players, then agent 1 is EF-satisfied with output.

> In PNE, each agent's bid is a BR to other agents' bids. Thus theorem 5 is a corollary to lemma 2.

:thinking:**Lemma 2**. Suppose that $v_1$ induces a strict preference ranking on the goods. Let $\mathbf b=(\mathbf b_1,\mathbf b_{-1})$. Then, there exists a valuation function $v_1^*$ with the following properties:

- If $\mathbf b_1^*$ is the truthful bid for $v_1^*$, then round-robin($\mathbf b$) and round-robin($\mathbf b_1^*,\mathbf b_{-1}$) produce the same allocation $A$.
- $v_1^*(A_1)=v_1(A_1)$
- For every good $g\in M\diagdown A_1$, it holds that $v_1^*(g)=v_1(g)$.

> Proof of Lemma 2.
>
> Recall that $k=\lceil m/n\rceil$, we have $k$ total rounds.
>
> Let $\succ_1$ be the preference ranking induced by $\mathbf b_1$ and consider all the goods according to this ranking: $h_1\succ_1h_2\succ_1\cdots\succ_1h_m$. Let $n_1=1\lt n_2\lt\cdots\lt n_k$ be the indices in this ordering of the goods assigned to agent 1 by round-robin($\mathbf b$). In round $r$, agent 1 receives good $h_{n_r}$. Thus $A_1=\{h_{n_1},h_{n_2},\cdots,h_{n_k}\}$.
>
> Recursively construct $v_1^*$ from $v_1$ over the rounds of RR. We define a sequence of intermediate bid vector $\mathbf b_1^r$ and valuation functions $v_1^r$. One for each round $r$ starting from the last round $k$, so that $v_1^*=v_1^1\land\mathbf b_1^*=\mathbf b_1^1$. For defining each $\mathbf b_1^r$, we use a number of auxiliary bid vectors. For any round $r$ we maintain that
>
> 1. $v_1^r(A_1)=v_1(A_1)$
> 2. $v_1^r(g)=v_1(g)\forall g\in M\diagdown A_1$
> 3. $\mathbf b_1^r$ is truthful from round $r$ w.r.t. $v_1^r$, meaning that for every good that is no better than $h_{n_r}$, according to the preference ranking $\succ_1^r$ induced by $\mathbf b_1^r$. Formally $g\not\succ_1^rh_{n_r}\Rightarrow\mathbf b_1^r(g)=v_1^r(g)$.
> 4. The preference ranking $\succ_1^r$ is identical to $\succ_1$ up to good $h_{n_r-1}$.
> 5. $\min_{g,h\in M,g\neq h}|v_1^r(g)-v_1^r(h)|\gt0$. (strict preference?)
>
> Focus on the last round $k$. Let $\lambda_k$ be the most valuable available good for $v_1$ at very beginning of the round. It is easy to see $h_{n_k}=\lambda_k$. (If not, by increasing the agent's bid for $\lambda_k$ be above the agent's bid for $h_{n_k}$, agent 1 would end up with the bundle $\{h_{n_1},h_{n_2},\cdots,\lambda_k\}$), which strictly improve over $A_1$ and contradict the fact that $\mathbf b_1$ is BR.
>
> We construct the auxiliary bid $\bar{\mathbf b}_1^k$ by moving up $\succ_1$ every good that is more valuable than $\lambda_k$ but comes after it in $\succ_1$.
>
> ...  
>
> ~~A long tough proof~~

Definition of **partial slide**: Let $\{q_1,q_2,\cdots,q_m\}$ be a renaming of the goods according to $\succ$. We say $\succ$ and $\succ'$ are within a partial slide of each other if there exist $x,y\in[m],x\le y$ s.t.
$$
q_1\succ q_2\succ\cdots\succ q_m\\
q_1\succ'\cdots\succ'q_{x-1}\succ'q_{x+1}\succ'\cdots\succ'q_y\succ'q_x\succ'q_{y+1}\succ'\cdots\succ'q_m
$$
Let $M_t(\mathbf b)$ denote the set of available goods right after $t-1$ goods have been allocated in a run of round-robin($\mathbf b$).

Lemma 3 states that, at beginning of each step, there is at most one difference between the sets of unallocated goods, and this is ==difference is either fixed or passed on to the next step, possibly slightly altered.==

:thinking:**Lemma 3**. Let $\mathbf b$ and $\mathbf b'$ be two profiles s.t. the corresponding induced preference ranking $\succ_i$ and $\succ'_i$ of agent $i$ are within a partial slide of each other. Then
$$
|M_t(\mathbf b)\diagdown M_t(\mathbf b')|=|M_t(\mathbf b')\diagdown M_t(\mathbf b)|\le1
$$
for all $t\in[m+1]$.

> **Case 1**: $M_t(\mathbf b)=M_t(\mathbf b')$.
>
> - No matter who $j$ is and what $g,g'$ are. It always holds either
>   - $|M_t(\mathbf b)\diagdown M_t(\mathbf b')|=|M_t(\mathbf b')\diagdown M_t(\mathbf b)|=\emptyset$ or
>   - $M_t(\mathbf b')\diagdown M_t(\mathbf b)=\{g\}$ or
>   - $M_t(\mathbf b)\diagdown M_t(\mathbf b')=\{g'\}$
> - Thus Lemma 3 holds in this case.
>
> Because $\succ_i,\succ_i'$ are within a partial slide of each other, there exists a unique good $s\in M$ that goes from a better position in $\succ_i$ to a worst position in $\succ_i'$
>
> Claim 1. Suppose that $t_*$ is the first time step in which the good $\gamma$ allocated in round-robin($\mathbf b$) is different from the good $\gamma'$ allocated in round-robin$(\mathbf b')$. Then $\gamma=s$. ~~Proof of claim 1 is omitted here.~~
>
> **Case 2**: $M_t(\mathbf b)\diagdown M_t(\mathbf b')=\{h\}\land M_t(\mathbf b')\diagdown M_t(\mathbf b)=\{h'\}$.
>
> - When $g=h\lor g'=h'$, it's easy to complete the induction.
> - If $g=h\land g'\neq h'$, we have $M_t(\mathbf b)\diagdown M_t(\mathbf b')=\{g'\}$.
> - If $g\neq h\land g'=h'$, analogously we have $M_t(\mathbf b')\diagdown M_t(\mathbf b)=\{h'\}$
> - The sub-case $g\neq h\land g'\neq h'$ can't happen, proof by contradiction.

# EFX Equilibria: The Case of 2 Agents

When the agents are not strategic, EFX exist when $n\le3$.

Even for $n=3$, we are not sure whether there is a polynomial time algorithm.

We consider $n=2$ in this paper.

## Mechanism with EFX Nash Equilibria

A polynomial time algorithm that outputs EFX allocation when we have 2 agents: modified cut-and-choose algorithm in which `cut` is a variant of envy-cycle-elimination. In this paper, we refer it as "Mod-Cut&Choose".

This mechanism is **not truthful**.

This paper shows that although its non-truthful, Mod-Cut&Choose always has at least one PNE for any instance, and all its equilibria are MMS and EFX. (MMS for 2 agent is PMMS, PMMS is EFX.)

### Pseudo-code

input: $\mathbf b_1,\mathbf b_2$

1. $(E_1,E_2)=(\emptyset,\emptyset)$
2. $(h_1,h_2,\cdots,h_m)$ is $M$, sorted in decreasing order w.r.t. $v_1$.
3. for $i=1,\cdots m$ do // agent 1 partition the bundles
   - $j=\arg\min_{k\in[2]}\mathbf b_1(E_k)$
   - $E_j=E_j\cup\{h_i\}$
4. $\ell=\arg\max_{k\in[2]}\mathbf b_2(E_k)$ // agent 2 chooses the bundle
5. return $\mathcal A=(M\diagdown E_\ell,E_\ell)$

Mod-Cut&Choose algorithm is $5/6$-MMS (tight).

:thinking:**Lemma 4**. Let $(X_1,X_2)$ be a partition of $M$. Agent $1$, by bidding accordingly, can force Mod-Cut&Choose to construct $E_1,E_2$ in for loop s.t. $\{E_1,E_2\}=\{X_1,X_2\}$.

> Agent $1$ can report in order to create the desired partition $(X_1,X_2)$ or its permutation $(X_2,X_1)$.
>
> Case 1: One set has all the goods. Agent $1$ declares zero value for all the goods. Then all the goods goes to $E_1$.
>
> Case 2: One set has $m-1$ goods. Agent 1 declares value $1$ for the good that is contained in the set with cardinality $1$, and for every good that is contained in the set with cardinality $m-1$, the agent declares value $\frac{1}{m-1}$. The first good is added in $E_1$, so $E_2$ is going to get chosen next. According to these values, $E_2$ must get all the remaining goods.
>
> Case 3: The two set have cardinalities $k\ge2$ and $m-k\ge2$. Agent $1$ declares value of one for one of the goods that are contained in the set with cardinality $k$. For every good that is contained in the  $m-k$ set, the agent declares a value of $\frac{1+\epsilon}{m-k}$ where $0\lt\epsilon\lt\frac{1}{m-k-1}$, For the rest of the goods, the agent declares $\frac{\epsilon}{k-1}$.

Agent $1$ can manipulate bidding to produce $\min\{v_1(E_1),v_1(E_2)\}=\mu_1$.

Corollary: Agent $1$ can force Mod-Cut&Choose mechanism to construct $\mu_1$-partition in for loop.

:dart::thinking:**Theorem 6**. For any instance $\mathcal I=(\{1,2\},M,\mathbf v)$, the Mod-Cut&Choose has at least one PNE. Every PNE of the mechanism is MMS and EFX w.r.t. valuation function $v_1,v_2$.

> Given a partition $\mathcal X=(X_1,X_2)$, we define a profile $(\mathbf b_1,\mathbf b_2)$ and show that it is a PNE.
>
> Let $\mathbf b_2$ be the truthful bid of agent 2.
>
> Let $\mathbf b_1$ be the bidding vector that results in Mod-Cut&Choose constructing a partition in
> $$
> \arg\max_{\mathcal X\in\Pi_2(M)}v_1(\arg\min_{X\in\mathcal X}(X)).
> $$
> Notice that $\Pi_2(M)$ is finite. By lemma 4, every possible partition can be produced by Mod-Cut&Choose given the appropriate bid vector of agent $1$.
>
> So agent $1$ forces the partition that maximizes (according $v_1$) the value of the least desirable bundle according to $v_2$.
>
> Now it's easy to see that given the bidding strategy of agent $2$ (truth-telling), there is no deviation for agent $1$ that is profitable. Moreover, agent $2$ gets the best of the two bundles according to the agent's valuation function (truth-telling is dominant strategy for agent $2$). Thus, there is no profitable deviation for the agent either. Therefore $(\mathbf b_1,\mathbf b_2)$ is a PNE for $\mathcal I$.
>
> Suppose a contradiction that there is a PNE $\mathbf b$, for which an agent $i$ does not achieve the agent's $\mu_i$ in the allocation returned by Mod-Cut&Choose($\mathbf b$). If it's agent $1$, then according to Corollary $1$, there is a bid vector $\mathbf b_1'$ the agent can report so that the algorithm produces a $\mu_1$-partition. By deviating to $\mathbf b_1'$, regardless of the set given to agent $2$, agent $1$ ends up with a bundle the agent values at least $\mu_1$. This would be a strict improvement over the agent's current gain, contradicting that $\mathbf b$ is PNE.
>
> So it must be the case that agent $2$ gets a bundle that strictly less than $\mu_2$. Regardless of the partition, by declaring the truthful bid, agent $2$ gets a bundle of value at least $v_2(M)/2$. Then it's immediate to see that this value is at least $\mu_2$, which is a contradiction.
>
> So it's MMS for $2$ agents, thus it's PMMS, thus EFX.

## The Enhanced Fairness of EFX Nash Equilibria

Consider the class of mechanisms that have PNE for every instance, and these equilibria always lead to EFX allocations.

Goal: If these allocations have better fairness guarantees than EFX allocation in general.

2 agents + 4 goods, this paper proved that for every mechanism of this class, all allocations at a PNE are MMS allocations. By Theorem 2, there are instances with just 4 goods in which an EFX allocation is $2/3$-MMS (tight bound).

:thinking:**Lemma 5**. Consider an instance with $2$ agents and $4$ goods. If agent $i\in[2]$ has strictly positive value for $3$ or fewer goods, then in every allocation that is EFX from the agent's point of view, agent $i$ has value at least $\mu_i$.

> Suppose agent $i$ has positive value for at most 3 goods. The statement is trivial when there is at most one positively valued good as in this case $\mu_i=0$, and agent $i$ always gets $\mu_i$ no matter the bundle that agent gets. When agent has a positive value for two goods, in order to consider the allocation as EFX, the agent must get at least one of them. In this case, the agent also achieves the agent's $\mu_i$ as it is equal to the smaller of the 2 positive values.
>
> Suppose agent $i$ has positive value for 3 goods, notice that $\mu_i$ in this case is equal to $\min($the largest one, the sum of two smallest values$)$. If agent $i$ gets 2 goods, then the agent always derives a value of at least $\mu_i$. If agent gets just one good, then this good must have the highest value; otherwise the agent wouldn't consider the allocation as EFX. So agent gets value at least $\mu_i$.

:dart::thinking:**Theorem 7**. Let $\mathcal M$ be a mechanism that has PNE for any instance $([2],\mathcal M,(v_1,v_2))$ with $|\mathcal M|=4$ and all these equilibria lead to EFX allocations w.r.t. $v_1,v_2$. Then, each such EFX allocation is also an MMS allocation.

> Suppose for contradiction. There exists a valuation instance $\mathbf v$ for which there is a PNE $\mathbf b=(\mathbf b_1,\mathbf b_2)$ that produces an EFX allocation $(A_1,A_2)$, where, WLOG, $v_1(A_1)\lt\mu_1$.  Rename good to $h_1,h_2,h_3,h_4$ so that valuation is in decreasing order. By lemma 6, $A_1$ must be either a singleton or one of $\{h_2,h_3\},\{h_2,h_4\},\{h_3,h_4\}$.
>
> - $|A_1|=1$. Because $(A_1,A_2)$ is an EFX allocation and all goods have positive value according to $v_1$, it is easy to see that $A_1=\{h_1\}$. Then again because we have an EFX allocation, $v_1(h_1)\ge v_1(\{h_2,h_3\})$. The latter implies $v_1(A_1)\ge\mu_1$ by second inequality of lemma 6.
>
> - $A_1=\{h_2,h_3\}$. Because $A_1$ is EFX-satisfied, $v_1(A_1)\ge v_1(h_1)$. Similar to case 1, this implies $v_1(A_1)\ge\mu_1$.
>
> - $A_1=\{h_2,h_4\}$ or $\{h_3,h_4\}$. Consider different $\mathbf v^*$, where agents have same values over the goods. The valuation function $v^*$ is defined
>   $$
>   v^*(h_j)=
>   \begin{cases}
>   1.2\ j=1\\
>   1\ j\in\{2,3\}\\
>   0.1\ j=4
>   \end{cases}
>   $$
>   There are only 2 EFX allocations $(\{h_1,h_4\},\{h_2,h_3\}),(\{h_2,h_3\},\{h_1,h_4\})$. According to assumption, there must be a bid $\mathbf b^*$ that is a PNE of $\mathcal M$ for this valuation profile. Because we require PNE to be EFX, $\mathcal M(\mathbf b^*)$ must be one of these allocations.
>
>   Observe that the value that agent 2​ derives in these allocations is at most 2.
>
>   ~~discuss on bundle of agent 1 accordingly.~~
>
>   - $A_1$ is singleton. Contradicts PNE.
>   - $A_1$ has 3 items. Contradicts PNE.
>   - $A_1$ is one of $12,13,14,23$. By lemma 6, this contradicts MMS.
>   - $A_1$ is one of $24,34$. Contradicts PNE.
>
> Thus EFX allocations here are all MMS allocations. Q.E.D.

:thinking:**Lemma 6**. For $N,M,v_1$ as earlier, we have $v_i(\{h_1,h_4\})\ge\mu_1$ and $\max\{v_i(h_1),v_i(\{h_2,h_3\})\}\ge\mu_1$.

For $m\ge5$, theorem 7 is not work. Because there are cases don't lead to contradiction.

We need relaxation on MMS to $\alpha$-MMS, but here $\alpha\gt2/3$.

:dart::thinking:**Theorem 8**. Let $\mathcal M$ be a mechanism that has PNE for any instance $([2],\mathcal M,(v_1,v_2))$ and all these equilibria lead to EFX allocation w.r.t. $v_1,v_2$. Then, each such EFX allocation is also an $\alpha$-MMS with $\alpha\gt2/3$.

> Suppose for contradiction: there exists such a mechanism $\mathcal M$ and an instance $(\{1,2\},M,(v_1,v_2))$ for which there is a PNE $\mathbf b=(\mathbf b_1,\mathbf b_2)$ that results in an EFX allocation $\mathcal A$, where $v_i(A_i)\le2\mu_i/3$ for at least one $i\in[2]$. WLOG, assume $v_1(A_1)\le2\mu_1/3$. By Theorem 2, this implies $v_1(A_1)=2\mu_1/3$. Thus $v_1(A_2)\ge4\mu_i/3$ because $v_1(M)\ge2\mu_1$ by definition of MMS.
>
> Initially, we restrict # goods with positive value in $A_2$. Let $S\subseteq A_2$ be the set of such goods. Let $|S|=k$ and notice that $k$ can't be $0$ or $1$. Finally, let $x\in\arg\min_{g\in S}v_1(g)$ be a minimum valued good for agent $1$ in $S$. We have
> $$
> \frac{2}{3}\mu_1=v_1(A_1)\ge v_1(S\diagdown\{x\})\ge v_1(S)-\frac{v_1(S)}{k}=\frac{k-1}{k}v_1(A_2)\ge\frac{k-1}{k}\frac{4}{3}\mu_1,
> $$
> where the first inequality follows from EFX. Observe that $k\le2$, this implies $k=2$.
>
> ...... ~~Yet another long proof~~

# Discussion

This paper discusses fair division with indivisible goods to a set of strategic agents.

Surprisingly, there exists strong impossibilities for truthful mechanisms. The paper shows that there exist mechanisms that have PNE for every instance, and at the same time the **PNE** allocation have **strong guarantees** w.r.t. true valuations.

Possible directions for future work:

- Algorithm that compute EF1 allocations for richer valuation function domains (Envy-cycle elimination) in strategic setting.
  - Does there exist pure or mixed Nash Equilibria?
  - If there exist NE, does it still maintain fairness properties at the NE?

- Theorem 7&8 leave an open problem: MMS guarantee that the equilibria of mechanisms that always have PNE and these are EFX.
  - suspect: corresponding allocations are not always MMS.
  - For every such mechanism that runs in polynomial time, finding a best response of an agent is NP. For $n\ge3$ the problem is absolutely non-trivial.
- The complexity for computing best response. Want to know if best response dynamics always converge to a PNE or there might be cyclic behavior?