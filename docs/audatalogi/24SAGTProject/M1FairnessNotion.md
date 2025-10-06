---
title: Fair Division 1 Survey Study
author: tinsir888
date: 2024/2/2
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: 92da76c1
---

在和扬尼斯教授交流后，我确定了 24 Spring 这 10 ECT 的 Projektarbejde i Datalogi 的具体方向为“不可分割物品的公平分配博弈” Fair Division with Indivisible Items

美美考完算法博弈论，从扬尼斯教授这拿了 12 分之后，我就得开搞 Projekt 了。

# Objective

Gain familiarity with fairness notions and key results in fair division with indivisible items.

# Fairness Notions

## Denotation

Set $N$ of $n$ agents

Set $M$ of $m$ indivisible goods

For agent $i$:

- Valuation function $v_i:2^M\rightarrow\mathbb R_{\ge0}$.

  $v_i(\emptyset)=0,v_i(S)\le v_i(T)\forall S\subseteq T\subseteq M$

  **Assume valuation function are additive** $v_i(S)=\bigcup_{g\in S}v_i(g)$ ==(My question: Does this imply single parameter environment? Why?)==

Allocation instance $I=(N,M,v)$, $v=(v_1,\cdots,v_n)$ is the vector of valuation functions.

Allocation $A=(A_1,\cdots,A_n)$ s. t. each agent $i$ receive the bundle $A_i\subseteq M$, $A_i\cap A_j=\emptyset\forall i\neq j$, $\bigcup_{i\in N}A_i=M$.

If $\bigcup_{i\in N}A_i\subsetneq M$, the allocation is called partial.

## Familiy of Envy-freeness

### Envy-freeness

$A$ is EF if $\forall i,j\in N$, $v_i(A_i)\ge v_i(A_j)$.

Deciding whether an instance admits EF is NP-complete.

EF is not always exist.

### Envy-freeness up to one good

EF1, a relaxation of EF.

$A$ is EF1 if $\forall i,j\in N$, $v_i(A_i)\ge v_i(A_j\diagdown\{g\})$ for **some** $g\in A_j$.

Very weak notion: $g$ maybe high-valued.

> My difinition: $A$ is EF1 if $\forall i,j\in N,v_i(A_i)\ge v_i(A_i\diagdown\{g\})$ for $g=\arg\max_{t\in A_j}v_i(t)$

#### Computing EF1

Round Robin Algoithm

Envy-Cycle elimination

#### EF1 and Pareto Optimality

MNW allocation: an allocation $A$ is MNW if

1. It maximizes number of agents with positive value
2. It maximizes the Nash welfare (the product of valuation) for agents with positive value.

MNW$\rightarrow$EF1+PO

:question:Can EF1+PO be computed in P time?

### Envy-freeness up to any good

EFX, a relaxation of EF stricter than EF1.

$A$ is EFX if $\forall i,j\in N$, $v_i(A_i)\ge v_i(A_j\diagdown\{g\})$ for **any** $g\in A_j$.

:question:Open problem: The existence of EFX? ($n\ge4$? Unrestricted additive valuations?)

> My difinition: $A$ is EFX if $\forall i,j\in N,v_i(A_i)\ge v_i(A_i\diagdown\{g\})$ for $g=\arg\min_{t\in A_j}v_i(t)$

#### Approximately EFX

$\alpha$-EFX: $\alpha\in[0,1]$ if $\forall i,j\in N$, $v_i(A_i)\ge\alpha\times v_i(A_j\diagdown\{g\})$ for **any** $g\in A_j$.

$\frac{1}{2}$-EFX always exists for sub-additive valuation functions.

Envy-Cycle Elimination algorithm computes $1\over2$-EFX allocations.

:question:What is the best possible $\alpha$ for $\alpha$-EFX allocation?

Loose: must allocate all goods$\rightarrow$allocate part of goods.

:question:Is it possible to achieve an exact EFX allocation by donating a sub-linear number of goods?

### Other types of Envy-freeness

Envy-freeness up to one less-preferred good. EFL, between EF1 and EFX.

- EFL always exists, can be computed using a variant of Envy-Cycle Elimination.

Envy-freeness up to a random good. EFR

- Randomized version. 
- $0.73$-EFR allocation can be computed in polynomial time.

:question:Do EFR allocations always exist?

Epistemic Fairness

- Agents don't have complete knowledge about allcation.

## Familiy of Proportionality

### Proportionality

$A$ is PROP if $\forall i\in N$, $v_i(A_i)\ge\frac{v_i(M)}{n}$.

Obviously EF$\rightarrow$PROP, PROP$\not\rightarrow$EF.

Deciding whether an instance admits PROP is also NP-complete.

#### Prop1

Propotionality up to one good

Prop1+PO always exists, can be computed in P time.

#### PropX

Propotionality up to any good

Can not always be guaranteed.

#### PropM

Propotionality up to maximin good

Between Prop1 and PropX

The value of a maximin good for $i$ is $d_i(A)=\max_{j\neq i}\min_{g\in A_j}v_i(g)$

$A$ is called PropM if $v_i(A_i)+d_i(A)\ge\frac{v_i(M)}{n}$,

PropM always exists, can be computed in P time.

### Maximin Share Fairness

MMS, a relaxation of PROP, is trying to give each agent $i$ bundles of value at least as her *maximin share* $\mu_i^n(M)$.

Maximin share is the worst value agent can guarantee for herself by partitioning $M$ into $n$ disjoint bundles and keeping the worst of them.

Let $\mathcal A_n(M)$ be te collection of possible allocations of the goods in $M$ to $n$ agents.

$A$ is MMS if $\forall i\in N$:
$$
v_i(A_i)\ge\mu_i^n(M)=\max_{B\in\mathcal A_n(M)}\min_{S\in B}v_i(S)
$$
Computing MMS even Maximin share of an agent is NP-hard.

MMS don't always exist when there are more than 2 agents.

#### Approximately MMS

$\alpha$-MMS: $\forall i\in N$, $v_i(A_i)\ge\alpha\times\mu_i^n(M)$

:question:Is it possible to improve upon the bound of $\frac{3}{4}+\frac{1}{12n}$ for additive valuations? Is there a stronger inapproximability bound than $39\over40$?

:question:Are there other class of structured valuations for which MMS is guaranteed to exist?

#### PMMS

Pairwise maximin share fairness
$$
\forall i,j\in N,v_i(A_i)\ge\max_{A_i'\cup A_j'=A_i\cup A_j}\min\{v_i(A_i'),v_i(A_j')\}
$$
$\alpha$-PMMS

:question:Do PMMS allocations always exist?

#### GMMS

Groupwise maximin share fairness

$\alpha$-GMMS: if $\forall i\in N$, $v_i(A_i)\ge\alpha\cdot\text{GMMS}_i$
$$
\text{GMMS}_i=\max_{S\subseteq N:i\in S}\mu_i^{|S|}(\bigcup_{j\in S}A_j)
$$
Best known: $4\over7$-GMMS

:question:What's the best possibe $\alpha$ for $\alpha$-GMMS?

# Efficiency and Incentives

Fair and Pareto Optimal Allocations

Approximating the Nash Welfare

Price of Fairness
$$
\text{PoF}=\sup_I\min_{A\in F(O)}\frac{OPT(I)}{SW(A)}\\
=\frac{\max_{D\in\text{Division}}SW(D)}{\max_{S\in\text{Fair Division}}SW(S)}
$$
Fair Divsion with Strategic Agents