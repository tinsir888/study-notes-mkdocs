---
title: Fair Division Conjectures on Price of Fairness for EEFX/MXS
author: tinsir888
date: 2024/3/22
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: ef3cad20
---

After reading the paper [*Optimal Bounds on the Price of Fairness for Indivisible Goods*](https://arxiv.org/abs/2007.06242) to know the notion of Price of Fairness and the paper [*New Fairness Concepts for Allocating Indivisible Items*](https://www.ijcai.org/proceedings/2023/284) to adapt new fairness notions EEFX and MXS, it is natural to consider the left open problem: **What is the PoF for EEFX and MXS?**

# The Relation between EFX, EEFX, MXS, PROP1

It is not hard to say that

$$
EFX\subset EEFX\subset MXS\subset PROP1
$$

- Every EFX allocation is EEFX allocation.
- Every EEFX allocation is MXS allocation.
- Every MXS allocation is PROP1 allocation.

# Known PoF on EFX and PROP1

Assumption: The valuation function are additive.

- Scaled valuation: The valuation each player for all goods are the same.
- Unscaled valuation: The valuation each player for all goods are not the same.

## PROP1 PoF Bounds

The corollary 1 of [*Optimal Bounds on the Price of Fairness for Indivisible Goods*](https://arxiv.org/abs/2007.06242): The price of PROP1 is $\Theta(\sqrt n)$ for scaled additive valuations and $\Theta(n)$ for unscaled additive valuations.

## EFX Bounds

The Theorem F.5 and Theorem F.6 of [*On the Complexity of Maximizing Social Welfare within Fair Allocations of Indivisible Goods*](https://arxiv.org/abs/2205.14296): The price of EFX is $O(\sqrt n)$ for scaled additive valuations and $\Theta(n)$ for unscaled valuations.

# My Conjectures 😎

Based on latent relations between EFX, EEFX, MXS, PROP1 and proved PoF bounds on EFX and PROP1, I made the reasonable conjectures on PoF bounds for EEFX and MXS:

For EEFX allocation, the PoF is

- $\Theta(\sqrt n)$ for scaled valuations.
- $\Theta(n)$ for unscaled valuations.

For MXS allocation, the PoF is

- $\Theta(\sqrt n)$ for scaled valuations.
- $\Theta(n)$ for unscaled valuations.

## Analysis

Break down my conjectures.

### Unscaled valuations

We consider unscaled valuations first.

$\forall$ instance, $\exists$ EEFX (or MXS) allocations $X$ with $SW(X)\ge\frac{1}{cn}SW(X^*)$, i.e., Price of EEFX (or MXS) is $O(n)$. ==This one is easy.==

$\exists$ instance, in which any EEFX (or MXS) allocation has $SW(X)\le\frac{1}{cn}SW(X^*)$, i.e., Price of EEFX (or MXS) is $\Omega(n)$. ==This one is not easy. Not solved!==

### Scaled valuations

$\forall$ instance, $\exists$ EEFX (or MXS) allocations $X$ with $SW(X)\ge\frac{1}{c\sqrt n}SW(X^*)$, i.e., Price of EEFX (or MXS) is $O(\sqrt n)$.

$\exists$ instance, in which any EEFX (or MXS) allocation has $SW(X)\le\frac{1}{c\sqrt n}SW(X^*)$, i.e., Price of EEFX (or MXS) is $\Omega(\sqrt n)$.

For scaled valuations, we have not got the idea yet. ==Not solved!==

## Trying to Prove

By considering the algorithm in the paper (introducing EEFX & MXS) which produces EEFX allocation:

1. Ordering all items w.r.t. each agent's valuations.
2. Running Envy-Cycle Elimination on ordered instance yields EFX allocation.
3. Using the same picking sequence of step 2. to let agents pick items on original instance.

We have known that the final allocation has better social welfare than EFX allocation in step 2.

:question:Then what is the PoF of EFX for ordered instances?

> Consider such an ordered instance:
>
> 1. $m=n+1$.
> 2. $Y\gt\gt 1$.
> 3. $\epsilon\rightarrow0^+$.
> 4. Valuation table:
>
> | agent\item | g~1~ | g~2~ | ...  | g~n~ | g~n+1~ |
> | :--------: | :--: | :--: | :--: | :--: | :----: |
> |     1      |  Y   |  Y   | ...  |  Y   |   ɛ    |
> |     2      |  1   |  1   | ...  |  1   |   ɛ    |
> |    ...     | ...  | ...  | ...  | ...  |  ...   |
> |    n-1     |  1   |  1   | ...  |  1   |   ɛ    |
> |     n      |  1   |  1   | ...  |  1   |   ɛ    |
>
> The EEFX allocation maximizing social welfare is:
>
> - Allocating one good among $g_1,\cdots g_n$ to agent $1$.
> - Allocating one good among $g_1,\cdots,g_n$ to agent $2,\cdots,n$
> - Arbitrarily allocating $g_{n+1}$.
>
> In this instance:
>
> - $\max_{X\in \text{EFX,ordered instance}}SW(X)=Y+(n-1)+\epsilon$.
> - Obviously, $SW(X^*)=nY+\epsilon$.
>
> Thus, PoF of EFX for ordered instance is $\frac{SW(X^*)}{\max SW(X)}=O(n)$.
>
> Analogously, in this instance,
>
> - MXS share for agent 1: $\ge Y$.
> - MXS share for agent 2 to n: $1$.
>
> The PoF bound also holds for MXS.

It seems that we solve the lower bound for unscaled valuation.

What about lower bound for scaled valuation?

# The PoF on Ordered Instance with Scaled Valuations

(update on 5. April 2024)

Consider the algorithm that produce EEFX. It firstly "order" all the items with respect to each agents.

It's crucial to analysis the price of fairness on ordered instance.

Because in ordered instance, each agent has identical preference on the same goods. More importantly, on such instance, $EF1=EEFX=EFX$!

Consider such an instance:

- $n$ agents
- $n+1$ items
- Scaled valuations: each agent's value for all item is $1+\epsilon$
- Each agent has same preference on the same item

$$
\epsilon\rightarrow0\\
\begin{array}{cc}
  & g_1 & g_2 & g_3&g_4&g_5& \cdots & g_n & g_{n+1}\\
1 & 1 & \frac{\epsilon}{n} & \frac{\epsilon}{n}&\frac{\epsilon}{n}&\frac{\epsilon}{n}&\cdots&\frac{\epsilon}{n}&\frac{\epsilon}{n}\\
2 & \frac{1}{2}&\frac{1}{2}&\frac{\epsilon}{n-1}&\frac{\epsilon}{n-1}&\frac{\epsilon}{n-1}&\cdots&\frac{\epsilon}{n-1}&\frac{\epsilon}{n-1}\\
3&\frac{1}{4}&\frac{1}{4}&\frac{1}{4}&\frac{1}{4}&\frac{\epsilon}{n-3}&\cdots&\frac{\epsilon}{n-3}&\frac{\epsilon}{n-3}\\
\vdots&\vdots&\vdots&\vdots&\vdots&\vdots&\vdots&\vdots&\vdots\\
1+\log_2 n&\frac{1}{n}&\frac{1}{n}&\frac{1}{n}&\frac{1}{n}&\frac{1}{n}&\cdots&\frac{1}{n}&\epsilon\\
\vdots&\vdots&\vdots&\vdots&\vdots&\vdots&\vdots&\vdots&\vdots\\
n&\frac{1}{n}&\frac{1}{n}&\frac{1}{n}&\frac{1}{n}&\frac{1}{n}&\cdots&\frac{1}{n}&\epsilon\\
\end{array}
$$

:fist_oncoming:

- The maximum social welfare is

  $$
  SW(\mathcal A^*)=1+1\times\frac{1}{2}+2\times\frac{1}{4}+4\times\frac{1}{8}+\cdots
  $$
  
  $$
  +2^{\log_2 n-1}\times\frac{1}{n}+\epsilon\\
  $$
  
  $$
  =1+\frac{\log_2n}{2}
  $$
  

- The maximum social welfare on fair allocation (EF1, EFX, EEFX are the same) is

  $$
  \max_{\mathcal A\text{ is fair allocation}}SW(\mathcal A)=1+\frac{1}{2}+\frac{1}{4}+\cdots+\frac{1}{2^{\log_2n}}+(n-\log_2n-1)\frac{1}{n}+\epsilon\\
  $$

  $$
  \le2+1+\epsilon-(\log_2n+1)\frac{1}{n}
  $$

  By $\epsilon\rightarrow0$, this quantity $\le3$.

- In this case,
  $$
  PoF=\Theta(\log n)
  $$

Naturally we come up with two open question about this:

:question: Is there a worse counter-example (which shows larger bound like $\Theta(\log^2n)$)?

:question: Prove a better than $O(\sqrt n)$ upper bound for ordered instances?

