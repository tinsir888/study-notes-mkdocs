---
title: 随机算法 5 Streaming Algorithms for Frequency Estimation
author: tinsir888
date: 2024/2/26
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: 9718f2f0
---

Lecture notes adopted from UCB CS270

Streaming Algorithms: Frequent Items

Streaming setting: Data stream $x_1,x_2,\cdots,x_n$ with $x_i\in[m]$. The available memory is $O(\log^cn)$. **Sub-linear space**.

Algorithm: Approximates frequencies for the top $k$ items.

# Deterministic Algorithm

To estimate item frequencies $f_j$ within an additive error of $n/k$ using with $O(k\log n)$ memory. （$\log n$ 这里指的是内存的比特数量。）

- Maintain set $S$ of $k$ counters, initialize to $0$. For each element $x_i$ in stream
  - If $x_i\in S$, increment the counter of $x_i$.
  - If $x_i\not\in S$, add $x_i$ to $S$ is space is available, else decrement all counters in $S$.

An item in $S$ whose count falls to $0$ can be removed, the space requirement for storing $k$ counters is $k\log n$ and the update time per item is $O(k)$. The algorithm estimates the count of an item as the value of its counter or zero if it has no counter.

:thinking:Claim 1: The frequency estimate $n_j$ produced by the algorithm satisfies $f_j-n/k\le n_j\le f_j$.

> Clearly, $n_j$ is less than the true frequency $f_j$. Differences between $f_j$ and the value of the estimate are caused by one of the two scenarios:
>
> 1. The item $j\not\in S$, each counter in $S$ gets decremented, this is the case when $x_j$ occurs in the stream but the counter for $j$ is not incremented.
> 2. The counter for $j$ gets decremented due to an element $j'$ that is not contained in $S$.
>
> Both scenarios result in $k$ counters getting decremented hence they can occur at most $n/k$ times, showing that $n_j\ge f_j-n/k$.
>
> 在所有计数器减少前，至少要添加 $k$ 次。对于所有计数器的和，每次减少操作都会减少 $k$。所有计数器的和最多减少 $n/k$ 次。所以单个计数器也最多减少 $n/k$ 次。
>
> 也就是当所有 item 出现的频率都一样（都是 $n/k$）的时候，取等号。

# Count Min Sketch

The **turnstile model** (旋转门模型) allows both additions and deletions of items in the stream.

The stream consists of pairs $(i,c_i)$, where the $i\in[m]$ is an item and $c_i$ is the number of items to be added or deleted. The count of an item can not be negative at any stage, the frequency $f_j$ of item $j$ is $f_j=\sum c_j$.

The following algorithm estimates frequencies of all items up to an additive error of $\epsilon|f|_1$ with probability $1-\delta$, the $\ell_1$ norm $|f|_1$ is the number of items present in the data set. The two parameters $k$ and $t$ in the algorithm are chosen to be ${2\over\epsilon},\log(1/\delta)$.

**Space usage: $O({1\over\epsilon}\log n)$**

The algorithm works as follows:

1. Maintain $t$ arrays $A[i]$ each having $k$ counters, hash function $h_i:U\rightarrow[k]$ drawn from a $2$-wise independent family $\mathcal H$ is associated to array $A[i]$.

2. For element $(j,c_j)$ in the stream, update counters as follows:
   $$
   A[i,h_i(j)]\gets A[i,h_i(j)]+c_j\forall i\in[t]
   $$

3. The frequency estimate for item $j$ is $\min_{i\in[t]}A[i,h(j)]$.

The output estimate is always more than the true value of $f_j$ as the count of all the items in the stream is non-negative.

## Analysis

To bound the error in the estimate for $f_j$ we need to analyze the excess $X$ where $A[1,h_1(j)]=f_j+X$. The excess $X$ an be a sum of random variables $X=\sum_iY_i$ where $Y_i=f_i$ if $h_1(j)=h_1(i)$ and $0$ otherwise. As $h_1\in\mathcal H$ is chosen uniformly at random from a pair-wise independent hash function family, $\mathbb E[Y_i]=f_i/k$.
$$
\mathbb E[X]={|f|_1\over k}={\epsilon|f|_1\over2}.
$$
> $$
> \mathbb E[X]=\mathbb E_h[\sum_{j\neq i,h(i)=h(j)}f_j]=\mathbb E_h[\sum_{j|j\neq i}f_j\cdot\mathbf 1_{h(i)=h(j)}]\\
> =\sum_{j|j\neq i}\mathbb E[\mathbf 1_{h(i)=h(j)}]\cdot f_j=\sum_{j|j\neq i}\Pr[h(i)=h(j)]\cdot f_j=\sum_{j|j\neq i}{f_j\over k}\le{\sum_jf_j\over k}={|f|_1\over k}
> $$

Applying Markov's inequality, we have
$$
\Pr[X\gt\epsilon|f|_1]\le{1\over2}.
$$
The probability that all the excesses at $A[i,h_i(x_i)]$ are greater than $\epsilon|f|_1$ is at most ${1\over2^t}\le\delta$ as $t$ was chosen to be $\log(1/\delta)$. The algorithm estimates the frequency of item $x_j$ up to an additive error $\epsilon|f|_1$ with probability $1-\delta$.

The memory required for the algorithm is the sum of the space for the array and the hash functions, $O(kt\log n+t\log m)=O({1\over\epsilon}\log(1/\delta)\log n)$. The update time per item in the stream is $O(\log{1\over\delta})$.

# Count Sketch

Another sketch algorithm with error in terms of the $\ell_2$ norm $|f|_2=\sqrt{\sum_jf_j^2}$.

The relation between the $\ell_1$ and $\ell_2$ norm is ${1\over\sqrt n}|f|_1\le|f|_2\le|f|_1$, the $\ell_2$ norm is less than the $\ell_1$ norm so the guarantee for this algorithm is better than that for the previous one.

The algorithm works as follows:

1. Maintain $t$ arrays $A[i]$ each having $k$ counters, hash function: $g_i:U\rightarrow\{-1,1\}$ and $h_i:U\rightarrow[k]$ drawn uniformly at random from a pair-wise independent families are associated to array $A[i]$.

2. For element $(j,c_j)$ in the stream, update counters as follows
   $$
   A[i,h_i(j)]\gets A[i,h_i(j)]+g_i(j)c_j\forall i\in[t]
   $$

3. The frequency estimate for item $j$ is the **median** over the $t$ arrays of $g_i(x_j)A[i,h(j)]$.

## Analysis

The entry $A[1,h_1(j)]=g_1(j)f_j+X$, we examine the contribution $X$ from the other items by writing $X=\sum_iY_i$ where $Y$ is $\pm f_i$ if $h_1(i)=h_1(j)$ and $0$ otherwise. Note that $\mathbb E[Y_j]=0$, so the expected value of $g_1(j)A[1,h(j)]$ is $f_j$.

> $$
> \hat{f_i}=g(i)\cdot A[h(i)]=g(i)\cdot(g(i)\cdot f_i+\sum_{j\neq i,h(j)=h(i)}g(j)\cdot f_j)\\
> =f_i+\sum_{j\neq i,h(j)=h(i)}g(i)g(j)f_j\\
> =f_i+X\\
> \mathbb E[X]=\mathbb E[\sum_{j\neq i,h(j)=h(i)}g(i)g(j)f_j]=\mathbb E[\sum_{j\neq i}\mathbf1_{h(i)=h(j)}g(i)g(j)f_j]\\
> =\sum_{j\neq i}\mathbb E[\mathbf1_{h(i)=h(j)}g(i)g(j)]f_j=\sum_{j\neq i}\mathbb E[\mathbf 1_{h(i)=h(j)}]\mathbb E[g(i)]\mathbb E[g(j)]f_j=0\\
> $$

The random variable $Y_i$ are pairwise independent as $h_1$ is a pair-wise independent hash function, so the variance of $X$:
$$
Var(X)=\sum_{i\in[m]}Var(Y_i)=\sum_{i\in[m]}{f^2_i\over k}={|f|_2^2\over k}.
$$
By Chebyshev's inequality,
$$
\Pr[|X-\mu|\gt\Delta]\le{Var(X)\over\Delta^2}.
$$
The mean $\mu=0$ and the variance is $|f|_2^2\over k$, choosing $\delta=\epsilon|f|_2$ and $k=4/\epsilon^2$, we have
$$
\Pr[|X-\mu|\gt\epsilon|f|_2]\le{1\over\epsilon^2k}\le{1\over4}.
$$
For $t=\theta(\log(1/\delta))$, the probability that the median value deviates from $\mu$ by more than $\epsilon|f|_2$ is less than $\delta$ by a **Chernoff Bound**. That is, the probability that there are fewer than $t/2$ success in a series of $t$ tosses of a coin with success probability $3\over4$ is smaller than $\delta$ for $t=O(\log({1\over\delta}))$.

Arguing as in the count min sketch the space required is $O({1\over\epsilon^2}\log{1\over\delta}\log n)$ and the update time per item is $O(\log{1\over\delta})$.

# Remarks

- The count sketch approximates $f_j$ within $\epsilon|f|_2$ but requires $\widetilde O({1\over\epsilon^2})$ space,

- while the count min sketch approximates $f_j$ within $\epsilon|f|_1$ and requires $\widetilde O({1\over\epsilon})$.

- The approximation provided by the sketch algorithm is meaningful only for items that occur with high frequency.