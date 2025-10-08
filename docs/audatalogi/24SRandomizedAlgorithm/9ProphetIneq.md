---
title: '随机算法 9 Prophets, Secretaries and Martingales'
author: tinsir888
date: 2024/4/8
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: 5ee7e8e7
---

First part of lecture notes on prophet inequality and secretary problem is by Anupam Gupta (CMU)

Prophet inequalities and Secretary problems are 2 classes of problem where **online decision-making** meets **stochasticity**: in the first set of problems the inputs are random variables, whereas in the second one the inputs are **worst-case** but revealed to the algorithm (aka decision maker) in random order.

Here are some results, proofs and techniques about them.

# The Prophet Inequality

在之前的算法博弈论课上学过，具体可以参考我的对应 [课堂笔记](https://tinsir888.github.io/posts/ec43f770.html)

$n$ random variables $X_1,\cdots,X_n$. We know their distributions up-front, but not their realizations. The realizations are revealed one-by-one.

We want to give a strategy that, upon seeing the value $X_i$ decides either to choose $i$ in which case we get reward $X_i$ and the process stops. Or we can pass, in which case we move on to the next random variables, and are not allowed to come back to $i$ every again. We want to maximize our expected reward. If $X_\max=\max\{X_1,X_2,\cdots,X_n\}$, it is clear that our reward can't exceed $\mathbb E[X_\max]$.

 **Theorem 1** by Krengel, Sucheston and Garling. There exists a strategy with expected reward $\frac{1}{2}\mathbb E[X_\max]$.

> Let $\tau$ be the median of the distribution of $X_\max$, i.e., $\Pr[X_\max\ge\tau]=1/2$.
>
> Now the strategy is simple: pick the first $X_i$ which exceeds $\tau$. Clearly, we will pick an item with probability exactly $1\over2$, then notice that
>
> $$
> \mathbb E[X_\max]\le\tau+\mathbb E[(X_\max-\tau)^+]\\
> \le\tau+\mathbb E[\sum_{i=1}^n(X_i-\tau)^+].
> $$
>
> Then for algorithm:
>
> $$
> ALG\ge\tau\cdot\Pr[X_\max\ge\tau]+\sum_{i=1}^n\mathbb E[(X_i-\tau)^+]\cdot\Pr[\bigwedge_{j\le i}(X_j\lt\tau)]\\
> \ge\tau\cdot\Pr[X_\max\ge\tau]+\sum_{i=1}^n\mathbb E[(X_i-\tau)^+]\cdot\Pr[X_\max\lt\tau]
> $$
>
> By letting $\Pr[X_\max\lt\tau]=\Pr[X_\max\ge\tau]=1/2$, we have
>
> $$
> ALG\ge\frac{1}{2}\mathbb E[X_\max]
> $$

*The proof is beautiful, but hard to generalize. What if we can choose $k$ variables?*

There are **LP-based** version of proof, which can be generalized:

> Define $p_i$ as the probability that element $X_i$ takes on the highest value. Hence $\sum_ip_i=1$. Moreover, suppose $\tau_i$ is s.t. $\Pr[X_i\ge\tau_i]=p_i$, i.e., the $p_i$-th percentile for $X_i$. Define
> $$
> v_i(p_i)=\mathbb E[X_i|X_i\ge\tau_i]
> $$
> as value of $X_i$ conditioned on it lying in the top $p_i$-th percentile. Clearly, $\mathbb E[X_\max]\le\sum_iv_i(p_i)\cdot p_i$.
>
> **A simpler weaker bound**. Here's a simple algorithm that gets value $\frac{1}{4}\sum_iv_i(p_i)\cdot p_i\ge\frac{1}{4}\mathbb E[X_\max]$.
>
> If we haven't chosen an item among $1,\cdots,i-1$, when looking at item $i$, pass with probability half without even looking at $X_i$, else accept it if $X_i\ge\tau_i$.
>
> Definition: we "reach" item $i$ if we have not picked an item before $i$. The expected value of the algorithm is
> $$
> ALG\ge\sum_{i=1}^n\Pr[\text{reach item }i]\cdot\frac{1}{2}\cdot\Pr[X_i\ge\tau_i]\cdot\mathbb E[X_i|X_i\ge\tau_i]\\
> =\sum_{i=1}^n\Pr[\text{reach item }i]\cdot\frac{1}{2}\cdot p_i\cdot v_i(p_i)
> $$
> Since we pick each item with probability $\frac{1}{2}p_i$, the expected number of items we choose is half. So, by Markov's inequality, the probability we pick no item at all is at least half. Hence, the probability we reach item $i$ is at least one half too, the above expression is $\frac{1}{4}\cdot\sum_iv_i(p_i)\cdot p_i$ as claimed.
>
> **The bound of 2**. To improve the algorithm, suppose we denote the probability of reaching item $i$ by $r_i$ and suppose we reject item $i$ outright with probability $1-q_i$. Then inequality shows that
> $$
> ALG\ge\sum_{i=1}^nr_i\cdot q_i\cdot p_i\cdot v_i(p_i)
> $$
> Above, we ensured that $q_i=r_i=\frac{1}{2}$, and hence lost a factor of $\frac{1}{4}$. But if we ensures that $r_i\cdot q_i=1/2$, we would get the desired result of $\frac{1}{2}\mathbb E[X_\max]$. For the first item $r_1=1$ and hence we can set $q_1=\frac{1}{2}$.
>
> For future items, since
> $$
> r_{i+1}=r_i(1-q_i\cdot p_i)
> $$
> we can just set $q_{i+1}=\frac{1}{2r_{i+1}}$. A simple induction shows that $r_{i+1}\ge\frac{1}{2}$. Summing up this equation to get $r_{i+1}=r_1-\sum_{j\le i}p_i/2$, so that $q_{i+1}\in[0,1]$ and is well-defined.

## The Computational Aspect

If the distribution of the random variable stream $X_i$ is explicitly given, the proofs above immediately give us algorithms. But what if $X_i$ are given via a black-box (we can only access via samples)?

The first proof merely relies on finding the median: finding an approximate median $\hat\tau$ s.t. $\Pr[X_\max\lt\hat\tau]\in(\frac{1}{2}-\epsilon,\frac{1}{2}+\epsilon)$ gives us expected reward $\frac{1}{2+\epsilon/2}\mathbb E[X_\max]$. To do this, sample from $X_\max O(\epsilon^{-2}\log\delta^{-1})$ times and take $\hat\tau$ is an approximate median with probability at least $1-\delta$.

For the second proof, there are 2 ways of making it algorithmic. Firstly, if the quantities are polynomially bounded, estimate $p_i$ and $v_i$ by sampling. Alternatively, solve the convex program
$$
\max\{\sum_iy_i\cdot v_i(y_i)|\sum_iy_i=1\}
$$
and use the $y_i$'s from its solution in lieu of $p_i$'s in the algorithm above.

Since getting samples from the distributions may be expensive, there are method to get a constant fraction of $\mathbb E[X_\max]$ via taking just one sample from each of the $X_i$s.

## Extensions: Picking Multiple Items

What if we are allowed to choose $k$ variables from among the $n$?

If $p_i$ is the probability that $X_i$ is among the top $k$ values, we have
$$
\sum_ip_i=k.
$$
The upper bound on our quantity of interest remains essentially unchanged
$$
\mathbb E[\text{sum of top }k\text{ r.v.s}]\le\sum_iv_i(p_i)\cdot p_i
$$
What about an algorithm to get value $1/4$ of the value in this inequality? Then same: reject each item outright with probability $1/2$, else pick $i$ if $X_i\ge\tau_i$. Proof goes through unchanged.

### Better

One can get within $(1-\frac{1}{\sqrt{k+3}})$ of the value. For $k=1$, this matches $1/2$. One can, however, get a fractor of $1-O(\sqrt{\frac{\log k}{k}})$ using a simple concentration bound.

> Suppose we reduce the rejection probability to $\delta$. Then the probability that we reach some item $i$ without having picked $k$ items already is lower-bounded by the probability that we pick at most $k$ elements in the entire process. Since we reject items with probability $\delta$, the expected number of elements we pick is $(1-\delta)k$.
>
> Hence, the probability that we pick less than $k$ items is at least $1-e^{-\Theta(\delta^2k)}$, by a Hoeffding bound for sums of independent random variables. Now setting $\delta=O(\sqrt{\frac{\log k}{k}})$ ensures that the probability of reaching each item is at least $(1-1/k)$, and a argument similar to that in Proof shows that
> $$
> ALG\ge\sum_{i=1}^n\Pr[\text{reach item }i]\cdot\Pr[\text{not reject item }i]\cdot\Pr[X_i\ge\tau_i]\cdot\mathbb E[X_i|X_i\ge\tau_i]\\
> =\sum_{i=1}^n(1-1/k)\cdot(1-O(\sqrt{\frac{\log k}{k}}))\cdot p_i\cdot v_i(p_i)
> $$
>
> which gives the claimed bound of $(1-O(\sqrt{\frac{\log k}{k}}))$.

# Secretary Problems

$n$ items, each has some intrinsic non-negative value.

Assume the values are distinct, but we know nothing about their ranges.

We only know $n$.

The item are presented one by one. Upon seeing an item, we can either pick it or we can pass. The goal is to maximize the probability of picking the item with the highest value.

If an adversary chooses the order in which the items are presented, every deterministic strategy must fail.

:question:Secretary problem: what if the items are presented in uniformly random order? For this setting, it seems surprising that one can prove the following theorem:

**Theorem 2.1**. There is a strategy to pick the best item with probability at least $\frac{1}{e}$.

> A simple algorithm and proof showing probability of $\frac{1}{4}$: ignore the first $n/2$ items, and then pick the next item that is better than all the ones seen so far. Note that this algorithm succeeds if the best item is in the second half of the items (happens with probability $1/2$). Hence $1/4$. Rejecting the first half of the items is not optimal, and there are other cases where the algorithm succeeds that this simple analysis does not account for.
>
> First, the algorithm should never pick an element that is not the best so far. Moreover, if we define
>
> $$
> f(i)=\Pr[i^{th}\text{ item is global best}|i^{th}\text{ is best so far}]\\
> $$
>
> $$
> =\frac{\Pr[i^{th}\text{ item is global best}]}{\Pr[i^{th}\text{ is best so far}]}=\frac{1/n}{1/i}=\frac{i}{n}\\
> $$
>
> $$
> g(i)=\Pr[\text{picking global best using optimal strategy from item i onwards}]
> $$
> then $g(i)$ is a non-increasing and $f(i)$ is increasing.
>
> Any optimal strategy should pass at times $i$ where $f(i)\lt g(i+1)$ and pick at other times if we see an element that is best so far. i.e., a strategy of the type used in the simple proof of $1/4$ is optimal.
>
> So if we reject the first $\tau$ items, then the probability we succeed in picking the global best is
> 
> $$
> \sum_{t=\tau+1}^n\Pr[t^{th}\text{ item is global best}]\cdot\Pr[\text{best of first }t-1\text{ items in first }\tau\text{ positions}]\\
> $$
>
> $$
> =\sum_{t=\tau+1}^n\frac{1}{n}\cdot\frac{\tau}{t-1}=\frac{\tau}{n}(H_{n-1}-H_{\tau-1}).
> $$
>
> This is $g(\tau+1)$. So the first position where $f(\tau)=\tau/n\ge g(\tau+1)$ is given by the smallest $\tau$ s.t. $1\ge H_{n-1}-H_{\tau-1}$, i.e., $\tau\approx n/e$ for large $n$, hence $g(\tau+1)\approx1/e$.

There is an alternate proof that uses a convex-programming. We will write down an LP that captures some properties of any feasible solution, optimize this LP and show a strategy whose success probability is comparable to the objective of this LP.

> Fix an optimal strategy. Assume w.l.o.g（不失一般性） that it does not pick any item that is not the best so far.
>
> Let $p_i$ be the probability that this strategy picks an item at position $i$. Let $q_i$ be the probability that we pick an item at position $i$, conditioned on it being the best so far. So $q_i=\frac{p_i}{1/i}=i\cdot p_i$.
>
> Now the probability of picking the best item is
> $$
> \sum_i\Pr[i^{th}\text{ position is global best and we pick it}]\\
> =\sum_i\Pr[i^{th}\text{ position is global best}]\cdot q_i\\
> =\sum_i\frac{1}{n}q_i=\sum_{i}\frac{i}{n}p_i
> $$
> Constraints: $p_i\in[0,1]$. But also
> $$
> p_i=\Pr[\text{pick item }i|i\text{ best so far}]\cdot\Pr[i\text{ best so far}]\\
> \le\Pr[\text{didn't pick 1,...,i-1}|i\text{ best so far}]\cdot(1/i)
> $$
> But not picking the first $i-1$ items is independent of $i$ being the best so far, so we get
> $$
> p_i\le\frac{1}{i}(1-\sum_{j\lt i}p_j).
> $$
> Hence the success probability of any strategy is upper-bounded by the following LP in variables $p_i$:
> $$
> \max\sum_i\frac{i}{n}\cdot p_i\\
> i\cdot p_i\le1-\sum_{j\lt i}p_j\\
> p_i\in[0,1]
> $$
> Now it can be checked that the solution $p_i=0$ for $i\le\tau$ and $p_i\frac{\tau}{n}(\frac{1}{i-1}-\frac{1}{i})$ for $\tau\le i\le n$ is a feasible solution, where $\tau$ is defined by the smallest value s.t. $H_{n-1}-H_{\tau-1}\le1$.
>
> Finally we can get a stopping strategy whose success probability matches that of the LP. Indeed solve the LP.

For $i^{th}$ position if we've not picked an item already and if this item is the best so far, pick it with probability $\frac{ip_i}{1-\sum_{j\lt i}p_j}$. By the LP constraint, this probability $\in[0,1]$. More over, removing the conditioning shows we pick an item at location $i$ with probability $p_i$, and a calculation similar to the one above shows that the algorithm's success probability is $\sum_iip_i/n$, the same as the LP.

## Extension: Game-Theoretic Issues

In the optimal strategy, we don't pick any items in the first $n/e$ timesteps, and then we pick items with quite varying probabilities.

Suppose we insist that for each position $i$, the probability of picking the item at position $i$ is the same.

Let's fix any such strategy, and write an LP capturing the success probbailities of this strategy with uniformity condition as a constraint. Suppose $p\le1/n$ is this uniform probability. Again, let $q_i$ be the probability of picking an item at position $i$, conditioned on it being the best so far. Note that we may pick items even if they are not the best so far, just to satisfy the uniformity condition; hence instead of $q_i=i\cdot p$, we have $q_i\le ip$.

By same argument constraint in LP, we know that $q_i\le1-(i-1)p$.

And the strategy's success probability is again $\sum q_i/n$. So we can now solve the LP
$$
\max\sum_i\frac{1}{n}\cdot q_i\\
q_i\le1-(i-1)\cdot p\\
q_i\le i\cdot p\\
q_i\in[0,1],p\ge0
$$
Buchbinder et al. shows the optimal value of this LP is at least $1-1/\sqrt 2\approx 0.29$.

## Extension: Multiple Items

Suppose we want $k$ items, maximizing the expected sum of these $k$ values.

Suppose the set of $k$ largest values is $S^*\subseteq[n]$, and their total value is $V^*=\sum_{i\in S}v_i$. It is easy to get an algorithm with expected value $\Omega(V^*)$.

E.g., split the $n$ items into $k$ groups of $n/k$ items, and run the single-item algorithm separately on each of these. Or ignore the first half of the elements, look at the value $\hat v$ of the $(1-\epsilon)k/2^{th}$ highest value item in this set, and pick all items in the second half with values grater than $\hat v$. And indeed, ignoring half the items must lose a constant factor in expectation.

But here's an algorithm that gives value $V^*(1-\delta)$ where $\delta\rightarrow 0$ as $k\rightarrow\infty$. We set $\delta=O(k^{-1/3}\log k)$ and $\epsilon=\delta/2$. Ignore the first $\delta n$ items.

Now look at the value $\hat v$ of the $(1-\epsilon)\delta k^{th}$-highest valued item in this ignored set, and pick the first $k$ elements with values grater than $\hat v$ among the remaining $(1-\delta)n$ elements.



Second part of lecture notes on martingales is by Elias Koutsoupias (Oxford)

# Martingales

~~中文翻译：”鞅“，之前从来没听说过。~~

Chernoff bounds cannot be used for sums of dependent variables.

If the dependency of particular type, the Azuma-Hoeffding inequality provides a general concentration bound.

**Definition of martingale**: A martingale is a sequence of random variables $X_0,X_1,\cdots$ of bounded expectation s.t. for every $i\ge0$,
$$
\mathbb E[X_{i+1}|X_0,\cdots,X_i]=X_i.
$$
More generally, a sequence of random variables $Z_0,Z_1,\cdots$ is a martingale w.r.t. the sequence $X_0,X_1,\cdots$ if for all $n\gt0$ the following conditions hold:

- $Z_n$ is a function of $X_0,\cdots,X_n$
- $\mathbb E[|Z_n|]\le\infty$
- $\mathbb E[Z_{n+1}|X_0,\cdots,X_n]=Z_n$



Here are some example of martingales.

## Gambler's Fortune

A gambler plays a sequence of *fair* games. Let $X_i$ be the amount that the gambler's total winnings immediately after the $i$-th game. Since the games are fair, $\mathbb E[X_i]=0$ and $\mathbb E[Z_{i+1}|X_0,\cdots, X_i]=Z_i+\mathbb E[X_{i+1}]=Z_i$, which shows that very finite sequence $Z_0,Z_1,\cdots,Z_n$ is a martingale. This is a generalization of the simple random walk on a line. The gambler can use the past outcomes and any algorithm to calculate the amount of the next bet.

## Balls and Bins

Suppose that we throw $m$ balls into $n$ bins independently and uniformly at random.

Consider the expected number of empty bins. Let $X_i$ be the random variable representing the bin into which the $i$-th ball falls. Let $Y$ be a random variable representing the number of empty bins. Then the sequence of random variables
$$
Z_i=\mathbb E[Y|X_1,\cdots,X_i]
$$
is a martingale. Clearly $Z_i$ is a function of the $X_1,\cdots,X_i$'s and has bounded expectation. Furthermore
$$
\mathbb E[Z_{i+1}|X_1,\cdots,X_i]=\mathbb E[\mathbb E[Y|X_1,\cdots,X_i,X_{i+1}]|X_1,\cdots,X_i]\\
=\mathbb E[Y|X_1,\cdots,X_i]=Z_i
$$
We can view $Z_i$ as an estimate of $Y$ after having observed the outcomes $X_1,\cdots,X_i$. At the beginning $Z_0$ is a crude estimate, simply the expectation of $Y$. As we add more balls to the bins. $Z_i$'s give improved estimates of $Y$, and at the end we get the exact value $Z_m=Y$.

## Doob Martingales

The balls and bins is a typical **doob martingale**. Doob martingales are processes in which we obtain a sequence of improved estimates of the value of a random variable as information about it is revealed progressively. More precisely, suppose $Y$ is a r.v. that is a function of random variables $X_0,X_1,\cdots$. As we observe the sequence of random variables $X_0,\cdots,X_n$, we improve our estimates of $Y$. The sequence of the mean estimates
$$
Z_t=\mathbb E[Y|X_0,\cdots,X_t]
$$
form a martingale w.r.t. the sequence $X_0,\cdots,X_n$ (provided that $Z_t$'s bounded). Indeed, when we argued that the balls and bins process is a martingale, we used no property of the experiment, therefore the following holds in general
$$
\mathbb E[Z_{t+1}|X_0,\cdots,X_t]\\
=\mathbb E[\mathbb E[Y|X_0,\cdots,X_t,X_{t+1}]|X_0,\cdots,X_t]\\
=\mathbb E[Y|X_0,\cdots,X_t]=Z_t
$$

# Azuma-Hoeffding Inequality

Let $X_0,\cdots,X_n$ be a martingale s.t. $|X_i-X_{i-1}|\le c_i$.

Then for any $\lambda\gt0$,

$$
\mathbb P(X_n-X_0\ge\lambda)\le\exp(-\frac{\lambda^2}{2\sum_{i=1}^nc_i^2}),
$$

and

$$
\mathbb P(X_n-X_0\le-\lambda)\le\exp(-\frac{\lambda^2}{2\sum_{i=1}^nc_i^2}).
$$

Proof of the first inequality, (similar proof for second inequality):

>By applying Markov's inequality to an appropriate random variable and it is similar to the proof of Chernoff's bounds.
>
>We use the variables $Y_i=X_i-X_{i-1}$. The step of the proof:
>
>1. We use Chernoff bounds and instead of bounding $\mathbb P(X_n-X_0\ge\lambda)$, we bound $\mathbb P(\exp(t(X_n-X_0))\ge\exp(t\lambda))$:
>
> $$
>   \mathbb P(\exp(t(X_n-X_0))\ge\exp(t\lambda))\le\exp(-t\lambda)\mathbb E[\exp(t(X_n-X_0))].
> $$
>
>   Then focus on $\mathbb E[\exp(t(X_n-X_0))]$, which can be replaced by $Y_i$'s
>
> $$
>   \mathbb E[\exp(t(X_n-X_0))]=\mathbb E[\prod_{i=1}^n\exp(tY_i)],
> $$
>
>   by telescoping, $X_n-X_0\sum_{i=1}^n(X_i-X_{i-1})=\sum_{i=1}^nY_i$.
>
>2. We can't use $\mathbb E[Y_1Y_2]=\mathbb E[Y_1]\mathbb E[Y_2]$ since $Y_i$s are not independent. Consider the conditional expectation.
> 
> $$
>   \mathbb E[\prod_{i=1}^n\exp(tY_i)|X_0,\cdots,X_{n-1}]\\
> $$
>
> $$
>   =(\prod_{i=1}^{n-1}\exp(tY_i))\mathbb E[\exp(tY_n)|X_0,\cdots,X_{n-1}]
> $$
>
>   because for fixed $X_0,\cdots,X_{n-1}$, all but the last factor in the product are constant and can be moved out.
>
>   Then find an upper bound on $\mathbb E[\exp(tY_n)|X_0,\cdots,X_{n-1}]$.
>
>   - Observe that $\mathbb E[Y_i|X_0,\cdots,X_{i-1}]=0$ by martingale property.
>
>     $$
>     \mathbb E[Y_i|X_0,\cdots,X_{i-1}]\\
> $$
>
> $$
>     =\mathbb E[X_i-X_{i-1}|X_0,\cdots,X_{i-1}]\\
> $$
>
> $$
>     =\mathbb E[X_i|X_0,\cdots,X_{i-1}]-\mathbb E[X_{i-1}|X_0,\cdots,X_{i-1}]\\
> $$
>
> $$
>     =X_{i-1}-X_{i-1}=0
> $$
>
>   - Then by the condition $|Y_i|\le c_i$,
> 
>     $$
>     \exp(tY_i)\le\beta_i+\gamma_iY_i
>     $$
>
>     for $\beta_i=(e^{tc_i}+e^{-tc_i})/2\le\exp((tc_i)^2/2)$, $\gamma_i=(e^{tc_i}+e^{-tc_i})/(2c_i)$. Then rewrite $Y_i$ as $Y_i=rc_i+(1-r)(-c_i)$, where $r=\frac{1+Y_i/c_i}{2}\in[0,1]$, then use the convexity of $e^{tx}$ to get
> 
>     $$
>     e^{tY_i}\le re^{tc_i}+(1-r)e^{-tc_i}\\
>     =\beta_i+\gamma_iY_i.
>     $$
>
>   - Combine the above to get
> 
>     $$
>     \mathbb E[e^{tY_i}|X_0,\cdots,X_{i-1}]\le\mathbb E[\beta_i+\gamma_iY_i|X_0,\cdots,X_{i-1}]\\
>     $$
>
>     $$
>     =\beta_i\le\exp((tc_i)^2/2).
>     $$
>
>   It follows that
>
> $$
>   \mathbb E[\prod_{i=1}^n\exp(tY_i)]=\mathbb E[\prod_{i=1}^{n-1}\exp(tY_i)\times\exp(tY_n)|X_0,\cdots,X_{n-1}]\\
> $$
>
> $$
>   \le\mathbb E[\prod_{i=1}^{n-1}\exp(tY_i)]\exp((tc_n)^2/2).
> $$
>
>3. Then take expectations on both sides to get rid of the conditional expectation:
>
> $$
>   \mathbb E[\prod_{i=1}^n\exp(tY_i)]=\mathbb E[\mathbb E[\prod_{i=1}^{n-1}\exp(tY_i)|X_0,\cdots,X_{n-1}]]\\
> $$
>
> $$
>   \le\mathbb E[\prod_{i=1}^{n-1}\exp(tY_i)]\exp((tc_n)^2/2).
> $$
>
>4. Then use same standard techniques
>
>   - By induction: $\mathbb E[\prod_{i=1}^n\exp(tY_i)]\le\prod_{i=1}^n\exp((tc_i^2)/2)=\exp(t^2\sum_{i=1}^nc_i^2/2)$
>   - Therefore $\mathbb P(\exp(t(X_n-X_0))\ge\exp(\lambda t))\le\exp(-\lambda t)\exp(t^2\sum_{i=1}^nc_i^2/2)$
>   - Set $t=\lambda/\sum_{i=1}^nc_i^2$ to minimize the above expression and get the bound of the theorem.

## Apply AH Bounds on Gambler's Fortune

A gambler plays a sequence of fair games. We have seen that if $Z_i$ denotes the gambler's total winnings immediately after the $i$-th game, the sequence $Z_0,\cdots,Z_n$ is a martingale. Suppose that the gambler has a very sophisticated algorithm to decide the amount that he bets every day that takes into account past bets and outcomes. Since the games are fair, the expected gain $Z_n$ is zero.

Winning is not concentrated around the mean value in general.

For example, the case that gambler puts higher and higher bets. Suppose now that there is a bound on the size of bets, By AH inequality, the final winnings are concentrated with high probability in $[-k,k]$ where $k=O(\sqrt{n\log n})$.