---
title: 算法和计算复杂度 9 Circuit Lower Bounds
author: tinsir888
date: 2024/10/3
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: f0c10c56
---

Parity function is not in $AC^0$.

$MAJ\not\in AC^0$.

$NEXP\not\subseteq ACC^0$.

Some unknown proposition:

- whether $NP\subseteq ACC^0$.

- whether $EXP\subseteq ACC^0$.

- whether $NEXP\subseteq TC^0$.
- whether all languages in $NEXP$ can be computed by depth $2$ circuits consisting of weighted linear threshold gates.

# The Razborov-Smolensky Lower Bound

The lower bounds for class $AC^0[p^r]$, where $p$ is a prime.

Main idea: convert Boolean circuit of small size into a low-degree **probabilistic polynomial**.

Here we consider $r=1$.

:book:Definition 43. Let $\mathbb F$ be a field. By a probabilistic polynomial $P$ in $n$ variables of total degree $d$ over $\mathbb F$ we mean a random variable assuming as value such polynomials. Let $f:\{0,1\}^n\to\{0,1\}$ be a Boolean function. We say that $P$ computes $f$ with error $\epsilon$ if $\Pr[P(x)=f(x)]\ge1-\epsilon$ for all $x\in\{0,1\}^n$. Similarly we say that $P$ computes $f$ with one-sided error $\epsilon$ if $P$ computes $f$ with error $\epsilon$ and furthermore we have $\Pr[P(x)=f(x)]=1$ for all $x\in\{0,1\}^n$ where $f(x)=0$.

Then construct a probabilistic polynomial form an $AC^0[p]$ circuit.

:thinking:Lemma 13. Let $p$ be a prime and let $C$ be a depth $h$ circuit with at most $S$ unbounded fanin $AND,OR,MOD_p$ as well as negation gates computing a Boolean function $f$. Let $t\ge1$ be an arbitrary integer. Then for any $n$ there exist a probabilistic polynomial in $n$ variables of total degree at most $(pt)^h$ over $\mathbb Z_p$ computing $f$ with error $\frac{S}{p^t}$.

> Probabilistic polynomial for each of the gates of $C$. Each of these will be of degree at most $pt$ and compute the gate function with error $\frac{1}{p^t}$.
>
> Composing these will give probabilistic polynomial of degree at most $(pt)^h$ computing $f$ and with error at most $\frac{S}{p^t}$ by a union bound.
>
> Input gates: $x_i$ can be computed by the polynomials $x_i$ with error $0$. $\bar x_i$ can be computed by the polynomials $1-x_i$ with error $0$.
>
> Negation gates: $\neg z$ can be computed by the polynomial $1-y$ with error $0$.
>
> $MOD_p$ gate with inputs $z_1,\cdots,z_k$ can be computed by the polynomial
> $$
> \left(\sum_{i=1}^kz_i\right)^{p-1}
> $$
> with error $0$ by Fermat's little theorem.
>
> $OR$ gate with inputs $z_1,\cdots,z_k$: Consider picking $a_1,\cdots,a_k\in\mathbb Z_p$ uniformly and independently at random. Using Fermat's little theorem again, we have the polynomial
> $$
> \left(\sum_{i=1}^ka_iz_i\right)^{p-1}
> $$
> computes $OR(z_1,\cdots,z_k)$ with one-sided error $\frac{1}{p}$. In order to decrease the error, use success amplification using $t$ independent trials.
> $$
> 1-\prod_{j=1}^t\left(1-\left(\sum_{i=1}^ka_{ij}z_i\right)^{p-1}\right),
> $$
> which then computes $OR(z_1,\cdots,z_k)$ with one-sided error $\frac{1}{p^t}$.
>
> Analogously, for $AND$ gate, the probabilistic polynomial
> $$
> 1-\prod_{j=1}^t\left(1-\left(\sum_{i=1}^ka_{ij}(1-z_i)\right)^{p-1}\right).
> $$

:thinking:Lemma 14. Let $P$ be a probabilistic polynomial of degree $d$ and let $f:\{a,b\}^n\to\mathbb F$ be a function s.t. $\Pr[P(x)=f(x)]\ge1-\epsilon$. Then there exists a polynomial $P'$ of degree $d$ and a set $G\subseteq\{a,b\}^n$ with $|G|\ge(1-\epsilon)2^n$ s.t. $P(x)=f(x)$ for all $x\in G$.

> Linearity of expectation:
> $$
> \mathbb E[|x\in\{a,b\}^n:P(x)=f(x)|]=\sum_{x\in\{a,b\}^n}\Pr[P(x)=f(x)]\ge2^n(1-\epsilon),
> $$

A low-degree polynomial is equal to the product of all variables on a set $G\subseteq\{a,b\}^n$ enables one to replace all polynomials by equivalent polynomials on the set $G$ of degree significantly smaller than $n$.

:thinking:Lemma 15. Let $P$ be a polynomial in $n$ variables of total degree $\Delta$ over a field $\mathbb F$. Let $a,b\in\mathbb F$ and suppose $G\subseteq\{a,b\}^n$ is s.t.
$$
\forall y\in G:P(y)=\prod_{i=1}^ny_i.
$$
Then for every function $f:G\to\mathbb F$ there exists a polynomial $P_f$ of total degree at most $\frac{n+\Delta}{2}$ s.t.
$$
\forall y\in G:P_f(y)=f(y).
$$

> Let $f:G\to\mathbb F$ be any function. Define multilinear polynomial $Q_f$:
> $$
> Q_f(y)=\sum_{x\in G}\left(f(x)\left(
> \prod_{i:x_i=a}\frac{b-y_i}{b-a}
> \right)\left(
> \prod_{i:x_i=b}\frac{a-y_i}{a-b}
> \right)\right).
> $$
> Clearly, $Q_f(y)=f(y)\forall y\in G$. Using $P$, any monomial of degree $d$ can be replaced by an equivalent polynomial of degree $n-d+\Delta$ since
> $$
> \prod_{i\in I}y_i=\left(\prod_{i=1}^ny_i\right)\left(\prod_{i\not\in I}\frac{1}{y_i}\right)=
> P(y)\prod_{i\not\in I}\left(\frac{1}{a}\frac{b-y_i}{b-a}+\frac{1}{b}\frac{a-y_i}{a-b}\right).
> $$
> Thus we replace each monomial of $Q_f$ of degree $d\gt\frac{n+\Delta}{2}$ by an equivalent polynomial of degree $n-d+\Delta\lt\frac{n+\Delta}{2}$. We can thus take $P_f$ to be the sum of these together with the monomials of $Q_f$ degree at most $\frac{n+\Delta}{2}$.

One may convert any polynomial into a multi-linear polynomial of the same degree which is equivalent on a given hypercube $\{a,b\}^n$.

:thinking:Lemma 16. Let $a,b\in\mathbb F$ and let $P$ be a polynomial of degree $d$. Then there exists a multi-linear polynomial $P'$ of degree at most $d$ s.t. $P(x)=P'(x)$ for all $x\in\{a,b\}^n$.

> An occurrence of $x_i^k$ in $P$ may be replaced by the polynomial
> $$
> \frac{x_i-b}{a-b}a^k+\frac{x_i-a}{b-a}b^k,
> $$
> which can never increase the degree.

:thinking:Lemma 17. For all integers $n\ge 1$:
$$
\frac{4^n}{\sqrt{\pi(n+1)}}\le C_{2n}^n\le\frac{4^n}{\sqrt{\pi n}}
$$
:thinking:Corollary 10. For all integers $n\ge1$:
$$
C_{\lceil n/2\rceil}^n\le\frac{2^n}{\sqrt{\pi\lceil n/2\rceil}}.
$$

:thinking:Proposition 13. Let $P$ be a polynomial in $n$ variables of total degree $\Delta$ over a field $\mathbb F$. Let $a,b\in\mathbb F$ and suppose $G\subseteq \{a,b\}^n$ is s.t.
$$
y\in G:P(y)=\prod_{i=1}^ny_i.
$$
Then
$$
|G|\le 2^n\left(\frac{1}{2}+\frac{4}{10}\frac{\Delta}{\sqrt n}\right).
$$

> By Lemma 16, assume that $P$ is multilinear.
>
> The number of functions $f:G\to\mathbb F$, which amounts to $p^{|G|}$, must be less than the number of multilinear polynomials of degree at most $\frac{n+\Delta}{2}$, which amounts to $p^M$, where $M$ denotes the number of monomials on $n$ variables of degree at most $\frac{n+\Delta}{2}$. It follows that $|G|\le M$.
>
> Then conclude by estimating $M$.
> $$
> M=\sum_{i=0}^{\lfloor\frac{n+\Delta}{2}\rfloor}C_n^i=2^{n-1}+\sum_{i=\lceil n/2\rceil}^{\lfloor\frac{n+\Delta}{2}\rfloor}C_n^i\le2^{n-1}+\frac{\Delta}{2}\frac{2^n}{\sqrt{\pi n/2}}\le 2^{n-1}+\frac{4}{10}\frac{\Delta2^n}{\sqrt n},
> $$
> where $C_n^i$ is estimated by central binomial coefficient, for $i\gt n/2$, and use $\sqrt{2\pi}\ge\frac{10}{4}$,

Finally combine the construction of the probabilistic polynomials with the counting argument to derive a circuit lower bound.

:dart:Theorem 46 . Let $p\neq 2$ be a prime. Suppose that $C$ is depth $h$ circuit with at most $S$ unbounded fanin $AND,OR,MOD_p$ as well as $NEG$ gates computing $MOD_2$. Then
$$
S=2^{\Omega(n^{\frac{1}{2h}})}.
$$

> Lemma 13, Lemma 14, Proposition 13.

:dart:Theorem 47 (Smolensky). Let $p$ be a prime. Suppose that $C$ is a depth $h$ circuit with at most $S$ unbounded fanin $AND,OR,MOD_{p^r}$ gates for a constant $r\ge1$ as well as $NEG$ gates computing function $MOD_q$, where $q\neq p$ is a prime. Then
$$
S=2^{\Omega(n^{\frac{1}{2h}})}.
$$

> ~~这个电路下界真的是人想的出来的吗！！！~~