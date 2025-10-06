---
title: '随机算法 2 Probabilistic Inequalities, Hashing with Chaining'
author: tinsir888
date: 2024/2/5
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: '36868314'
---

This time: Kasper Green Larsen

No slides!

# Basic Probabilistic Inequalities

The Dictionary Problem: set $S$ of $n$ keys: $x_1,\cdots,x_n$ from a universe $[U]=\{0,\cdots,U-1\}$. Goal: To store $S$ in a data structure s.t., given a query element $x\in[U]$, we can quickly determine whether $x\in S$.

Hashing with Chaining: A data structure solves the dictionary problems: Construct an array $A$ with $m$ entries, denoted $A[0],\cdots,A[m-1]$.

- Each array entry stores an initially empty linked list.

- Denote the list stored at entry $i$ by $List(A[i])$.

- Pick a random hash function $h:[U]\rightarrow[m]$

  Assumption: $h$ is truly random and can be evaluated in constant time, i.e.,

  1. $\forall$ set of distinct elements $\{x_1,\cdots,x_k\}\subseteq[U]$ and $\forall$ set of values $v_1,\cdots,v_k\in[m]$, we have $\Pr_h[h(x_1)=v_1\and\cdots\and h(x_k)=v_k]=\frac{1}{m^k}$.
  2. we can compute $h(x)$ in $O(1)$ time.

- After choosing $h$, we process $x_1,\cdots,x_n$ of $S$. For element $x_i$, we compute $h(x_i)$ and append $x_i$ to the list $List(A[h(x_i)])$.

- To answer whether an element $x$ is in $S$, we simply compute $h(x)$ and scan through the list $List(A[h(x)])$ to see if it is there.

- Space: $O(m+n)$.

## Linearity of Expectation

Analyze the expected query time of hashing in chaining.

The work spend when answering the query $x$ is proportional to the number of elements from $S$ also stored in $List(A[h(x)])$.

Bad idea: compute $\mathbb E[List(A[h(x)])]$ directly: (Too hard)
$$
=\sum_{i=0}^ni\cdot\Pr\{i=List(A[h(x)])\}\\
=\sum_{i=0}^ni\cdot(\frac{1}{m})^i(1-\frac{1}{m})^{n-i}C_n^i
$$
Good idea: break down $X$.

Random variable $X_i$ for each $x_i$, if $h(x_i)=h(x), X_i=1$, else $X_i=0$.

The expected query time is $O(\mathbb E_h[\sum_iX_i])$

Definition of expectation: $\mathbb E[X]=\sum_{x\in X}Pr[X=x]\cdot x$

Linearity of expectation: $\mathbb E[\sum_ix_i]=\sum_i\mathbb E[x_i]$

Thus, by linearity of expectation,
$$
\mathbb E_h[\sum_iX_i]=\sum_i\mathbb E_h[X_i]\\
\mathbb E_h[X_i]=\Pr[h(x_i)=h(x)]\cdot1+\Pr[h(x_i)\neq h(x)]\cdot0\\=\Pr[h(x_i)=h(x)]
$$
if $x_i=x,\Pr[h(x_i)=h(x)]=1$. if $x_i\neq x,\Pr[h(x_i)=h(x)]=\frac{1}{m}$.

Thus,
$$
\sum_i\mathbb E_h[X_i]\le1+\sum_{i:x_i\neq x}\mathbb E_h[X_i]\le1+\frac{n}{m}
$$
choose $m=\Theta(n)$, then query time is $O(1)$ and space usage is $O(n)$.

**Linearity of expectation is also valid if random variables are not independent!!!**

## Markov's Inequality

Let $X$ be a **non-negative** discrete random variable. $\forall t\gt0$, we have $\Pr[x\gt t]\lt\frac{\mathbb E[X]}{t}$ and $\Pr[x\ge t]\le\frac{\mathbb E[X]}{t}$

> Proof for continuous version
> $$
> \mathbb E[X]=\int_0^\infty xf(x)dx\\
> \forall t>0,\mathbb E[X]\ge\int_t^\infty xf(x)dx\\
> \ge\int_t^\infty tf(x)dx=t\int_t^\infty f(x)dx=t\cdot\Pr(X\ge t)
> $$

Conclusion: If we insert $n$ elements into hashing with chaining data structure and then ask one query $x$, then the linked list $List(A[h(x)])$ that we have to scan through contains more than $t(1+\frac{n}{m})$ elements with probability less than $1\over t$.
$$
\Pr[X\gt t(1+\frac{n}{m})]\lt\frac{\mathbb E[X]}{t(1+\frac{n}{m})}\le\frac{1+\frac{n}{m}}{t(1+\frac{n}{m})}=\frac{1}{t}
$$

Markov's Inequality gives a loose bound: $\Pr[List(A[h(x)])=n] \le(1+n/m)/n=O(1/n)(m=\Theta(n))$

## Chernoff Bound

:cold_sweat::cold_sweat::cold_sweat:~~What the hell is this?!~~

Introducing random variable $Y_j$: $Y_j=1$ if $h(x_j)=i$ else $Y_j=0$.

We have $|List(A[i])|=\sum_jY_j$.

$Y_j$s are independent and $\mathbb E[Y_j]=\frac{1}{m}$.

Chernoff Bound: Let $\{X_i\}$ be a set of **independent boolean random variables** and let $X=\sum_iX_i$. Then,

$\forall\mu\le\mathbb E[X],\forall0\lt\delta\lt1$ we have
$$
\Pr[X\lt(1-\delta)\mu]\lt e^{-\frac{\delta^2\mu}{2}}
$$
$\forall\mu\ge\mathbb E[X],\forall0\lt\delta\lt1$ we have
$$
\Pr[X\gt(1+\delta)\mu]\lt e^{-\frac{\delta^2\mu}{3}}
$$
Finally, $\forall\delta\ge1,\forall\mu\ge\mathbb E[X]$, we have
$$
\Pr[X\gt(1+\delta)\mu]\lt(\frac{e^\delta}{(1+\delta)^{1+\delta}})^\mu
$$


We have $\mathbb E[Y]=\frac{n}{m}$, $\forall\delta\ge1$, we have **Chernoff Bound**:
$$
\Pr[Y\gt(1+\delta)\frac{n}{m}]\lt(\frac{e^\delta}{(1+\delta)^{1+\delta}})^{\frac{n}{m}}
$$

Application in our scenario: $m=n,t=(1+\delta)\ge2,\mu=\mathbb E[Y]={n\over m}=O(1)$:
$$
\Pr[|List(A[i])|\gt t]=\Pr[Y\gt t]\lt\frac{e^{t-1}}{t^t}\lt(\frac{e}{t})^t
$$
Conclusion: we spend more than $O(t)$ time with probability decreasing as fast as $t^{-t}$.

> Proof of Chernoff Bound
>
> By Markov's inequality,
> $$
> \forall t\gt0:\Pr(x\gt a)=\Pr(e^{tX}\gt e^{ta})\lt\frac{\mathbb E[e^{tX}]}{e^{ta}}\\
> \text{Denote }M_X(t)=\mathbb E[e^{tX}], M_X(t)=\prod_{i=1}^nM_{X_i}(t)\\
> M_X(t)=pe^t+(1-p)=1+p(e^t-1)\le e^{p(e^t-1)}\\
> \therefore\Pr(X\gt(1+\delta)\mu)\lt\frac{\mathbb E[e^{tX}]}{e^{t(1+\delta)\mu}}=\frac{\prod_{i=1}^nM_{X_i}(t)}{e^{t(1+\delta)\mu}}\\
> \le\frac{e^{(e^t-1)\sum_ip_i}}{e^{t(1+\delta)\mu}}=\frac{e^{(e^t-1)\mathbb E[X]}}{e^{t(1+\delta)\mu}}\le[\frac{e^{e^t-1}}{e^{t(1+\delta)}}]^\mu\\
> $$
> Let $t=\ln(1+\delta)\ge0$, we have:
> $$
> \Pr[X\gt(1+\delta)\mu]\lt(\frac{e^\delta}{(1+\delta)^{1+\delta}})^\mu\\
> \Rightarrow\ln[(\frac{e^\delta}{(1+\delta)^{1+\delta}})^\mu]=\mu(\delta-(1+\delta)\ln(1+\delta))\\
> \because\forall x\ge0,\ln(1+x)\ge\frac{x}{1+\frac{x}{2}}\\
> \therefore\mu(\delta-(1+\delta)\ln(1+\delta))\le-\frac{\delta^2}{2+\delta}\mu\\
> \lt-\frac{\mu\delta^2}{3}(\because0\lt\delta\lt1)\\
> \Pr[X\gt(1+\delta)\mu]\lt e^{-\frac{\delta^2\mu}{3}}(0\lt\delta\lt1)
> $$
>
> 
>
> Analogously, we have
> $$
> \forall t\gt0:\Pr(x\lt a)=\Pr(e^{-tX}\gt e^{-ta})\lt\frac{\mathbb E[e^{-tX}]}{e^{-ta}}\\
> \Rarr\cdots\Rarr\cdots\\
> \Rarr\Pr[X\lt(1-\delta)\mu]\lt(\frac{e^{e^{-t}-1}}{e^{-t(1-\delta)}})^\mu(**)\\
> $$
> Let $t=-\ln(1-\delta)\gt0$,
> $$
> (**)=(\frac{e^{-\delta}}{(1-\delta)^{1-\delta}})^\mu(*)\\
> \because \forall0\lt\delta\lt1,(1-\delta)\ln(1-\delta)\gt-\delta+\frac{\delta^2}{2}\\
> \therefore(*)\le(\frac{e^{-\delta}}{e^{-\delta+\frac{\delta^2}{2}}})^\mu=e^{-\frac{\delta^2\mu}{2}}\\
> \Rightarrow\Pr[X\lt(1-\delta)\mu]\lt e^{-\frac{\delta^2\mu}{2}}
> $$

## Union Bound

Let $\{E_i\}$ be events, then
$$
\Pr[\bigcup_iE_i]\le\sum_i\Pr[E_i]
$$
If we define $E_i$ as event that $|List(A[i])|\gt4\frac{\ln n}{\ln\ln n}$, then by Chernoff Bound,
$$
\Pr[E_i]\lt((e/4)\ln\ln n/\ln n)^{4\ln n/\ln\ln n}\lt(\ln\ln n/\ln n)^{4\ln n/\ln\ln n}
$$
($n\gt3\gt e$ is needed!)

:astonished:~~CRAZY MATH!!!~~
$$
\because \ln\ln n\le\sqrt{\ln n}\\
\therefore(\frac{\ln\ln n}{\ln n})^{\frac{4\ln n}{\ln\ln n}}\le(\frac{\sqrt{\ln n}}{\ln n})^{\frac{4\ln n}{\ln\ln n}}=(\ln n)^{-\frac{2\ln n}{\ln\ln n}}\\
=(e^{\ln\ln n})^{-2\frac{\ln n}{\ln\ln n}}=e^{-2\frac{\ln n}{\ln\ln n}\cdot\ln\ln n}=n^{-2}
$$
By union bound, we have
$$
\Pr[\bigcup_iE_i]\le\sum_in^{-2}=n^{-1}
$$
This means with probability $1-\frac{1}{n}$, there is not even a single list $List(A[i])$ with size more than $4\frac{\ln n}{\ln\ln n}$, assuming $(n\ge3)$.

In this case, the worst case query time of the data structure is $O(\frac{\log n}{\log\log n})$

Hashing with Chaining with $m=n$, the worst query time is $O(\frac{\log n}{\log\log n})$ with probability $1-\frac{1}{n}$ when $h$ is truly random.

The construction time is $O(n)$ and the space is $O(n)$.

### Worst Case

After insert $x_1,\cdots,x_n$, one of the list exceeds $4\frac{\ln n}{\ln\ln n}$, then we discard the current data structure and try again with a freshly chosen $h$.

This performs at least $i$ iterations with probability no more than $(\frac{1}{n})^{i-1}$, thus the expected construction time is upper bounded by
$$
\sum_{i=1}^\infty(1/n)^{i-1}\cdot O(n)=O(n)
$$
We have thus achieved a worst case query time of $O(\log n/\log\log n)$ with space $O(n)$ and expected construction time $O(n)$.

# O-Notation

中文中有个很好的对应概念”数量级“

For two functions $f:\mathbb R\rightarrow\mathbb R,g:\mathbb R\rightarrow\mathbb R$, we can write $f(n)=O(g(n))$ if:

- There exists a constants $n_0\in\mathbb R$ and $c\gt0$, s.t. $\forall n\gt n_0$, we have $f(n)\le c\cdot g(n)$.

  e.g. $n^3/6-n^2/2=O(n^3)$

Sum of same magnitude. if $\forall i=1,\cdots,k,f_i(n)=O(g(n))$, then $\sum_{i=1}^kf_i(n)=O(g(n))$

Constants don't matter!

Products. if $f(n)=O(h(n)),g(n)=O(l(n))$ then
$$
f(n)g(n)=O(h(n)l(n))
$$
Base of Logs. The base of logarithms don't matter! We only use $f(n)=O(\log n)$.

Careful with exponents!!! $2\log_2n=O(\log n)$, but $2^{2\log_2n}=O(n^2)$

