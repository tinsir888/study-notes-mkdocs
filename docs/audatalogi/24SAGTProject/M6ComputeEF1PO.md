---
title: Fair Division 6 Computing EF1 + PO
author: tinsir888
date: 2024/4/5
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: f7ce8499
---

Objective: Investigate methods for computing allocations satisfying both EF1 and PO.

Activities: Review the paper [*Finding Fair and Efficient Allocations*](https://arxiv.org/abs/1707.04731).



Caragiannis et al has established the result: for additive valuations, there always exists an allocation that is EF1 + PO by maximizing Nash Social Welfare.

However, computing MNW is NP-Hard.

This paper:

1. develop a **pseudo-polynomial** time algorithm for finding EF1 + PO. When valuations are bounded, the algorithm is polynomial time.
2. For additive valuations, there always exists an allocation that is EF1 and **fractionally Pareto Efficient**.
3. The algorithm provides a polynomial time $1.45$-approximation to the Nash social welfare objective.
4. The algorithm is **completely combinatorial**, and relies on constructing integral Fisher market wherein specific equilibria are not only efficient, but also fair.

~~This paper is very difficult and I felt suffocated while reading it.~~🤡🤡🤡

# Introduction

Caragiannis showed by maximizing Nash Welfare, the geometric mean of the agents' valuations is both fair (EF1) and Pareto efficient. However, maximizing the Nash welfare is an NP-hard problem (more accurately, APX-hard)

This paper provides a pseudo-polynomial time algorithm for finding an EF1 and Pareto Optimal allocation of indivisible goods under additive valuations.

In particular, if the valuations are scaled, the algorithm finds such an allocation in polynomial time.

~~Maximizing Nash welfare remains APX-hard even for bounded valuations.~~

Related problem: approximation algorithm for Nash welfare maximization. But approximating Maximal Nash welfare is not guaranteed to be EF1 or Pareto optimal.

This algorithm provides a polynomial-time $1.45$-approximation to the Maximal Nash welfare problem. SOTA! It is also guaranteed to return a fair and efficient outcome.

## Contributions

1. **Pseudo-polynomial for unscaled valuation and polynomial for scaled valuation**. Computing EF1 + PO allocations. This algorithm can find an approximate EF1 and approximate PO allocation without scaled valuations.

2. **Stronger existence results**. For additive valuations, there always exists an allocation that is EF1 and **fractionally Pareto efficient**.

   There exists a non-deterministic polynomial time algorithm for finding EF1 + PO.

   Whereas even verifying whether an arbitrary allocation is PO is known to be co-NP-complete.

3. Polynomial time $1.45$-approximation for the Nash social welfare maximization.

   Interesting byproduct: under same valuations, EF1 allocation provides a $1.45$-approximation to the MNW.

## Techniques

Fisher market along with an underlying equilibrium which is integral and EF1.

Start with a Pareto Optimal allocation, then iteratively modify the allocation by exchanging goods between the agents. The goal of the exchange step is to move toward a fair allocation. Stop when the equilibrium of the market satisfies *price envy-freeness up to one good*. 

First the algorithm works with an integral Fisher market at every step, breaking away from the standard *relax-and-round* paradigm where a fractional market equilibrium is first computed followed by round step. Second, the algorithm uses the notion of *price envy-freeness up to one good* as a measure of balanced spending in the Fisher market. The notion is novel.

# Preliminaries

## The Fair Division Model

### Problem Instance

~~Omitted!!!~~

Valuations are non-negative, additive.

For single item $j$, denote $i$'s valuation for it as $v_{i,j}=v_i(\{j\})$

Denote $v_\max=\max_{i\in[n],j\in[m]}v_i(j)=\max_{i,j}v_{i,j}$.

### Allocation

Allocation: ~~Omitted!!!~~

Fractional allocation: For all agents, no more than one unit of each good is allocated:
$$
\sum_{i\in[n]}x_{i,j}\le1
$$

## Fairness Notions

EF, EF1

$\epsilon$-EF1: for every pair of agent $i,k\in[n]$, there exists a good $j\in\mathbf x_k$ s.t.

$$
(1+\epsilon)v_i(\mathbb x_i)\ge v_i(\mathbb x_k\diagdown\{j\})
$$

Nash social welfare

$$
NSW(\mathbf x)=(\prod_{i\in[n]}v_i(\mathbf x_i))^{1/n}
$$

!!! info
    It is okay to ignore the $1/n$ power.


$\mathbf x^*\in\arg\max_{\mathbf x\in\mathcal X}NSW(\mathbf x)$

## Efficiency Notions

Pareto Efficiency.

$\mathbf x$ is Pareto dominated by another allocation $\mathbf y$ if $v_k(\mathbf y_k)\ge v_k(\mathbf x_k)$ for every agent $k\in[n]$, and some of them is $\gt$.

An allocation is said to be **Pareto efficient** or **Pareto optimal** if it is not Pareto dominated by any other allocation. Similarly, $\mathbf x$ is $\epsilon$-PO if it is not $\epsilon$-PO dominated by any other allocation ($v_k(\mathbf y_k)\ge(1+\epsilon)v_k(\mathbf x_k)$, for some $k$ the inequalities are strict).

Fractional Pareto Efficiency. **If it is not Pareto dominated by any fractional allocation.**

fractionally PO $\Rightarrow$ PO, but PO $\not\Rightarrow$ fractionally PO.

# Main Results

## Algorithm computes EF1 + PO

:dart::thinking:**Theorem 1**.

Given any fair division instance $\mathcal I$ with additive valuations, an allocation that is EF1 and PO can be found in $\mathcal O(\text{poly}(m,n,v_{\max}))$ time, where $v_\max=\max_{i,j}v_{i,j}$.

Remark: If $v_\max$ is polynomial bounded by $f(m,n)$, the EF1 + PO can be computed in polynomial time.

If we relax the fairness and efficiency requirements to approximate versions, then the algorithm is guaranteed to run in polynomial time.

$\epsilon$-EF1 + $\epsilon$-PO in $\mathcal O(\text{poly}(m,n,\frac{1}{\epsilon},\ln v_\max))$

## Existence for EF1 and fractionally PO

:dart::thinking:**Theorem 2**. Given any fair division instance with additive valuations, there always exists an allocation that is EF1 and fractionally PO (fPO)

Remark: :sob: I don't understand. What is [TFNP](https://en.wikipedia.org/wiki/TFNP) (Total Function Nondeterministic Polynomial)?

> A binary relation $P(x,y)$ is in TFNP if and only if there is a deterministic polynomial time algorithm that can determine whether $P(x,y)$ holds given both $x$ and $y$, and for every $x$, there exists a $y$ which is at most polynomially longer than $x$ such that $P(x,y)$ holds.

## Approximation algorithm for Nash social welfare

:dart::thinking:**Theorem 3**. For additive valuations, there exists a polynomial-time $1.45$-approximation algorithm for the Nash social welfare maximization problem.

:thinking:**Lemma 1**. Given a fair division instance with identical and additive valuations, any $\epsilon$-EF1 allocations provides a $e^{(1+\epsilon)e}$-approximation to Nash social welfare.

# The Algorithm

## Market Terminology

### Fisher Market

A fundamental model in the economics of resource allocation.

A set of buyers enter the market with pre-specified budgets and use it to buy goods that provide maximum utility per unit of money spent.

$[n]$ buyers, $[m]$ **divisible** goods, valuation profile $\mathcal V$.

Each buyer $i\in[n]$ has an initial **endowment/budget** $e_i\gt0$. The endowment is only used for buying goods. Endowment vector $\mathbf e$.

A **market instance** $\langle[n],[m],\mathcal V,\mathbf e\rangle$

A **market outcome** is given by the pair $\langle\mathbf x,\mathbf p\rangle$, where the **allocation vector** $\mathbf x$ is a **fractional allocation** of the $m$ goods, and the price vector $\mathbf p$ associates a price $p_j\ge0$ with each good $j\in[m]$.

The spending of buyer $i$ under the market outcome $\langle\mathbf x,\mathbf p\rangle$ is given by $\mathbf p(\mathbf x_i)=\sum_{j=1}^mx_{i,j}p_j$.

The valuation derived by $i$ under the market outcome $\langle\mathbf x,\mathbf p\rangle$ is given by $v_i(\mathbf x_i)=\sum_{j=1}^mx_{i,j}v_{i,j}$.

Given a **price vector** $\mathbf p$, define the **bang per buck ratio** of buyer $i$ for good $j$ as
$$
\alpha_{i,j}=v_{i,j}/p_j
$$

> 显然这个性价比越高，$i$ 就越高兴。

The **maximum bang per buck ratio** is $\alpha_i=\max_j\alpha_{i,j}$.

Let $MBB_i=\{j\in[m]:v_{i,j}/p_j=\alpha_i\}$ denote the set of all goods that maximize the bang per buck ratio for buyer $i$ at the price vector $\mathbf p$. We call $MBB_i$ the **maximum bang per buck set** (MBB set) of buyer $i$ at the price vector $\mathbf p$.

**Fisher market equilibrium**. An outcome $\langle\mathbf x,\mathbf p\rangle$ satisfies the following conditions:

- Market clearing: Each good is either priced at zero or is completely allocated. $\forall j\in[m],p_j=0\lor\sum_{i=1}^nx_{i,j}=1$.
- Budget exhaustion: Buyers spend their endowments completely. $\forall i\in[n],\mathbf p(\mathbf x_i)=e_i$.
- Maximum bang per buck allocation: Each buyer's allocation is a subset of its MBB set. $x_{i,j}\gt0\Rightarrow j\in MBB_i$.

:thinking:**Proposition 1**. For a Fisher market with additive valuations, any equilibrium outcome is fractionally Pareto efficient (fPO).

Price envy-freeness and its variants

- $\mathbf x$ is price envy-free (pEF) w.r.t. $\mathbf p$ if for every pair of buyers $i,k\in[n]$, we have $\mathbf p(\mathbf x_i)\ge\mathbf p(\mathbf x_k)$. (Equivalently, $=$)
- $\mathbf x$ is price envy-free up to one good (pEF1) w.r.t. $\mathbf p$ for every pair of buyers $i,k\in[n]$, there exists a good $j\in\mathbf x_k$ s.t. $\mathbf p(\mathbf x_i)\ge\mathbf p(\mathbf x_k\diagdown\{j\})$.
- $\mathbf x$ is $\epsilon$-pEF1 w.r.t. $\mathbf p$ for every pair of buyers $i,k\in[n]$, there exists a good $j\in\mathbf x_k$ s.t. $(1+\epsilon)\mathbf p(\mathbf x_i)\ge\mathbf p(\mathbf x_k\diagdown\{j\})$.

### MBB graph and alternating paths

- MBB graph of a Fisher market instance w.r.t. $\mathbf p$ is defined as a bipartite graph $G$ whose vertex set consists of the set of agents $[n]$ and goods $[m]$. There is an edge between agent $i$ and good $j$ if $j\in MBB_i$ (called an MBB edge).
- Given an allocation $\mathbf x$, we can augment the MBB graph by adding allocation edges. For an augmented MBB graph, we define an **alternating path** $P=(i,j_1,i_1,j_2,i_2,\cdots,i_{\ell-1},j_\ell,k)$ from agent $i$ to agent $k$ as a series of alternating MBB and allocation edges s.t. $j_1\in MBB_i\cap \mathbf x_{i_1},j_2\in MBB_{i_1}\cap\mathbf x_{i_2},\cdots,j_\ell\in MBB_{i_{\ell-1}}\cap\mathbf x_k$. If such a path exists, we say that the agent $k$ is **reachable** from agent $i$ via an alternating path. ==No agent or good is allowed to repeat in an alternating path==. The path $P$ is of length $2\ell$ since it consists of $\ell$ MBB edges and $\ell$ MBB edges and $\ell$ allocation edges.

> 简单理解一下，MBB 图就是只含有 MBB 集合中对应关系的边。
>
> 而增强 MBB 图在此基础上加入了分配边。
>
> 可达性指的是在增强图上如果一个智能体节点经过一个替换路径可以到另一个智能体节点。

### Hierarchy structure

- Let $G$ denote the augmented MBB graph for a Fisher market instance with the market outcome $(\mathbf x,\mathbf p)$.

- Fix a source agent $i\in[n]$ in $G$. Define the *level* of an agent $k$ as half the length of the shortest alternating path from $i$ to $k$. The *level* of the source agent $i$ is defined to be $0$. If there is no alternating path from $i$ to some agent $k$ in $G$, then the level of $k$ is set to be $n$. The **hierarchy** structure $\mathcal H_i$ of agent $i$ is defined as a level-wise collection of all agents that are reachable from $i$, where $\mathcal H_i^\ell$ denotes the set of agents that are at level $\ell$ w.r.t. the agent $i$. `BuildHierarchy` is used for constructing such a hierarchy.

  In a hierarchy cannot have edges between agents at the same level.

### Violators

- An agent $i$ with smallest spending money among all the agents is called **least spender**, i.e., $i\in\arg\min_{k\in[n]}\mathbf p(\mathbf x_k)$. An agent $k\in[n]$ is said to be a **violator** if for every good $j\in\mathbf x_k$, we have $\mathbf p(\mathbf x_k\diagdown\{j\})\gt\mathbf p(\mathbf x_i)$, where $i$ is the least spender.

- Analogously, $k$ is said to be $\epsilon$-violator if for every good $j\in\mathbf x_k$, we have $\mathbf p(\mathbf x_k\diagdown\{j\})\gt(1+\epsilon)\mathbf p(\mathbf x_i)$. An agent can be a violator without being an $\epsilon$ violator.

  ==所以ε-违反者比违反者更苛刻？==

- If no agent is a violator (or $\epsilon$-violator), then the allocation $\mathbf x$ is pEF1 ($\epsilon$-pEF1) w.r.t. $\mathbf p$.

Path-violator

- Let $i$ denote the least spender, and let $\mathcal H_i$ denote the hierarchy of agent $i$. An agent $k\in\mathcal H_i$ is said to be **path-violator** w.r.t. the alternating path $P=(i,j_1,i_1,j_2,i_2,\cdots,i_{\ell-1},j_\ell,k)$ if $\mathbf p(\mathbf x_k\diagdown\{j_\ell\})\gt\mathbf p(\mathbf x_i)$.
- A path-violator need not be a violator, since there can be a good $j\in\mathbf x_k$ not on the path $P$ s.t. $\mathbf p(\mathbf x_k\diagdown\{j\})\le\mathbf p(\mathbf x_i)$. Similarly, an agent $k\in\mathcal H_i$ is said to be an $\epsilon$-path-violator w.r.t. $P$ if $\mathbf p(\mathbf x_k\diagdown\{j_\ell\})\gt(1+\epsilon)\mathbf p(\mathbf x_i)$.

## Description of the Algorithm

Given any fair division instance $I$ as input and a parameter $\epsilon\gt0$. constructs a market equilibrium $(\mathbf x,\mathbf p)$ w.r.t. a Fisher market instance $\langle[n],[m],\mathcal V,\mathbf e\rangle$. The pair $(\mathbf x,\mathbf p)$ has the following two properties:

1. $\mathbf x$ is an integral allocation.

2. $\mathcal x$ is $3\epsilon$-pEF1 w.r.t. $\mathbf p$.

   This shows that the allocation $\mathbf x$ is $3\epsilon$-EF1 for the corresponding fair division instance $\mathcal I$.

By proposition 1, the allocation $\mathbf x$ is guaranteed to be fractionally Pareto Optimal   for the Fisher market instance, and consequently for the fair division instance $\mathcal I$.

:thinking:**Lemma 2**. Let $\epsilon\gt0$, and let $\mathbf x$ and $\mathbf p$ be an allocation and a price vector respectively for a market instance $\langle[n],[m],\mathcal V,\mathbf e\rangle$ s.t.

1. $\mathbf x$ is $\epsilon$-pEF1.
2. $\mathbf x_i\in MBB_i$ for each player $i\in[n]$.

Then, $\mathbf x$ is $\epsilon$-EF1 for the associated fair division instance $\langle[n],[m],\mathcal V\rangle$. 

> Since $\mathbf x$ is $\epsilon$-pEF1 w.r.t. $\mathbf p$, for any pair of buyers $i,k\in[n]$, $\exists j\in\mathbf x_k$ s.t. $(1+\epsilon)\mathbf p(\mathbf x_i)\ge\mathbf p(\mathbf x_k\diagdown\{j\})$.
>
> Multiplying both sides with maximum bang per ratio $\alpha_i$ for agent $i$, we have
> $$
> \alpha_i(1+\epsilon)\mathbf p(\mathbf x_i)\ge\alpha_i\mathbf p(\mathbf x_k\diagdown\{j\})\\
> \Rightarrow(1+\epsilon)v_i(\mathbf x_i)\ge\alpha_i\mathbf p(\mathbf x_k\diagdown\{j\})\because\mathbf x_i\subseteq MBB_i\\
> \Rightarrow(1+\epsilon)v_i(\mathbf x_i)\ge v_i(\mathbf x_k\diagdown\{j\}),
> $$
> which is the $\epsilon$-EF1 guarantee for the allocation $\mathbf x$.

The algorithm's behavior:

- Phase 1: starts with a welfare maximizing allocation $\mathbf x$ and $\mathbf p$ s.t. $\mathbf x$ is fPO and each agent gets a subset of its MBB goods. If the allocation $\mathbf x$ is $3\epsilon$-pEF1 w.r.t. $\mathbf p$, then the algorithm terminates with the output $(\mathbf x,\mathbf p)$. Otherwise, goto phase 2.

- Phase 2: the algorithm works with hierarchy of the least spending agent, and performs a series of exchanges (or swaps) of goods between the agents in the hierarchy. The swaps are aimed at ensuring that at the end of Phase 2, no agent in the hierarchy is $\epsilon$-pEF1 envied by the least spender.

  All exchanges happen only along the MBB edges, thus maintaining at each stage the condition that $\mathbf x$ is an equilibrium allocation, and hence fPO.

  

  If at the end of Phase 2, the current allocation $\mathbf x$ is still not $3\epsilon$-pEF1 w.r.t. the price vector $\mathbf p$, the algorithm move to Phase 3.

- Phase 3: Uniformly raising the price of the goods owned by the members of the hierarchy. The price are raised until either the allocation $\mathbf x$ becomes $3\epsilon$-pEF1 w.r.t. the new price vector $\mathbf p$, or a new agent gets added to the hierarchy. In the latter case, the algorithm goes back to the start of Phase 2.

Establishing the time complexity for this algorithm is complicated. The stated running time bound is obtained via a number of involved arguments relying on analyzing the spending of the agents in different phases.

# Proof of Theorem 1

~~Seems that I have no time to understand the detail.~~

~~I try to grasp the argument of proof.~~

First we should present the analysis of algorithm for valuations that satisfy the power of $(1+\epsilon)$ property. Then extend the analysis to general valuations.

## Pseudo-code

Input: A fair division instance $\mathcal I$ s.t. valuations are power-of-$(1+\epsilon)$

Output: An integral allocation $\mathbf x$ and a price vector $\mathbf p$.

~~integral allocation 就是指所有的物品全部都分完的分配~~

// Phase 1 initialization

1. $\mathbf x\gets$ Welfare-maximizing allocation // allocate each good to the agent who has highest valuation.
2. $\mathbf p\gets$ For each good $j\in[m]$, set $p_j=v_{i,j}$ if $j\in\mathbf x_i$. // initialize the payment vector with the highest valuation among all agents.
3. if $(\mathbf x,\mathbf p)$ is $3\epsilon$-pEF1 then return it

// Phase 2 Removing price-envy within hierarchy

4. $i\gets$ least spender under $(\mathbf x,\mathbf p)$
5. $\mathcal H_i\gets$ `BuildHierarchy`($i,\mathbf x,\mathbf p$)
6. $\ell\gets1$
7. while $\mathcal H_i^\ell$ is non-empty and $(\mathbf x,\mathbf p)$ is not $3\epsilon$-pEF1 do
   - if $h\in\mathcal H_i^\ell$ is a $\epsilon$-path-violator along the alternating path $P=\{i,j_1,h_1,\cdots,j_{\ell-1},h_{\ell-1},j,h\}$ then
     - $\mathbf x_h\gets\mathbf x_j\diagdown\{j\}$ and $\mathbf x_{h_{\ell-1}}\gets\mathbf x_{h_{\ell-1}}\cup\{j\}$
     - repeat Phase 2 starting from line 4
   - else
     - $\ell\gets\ell+1$
8. if $(\mathbf x,\mathbf p)$ is $3\epsilon$-pEF1 then return it
9. else move to Phase 3

// Phase 3 price-rise

10. $\alpha_1\gets\min_{h\in\mathcal H_i,j\in[m]\diagdown\mathbf x_{\mathcal H_i}}\frac{\alpha_h}{v_{h,j}/p_j}$, where $\alpha_h$ is the maximum bang per ratio for agent $h$, and $\mathbf x_{\mathcal H_i}$ is the set of goods currently owned by members of the hierarchy $\mathcal H_i$.

    /* $\alpha_1$ corresponds to raising prices until a new agent gets added to the hierarchy */

11. $\alpha_2\gets\frac{1}{\mathbf p(\mathbf x_i)}\max_{k\in[n]\diagdown\mathcal H_i}\min_{j\in \mathbf x_k}\mathbf p(\mathbf x_k\diagdown\{j\})$

    /* $\alpha_2$ corresponds to raising prices until the pEF1 condition is satisfied */

12. $\alpha_3\gets(1+\epsilon)^s$, where $s$ is the smallest integral power of $(1+\epsilon)$ s.t. $(1+\epsilon)^s\gt\frac{\mathbf p(\mathbf x_h)}{\mathbf p(\mathbf x_i)}$. here $i$ is the least spender and $h\in\arg\min_{k\in[n]\diagdown\mathcal H_i}\mathbf p(\mathbf x_k)$.

    /* $\alpha_3$ corresponds to raising prices in multiples of $(1+\epsilon)$ until the identity of the least spender changes */ 

13. $\alpha\gets\min(\alpha_1,\alpha_2,\alpha_3)$

14. for each good $j\in\mathbf x_\mathcal H$, do

    - $p_j\gets\alpha\cdot p_j$

15. if $\alpha=\alpha_2$ then return $(\mathbf x,\mathbf p)$ else repeat Phase 2 starting from line 4

## Analysis of ALG when the Valuations are power-of-(1+ε)

power-of-$(1+\epsilon)$: $\exists\epsilon\gt0$, s.t. $\forall i\in[n],j\in[m]:v_{i,j}\in\{0,(1+\epsilon)^a\}$ for some natural number $a$.

**Time step and events**. 4 events:

1. `Swap` in Phase 2
2. `Change` in the identity of least spender in Phase 2
3. `Prise-rise` by a factor of $\alpha$ in Phase 3
4. `Termination` step.

Denote **time step** the indexing of any execution of ALG.

:thinking:**Lemma 3**. Given any power-of-($1+\epsilon$) instance as input, the allocation returned by ALG is $3\epsilon$-EF1 and fPO.

> fPO: observation: at each step, the allocation of any agent is a subset of its MMB goods.
>
> Step 1: true (by way of setting prices).
>
> Step 2: swap operation only happens along an alternating MBB-allocation edge which maintains MBB condition.
>
> Step 3: raising the prices of goods owned by the members of hierarchy $\mathcal H_i$ without changing the allocation. **ARGUMENT**
>
> Then define a Fisher market where each agent's endowment equals its spending under $\mathbf x$. Since $(\mathbf x,\mathbf p)$ is an equilibrium, $\mathbf x$ is fPO.
>
> Next, argue that $\mathbf x$ is $3\epsilon$-EF1. The ALG terminates only if either $3\epsilon$-pEF1, or $\alpha=\alpha_2$. If $\alpha=\alpha_2$, we need to argue it's $3\epsilon$-pEF1. ~~omitted~~

:thinking:**Lemma 4**. Given any power-of-$(1+\epsilon)$ instance as input, ALG terminates in time $\mathcal O(\text{poly}(m,n,\frac{1}{\epsilon},\ln v_\max))$ where $v_\max=\max_{i,j}v_{i,j}$.

> ~~Proof omitted.~~

## Analysis of ALG for General Valuations: Proof of Theorem 1

Show that for any given fair division instance $\mathcal I$ with integral valuations, EF1 + PO allocation can be found in pseudo-polynomial time.

Proof by running ALG on $\epsilon$-rounded version $\mathcal I'=\langle[n],[m],\mathcal V'\rangle$ of the given instance $\mathcal I$ for some parameter $\epsilon\gt0$. The $\mathcal I'$ is a power-of-($1+\epsilon$) instance constructed by *rounding up* the valuations in $\mathcal I$ to the nearest integer power of $(1+\epsilon)$. From Lemma 3, we know this allocation is $3\epsilon$-EF1 and fPO w.r.t. $\mathcal I'$. Then it remians to show that for an appropriate choice of $\epsilon$, the same allocation turns out to be EF1 + PO w.r.t. $\mathcal I$.

In addition, the running time bound in Lemma 4 instantiated for the choice of $\epsilon$ shows that ALG runs in pseudo-polynomial time.

$\epsilon$-rounded version:

for each agent $i$, the valuation is replaced by
$$
v'_{i,j}=\begin{cases}
(1+\epsilon)^{\lceil\log_{1+\epsilon}v_{i,j}\rceil}\text{ if }v_{i,j}\gt0\\
0\text{ if }v_{i,j}=0
\end{cases}
$$
$v_{i,j}\le v'_{i,j}\le(1+\epsilon)v_{i,j}$ for each $i,j$.

:thinking:**Lemma 5**. Let $\mathcal I$ be a fair division instance. Let $\epsilon\le\frac{1}{6m^3v^4_\max}$. Then, an allocation $\mathbf x$ that is fPO for $\mathcal I'$ is PO for $\mathcal I$.

Lemma 5 establishes for an appropriate choice of $\epsilon$, an allocation that is fPO for the $\epsilon$-rounded instance $\mathcal I'$ is PO w.r.t. the original instance $\mathcal I$.

:thinking:**Lemma 6**. Let $\mathcal I$ be a fair division instance, and let $0\lt\delta\lt\frac{1}{2mv_\max}$ Then, an allocation $\mathbf x$ is $\delta$-EF1 for $\mathcal I$ iff it is EF1 for $\mathcal I$.

> ~~Omitted~~.

The proof of Theorem 1:

- $\mathcal I'$ is $\epsilon$-rounded version of $\mathcal I$ with $\epsilon=\frac{1}{14m^3v^4_\max}$. From Lemma 3 and 4, $\mathcal x$ is $3\epsilon$-EF1 and fPO for $\mathcal I'$ can be found in $\mathcal O(\text{poly}(m,n,\frac{1}{\epsilon},\ln v_\max))$ time.
- Under this choice, by lemma 5, $\mathbf x$ must be PO for $\mathcal I$. Thus $\mathbf x$ is EF1.

:thinking:**Lemma 7**. Given the $\epsilon$-rounded version $\mathcal I'$ as input, ALG finds a $7\epsilon$-EF1 and $\epsilon$-PO allocation for $\mathcal I$ in $\mathcal O(\text{poly}(m,n,\frac{1}{\epsilon},\ln v_\max))$ time.

> ~~I am lost.~~

# Theorem 2 EF1+fPO

:dart::thinking:**Theorem 2**. Given any fair division instance with additive valuations, there *always exists* an allocation that is EF1 and fPO.

> $\epsilon_z=\frac{1}{14zm^3v^4_\max},z\in\mathbb N$.
>
> ~~Suffocating~~.

# Theorem 3 Nash Social Welfare Approximation

ALG provides $1.45\approx e^{1/e}$-approximation for Nash social welfare maximization in polynomial time.

Use lemma 8 to prove lemma 1.

:thinking:**Lemma 8**. Let $\mathcal I$ be an instance with additive and **identical** valuation functions s.t. $m\ge n$. Let $B\subset[m]$ be a subset of goods s.t. $|B|\lt n$. Then, there is a partially-fractional allocation $\omega\in\mathcal F_B$ that maximize Nash social welfare among allocations in $\mathcal F_B$ s.t.

1. Each agent gets at most one goods from $B$ under $\omega$.
2. Any agent with strictly-better-than-the-worst allocation under $\omega$ gets exactly one integral good. That is, for any agent $i\in[n]$ s.t. $v(\omega_i)\gt\min_kv(\omega_k)$, we have $\omega_{i,j}=1$ for some $j\in B$ and $\omega_{i,j'}=0$ for all $j'\neq j$.

> Property 2. proved by contradiction.
>
> Property 1. proved by `swap` operation.

Proof of lemma 1: Too difficult.

:dart::thinking:**Theorem 3**. For additive valuations, there exists a polynomial-time $1.45$-approximation algorithm for the Nash social welfare maximization problem.

> ~~omitted~~.

# Conclusion

Based on integral Fisher markets and approximate pEF condition

Psudo-polynomial algorithm for finding EF1 and PO

Poly time $1.45$-approximation algorithm for Nash social welfare.

Future work:

- Does there exist polynomial time algorithm for EF1 + PO?
- How about more general classes of valuations?