---
title: 算法和计算复杂度 6 Randomized Computation
author: tinsir888
date: 2024/8/2
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: f79c8719
---

What if Turing machine models have access to a source of randomness?

This would allow to compute more in same time/space, at the price of possibly not giving correct answer with small probability.

Current consensus: Randomization only gives

- Polynomial saving in time
- Constant factor saving in space

Because of hardness-based pseudo-random generator.

Exponential time: $E=DTIME(2^{O(n)})=U_L-SIZE(2^{O(n)})$. (By corollary 2)

Recall that $SIZE(2^n/n)$ includes every Boolean function on $n$ variables, making non-uniform inclusion $E\subseteq SIZE(2^{O(n)})$.

Corresponding randomized complexity class $BPP$.

Theorem 33 (Impagliazzo and Wigderson). If there exists $L\in E$ s.t. for every $n$ and every Boolean circuit $C$ computing $L$ on input length $n$, the size of $C$ must be $2^{\Omega(n)}$, then $P=BPP$.

# Probabilistic Turing Machines

Give a probabilistic Turing machine an extra read-only and read-once tape which is pre-initialized with uniform and independent random bits. The tape head is allow to only stop or move forward.

Thus everything related to the Turing machines becomes a random variable.

## Different kinds of Errors

**Zero-sided** error aka Las Vegas computation: Turing machine halt in a state "?" with small probability and require correct answer otherwise.

**One-sided** error: Turing machine may produce false negatives.

**Two-sided** error: Turing machine may produce false negatives and false positives.

:book:Definition 19. Let $M$ be a probabilistic Turing machine and $L$ a language.

- $M$ computes $L$ with zero-sided error if for all $x$ we have $\Pr[M(x)=?]\le\frac{1}{4}$, that $M(x)\in\{yes,?\}$ when $x\in L$, and that $M(x)\in\{no,?\}$ when $x\not\in L$.
- $M$ computes $L$ with one-sided error if for all $x\in L$ we have $\Pr[M(x)=yes]\ge\frac{3}{4}$ and for all $x\not\in L$ we have $M(x)=no$.
- $M$ computes $L$ with two-sided error if for all $x\in L$ we have $\Pr[M(x)=yes]\ge\frac{3}{4}$ and for all $x\not\in L$ we have $\Pr[M(x)=no]\ge\frac{3}{4}$.

:book:Definition 20. Let $T:\mathbb N\rightarrow\mathbb N$ and $S:\mathbb N\rightarrow\mathbb N$ be functions with $T(n)\ge n$.

- Then $ZPTIME(T(n))$, $RTIME(T(n))$ and $BPTIME(T(n))$ are the classes of languages computed by a $O(T(n))$ time-bounded probabilistic Turing machine with zero-sided error, one-sided error and two-sided error respectively.
- $ZPSPACE(S(n))$, $RSPACE(S(n))$ and $BPSPACE(S(n))$ are the classes of languages computed by a $O(S(n))$ space-bounded probabilistic Turing machine **that always halt** having zero-sided error, one-sided error and two-sided error respectively.

!!! info

    For space bounded randomized classes, we assume that the Turing machine always halts! Because otherwise these three classes would end up containing $NSPACE(S(n))$!



Define log-space/polynomial time bounded classes:

$$
ZPL=ZPSPACE(\log n)\\
$$

$$
RP=RSPACE(\log n)\\
$$

$$
BPL=BPSPACE(\log n)\\
$$

$$
ZPP=ZPTIME(n^{O(1)})\\
$$

$$
RP=RTIME(n^{O(1)})\\
$$

$$
BPP=BPTIME(n^{O(1)})
$$

Trivially, $L\subseteq ZPL\subseteq RL\subseteq BPL$; $P\subseteq ZPP\subseteq RP\subseteq BPP$.

It's also easy to see that $RL\subseteq NL$ and $RP\subseteq NP$.

Simple upper bound for $BPP$ (not tight): similar to theorem 10, $BPP\subseteq PSPACE$.

For $BPL$, $BPL\subseteq DSPACE(\log^{3/2}n)$.

# Success Amplification

Error probability of $1/4$ is large, but we can reduce it by independently running multiple times.

For zero-sided error, we won't stop until we get non-unknown answer.

For one-sided error, if some trial accepts, we accept. otherwise we reject.

For two-sided error, we decided the answer by the majority of trials.

:thinking:Lemma 3. Let $X_1,\cdots,X_k$ be independent Boolean variables s.t. $\Pr[X_i=1]\ge\epsilon$. Then

$$
\Pr[\forall i:X_i=0]\lt\exp(-k\epsilon)
$$

> By independence,
>
> $$
> \Pr[\forall i:X_i=0]=\prod_i\Pr[X_i=0]\lt(1-\epsilon)^k\le\exp(-k\epsilon).
> $$
>
> (by $1+x\le\exp(x)$)

Apply this to zero-sided and one-sided errors:

- Define $X_i$ to indicate whether trial $i$ gives the correct output.
- Instead of having error probability $1/4$, we actually assume that error probability for once is less than $1-\frac{1}{p(n)}$ and still achieve error probability $\exp(-q(n))$ for all by running $k=p(n)q(n)$ trials, which is polynomial number.

For two-sided error:

:thinking:Lemma 4 (Chernoff-Hoeffding Bound). Let $X$ be the sum of $k$ independent Boolean random variables. Assume $\mu_L\le\mathbb E[X]\le\mu_H$, $t>0$. Then

$$
\Pr[X<\mu_L-t]\le\exp(-2t^2/k),\\
$$

$$
\Pr[X>\mu_H+t]\le\exp(-2t^2/k).
$$

We define the random indicator $X_i$ to indicate whether trial $i$ gave correct output. We assume that $\Pr[X_i=1]\ge\frac{1}{2}+\epsilon$ and $k$ is odd. Let $X=\sum_{i=1}^kX_i$, we have $\mathbb E[X]\ge\frac{k}{2}+k\epsilon$. Applying Chernoff-Hoeffding bound, we have

$$
\Pr[X<\frac{k}{2}]=\Pr[X<\mu_L-k\epsilon]\le\exp(-2(k\epsilon)^2/k)\\
=\exp(-2k\epsilon^2)
$$

using $\mu_L=\frac{k}{2}+k\epsilon$. Instead of have error probability $1/4$, we assume error probability for once just less than $1/2-1/p(n)$ and achieve error probability $\exp(-q(n))$ by running $k=\frac{p(n)^2q(n)}{2}$ independent trials.

!!! info

    These analysis applies to both polynomial-time bounded and log-space bounded randomized complexity classes.



# Polynomial Identity Testing

Don't know:

1. Whether randomized classes have **complete problem**;
2.  Any hierarchy results for randomized classes.

## PIT

Polynomial Identity Testing (**PIT**): Testing whether a polynomial is identically zero.

PIT problem is efficiently solvable in randomized polynomial time but not known to be solvable deterministically in polynomial time.

:book:Definition 21 (Arithmetic circuit). An arithmetic circuit $C$ with $n$ inputs and $m$ outputs consists of the following:

1. A directed **acyclic** graph $D=(V,A)$ where the in-degree of every node is either $0$ or $2$.
2. A labeling $\ell$ for the nodes $V$.
3. A choice of $m$ outputs gates $o_1,\cdots,o_m\in V$.

Nodes $V$ corresponds to gates, edges $A$ corresponds to wires. Nodes of in-degree $0$ are input gates. The labeling function satisfies $\forall$ gates $g$:

1. If fanin of $g$ is $0$, $\ell(g)\in\{1,x_1,\cdots,x_n\}$.
2. If fanin of $g$ is $2$, $\ell(g)\in\{+,\times,-\}$.

~~故意不说人话……就是把多项式分解成多层二元运算。一个多项式就相当于只有一个输出口的算术电路。~~

Such an arithmetic circuit compute a polynomial $p\in R[x_1,\cdots,x_n]$ for any Abelian ring $R$. ~~阿贝尔环~~

!!! info

    Usually we consider arithmetic circuits w.r.t. *ring*.



## AFIT

aka Arithmetic formula identity testing

A variant of PIT: input polynomial is given by an arithmetic formula.

Instance: Arithmetic formula computing the polynomial $P(x_1,\cdots,x_n)\in\mathbb Z[x_1,\cdots,x_n]$.

Question: Is $P(x)\equiv0$?

A simple randomized algorithm: simply choose number $a_1,\cdots,a_n\in\{1,2,\cdots,4m\}$  independently and uniformly at random, where $m$ is the size of the given formula, evaluate $P(a_1,\cdots, a_n)$ and answer accordingly.

Using Schwartz-Zippel lemma to analyze error probability.

:thinking:Lemma 5 (Schwartz-Zippel lemma). Let $\mathbb F$ be a field, let $P(x_1,\cdots, x_n)\in\mathbb F[x_1,\cdots,x_n]$ be a non-zero polynomial of total degree at most $d$. Let $S\subseteq\mathbb F$, and pick $a_1,\cdots, a_n\in S$ independently and uniformly at random. Then

$$
\Pr[P(a_1,\cdots,a_n)=0]\le\frac{d}{|S|}.
$$

> Proof by induction on $n$.

So the error probability is decided by $m$, at most $1/4$.

:dart:Theorem 45. $AFIT\in coRP$.

~~没太懂这一部分~~

Sample from $1,\cdots,m$ is equivalent to sample $k$ times from $1,\cdots,2^{\lceil\log_2m\rceil}$. $k=\omega(\log n)$.

## ACIT

aka arithmetic circuit identity testing

Instance: Arithmetic circuit computing the polynomial $P(x_1,\cdots,x_n)\in\mathbb Z[x_1,\cdots,x_n]$.

Question: Is $P(x)\equiv0$?

Circuit of size $m$ has at most $m$ multiplication gates. The degree of the polynomial computed is at most $2^m$. The same approach as in *arithmetic formula* calls for draw random number from $1,\cdots,2^{m+2}$. (exponential!)

To remedy this, we switch to modular arithmetic with randomly chosen prime modulus. Use prime number theorem to analyze error probability.

Let $\pi(x)$ be the number of prime number less than $x$. By prime number theory,

$$
\pi(x)\sim\frac{x}{\ln x}.
$$

:thinking:Lemma 6. For all $m>1$, we have $\pi(2^m)\ge\frac{2^m}{m}$.

:thinking::dart:Theorem 35. $ACIT\in coRP$.

> ~~Det kan jeg ikke forstår~~

# An Alternative Characterization of Time Bounded Classes

We have alternative characterization of $BPP$. Also there are similar alternatives of $ZPP$ and $RP$.

Proposition 4. Let $L$ be a language. Then $L\in BPP$ iff there is a polynomial $q$ and another language $L'\in P$ s.t. $\forall x$:

$$
x\in L\Rightarrow\Pr_{y\in\{0,1\}^{q(|x|)}}[\langle x,y\rangle\in L']\ge\frac{3}{4}\\
$$

$$
x\not\in L\Rightarrow\Pr_{y\in\{0,1\}^{q(|x|)}}[\langle x,y\rangle\not\in L']\ge\frac{3}{4}
$$

We say that the randomized computation uses $q(|x|)$ random bits.

Using success amplification a randomized algorithm using $R$ random bits and having error probability less than $1/4$ can be transformed to a randomized algorithm using $kR$ random bits and having error probability less than $\exp(-k/8)$ by running $k$ trials and taking the majority vote.

# Randomized Computation and the Polynomial-time Hierarchy

By definition, $RP\subseteq NP$.

But we don't know whether $BPP\subseteq NP$ or $NP\subseteq BPP$.

We just guess: $BPP=P$ thus $BPP\subseteq NP$.

But we can prove that $BPP$ is contained in **the second level of polynomial-time hierarchy**.

:dart:Theorem 36. $BPP\subseteq \Sigma_2^P\cap\Pi_2^P$.

> Prove that $BPP\subseteq\Sigma_2^P$. From this also we get $BPP\subseteq\Pi_2^P$.
>
> - Let $L\in BPP$. We have a polynomial $q(n)$ and a language $L'\in P$ s.t. for all $x$ we have
>
>   $$
>   x\in L\Rightarrow\Pr_{y\in\{0,1\}^{q(|x|)}}[\langle x,y\rangle\in L']\ge1-\frac{1}{2q(|x|)},\\
>   $$
>
>   $$
>   x\not\in L\Rightarrow\Pr_{y\in\{0,1\}^{q(|x|)}}[\langle x,y\rangle\in L']<1-\frac{1}{2q(|x|)}.
>   $$
>
> - Let $m=q(|x|)$. For given $x$, we define $S_x=\{y\in\{0,1\}^m|\langle x,y\rangle\in L'\}$. Thus, for all $x$ we have
>
>   $$
>   x\in L\Rightarrow|S_x|\ge(1-\frac{1}{2m})2^m,\\
>   $$
>
>   $$
>   x\not\in L\Rightarrow|S_x|<\frac{1}{2m}2^m.
>   $$
>
>   Claim that $x\in L$ iff
>
>   $$
>   \exists u_1,\cdots, u_m\in\{0,1\}^m\forall r\in\{0,1\}^m:\\
>   $$
>
>   $$
>   \bigvee_{i=1}^m\langle x,u_i\oplus r\rangle\in L',
>   $$
>
>   where $u_i\oplus r$ denote the coordinate-wise exclusive-or. ~~什么是坐标或运算？~~
>
> - This shows that $L\in\Sigma_2^P$ since this can be done in polynomial time.
>
> ~~It remains to prove the predicate is equivalent to~~ $\bigcup_{i=1}^mu_i\oplus S_x=\{0,1\}^m$.

# Randomized Computation and Boolean Circuits

Everything that can be computed in polynomial-time by randomized algorithm can also be computed using Boolean circuits.

:dart:Theorem 37. (Adleman's Theorem). $BPP\subseteq P/poly$.

~~证明也不太懂~~

