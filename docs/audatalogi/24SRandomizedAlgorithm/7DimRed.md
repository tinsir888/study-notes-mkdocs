---
title: 随机算法 7 Dimensionality Reduction
author: tinsir888
date: 2024/3/11
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: 1fe3a20c
---

Lecturer: Iannis

Goal of dimensionality reduction: represent a high dimensional data set in a lower dimensional space while preserving much of the important structure.

PCA (principle component analysis): to maintain as much variance in the data as possible while reducing the dimensionality.

The mapping of data from a high dimensional space to a low dimensional space is called an **embedding**.

# Johnson-Lindenstrauss Dimensionality Reduction

Lecture notes by Allan Grønlund

Main results of JL: any $m$ point subset of Euclidean space can be linearly embedded in $k=O(\log m/\epsilon^2)$ dimensions without distorting the distance between any pair of points by more than a factor of $(1\pm\epsilon)$, for any $0\lt\epsilon\lt\frac{1}{2}$.

For PCA to be relevant the original data points $x_1,\cdots,x_m$ must be inherently low dimensional.

While JL theorem requires no assumption on the original data and the target dimension is even independent of the input dimension.

Further results: JL lemma is tight even for non-linear embeddings.

## Simple JL Lemma

**Theorem 1**. For any $0\lt\epsilon\lt\frac{1}{2}$ and any integer $m$, then for integer

$$
k=O(\frac{1}{\epsilon^2}\log m)
$$

large enough and any $m$ points $x_1,\cdots,x_m\subset\mathbb R^d$ there exists a linear map matrix $L:\mathbb R^d\rightarrow\mathbb R^k$ s.t. for any $1\le i,j\le m$:

$$
(1-\epsilon)||x_i-x_j||_2^2\le||Lx_i-Lx_j||_2^2\le(1+\epsilon)||x_i-x_j||^2_2
$$

The linear transformation $L$ in Theorem 1 is simply multiplication by a matrix whose entries are sampled independently from a standard Gaussian.

Define a random variable $A$ as $k\times d$ matrix, where each entry $A_{i,j}\sim\mathcal N(0,1)$. The final embedding matrix is sample of the random variable $L=\frac{1}{\sqrt k}A$.



First we prove a simply simpler result for such random variables.

**Lemma 1**. Fix any vector **unit vector** $x$.

> $x$ is unit vector if $\sum_ix_i^2=1$.

 For any $0\lt\epsilon,\delta\le\frac{1}{2}$. For $k=O(\frac{1}{\epsilon^2}\log\frac{1}{\delta})$ large enough let $L=\frac{1}{\sqrt k}A$ be a random variable, where $A$ is a $k\times d$ random matrix whose entries are independent $\sim\mathcal N(0,1)$. Then:

$$
\Pr_L(|||Lx||^2-1|\gt\epsilon)\le\delta
$$

If we pick a random matrix as described, then with probability at least $1-\delta$ the norm of $x$ is distorted by a factor at most $(1\pm\epsilon)$ by $L$.

Note that this generalizes to any non-unit vector since a vector $v$ can be written as $v=||v||\frac{v}{||v||}$. Since the embedding is linear, $Lv=||v||L\frac{v}{||v||}$, thus with probability at least $1-\delta$,

$$
||Lv||^2=\left|\left|||v||L\frac{v}{||v||}\right|\right|^2=||v||^2\left|\left|L\frac{v}{||v||}\right|\right|^2\in||v||^2(1\pm\epsilon)
$$

 and the same bound applies for any vector $v$.

### Proof of Theorem 1

Fix a point set $x_1,\cdots,x_m$, Set $\delta=\frac{1}{m^3}$ and $k=O(\epsilon^{-2}\log\frac{1}{\delta})$ large enough and consider the random variable matrix $L=\frac{1}{\sqrt k}A$ where $A_{i,j}\sim\mathcal N(0,1)$.

Observation: From linearity of $L$, it follows that $||Lx_i-Lx_j||=||L(x_i-x_j)||$. This means that the distance between $x_i$ and $x_j$ is distorted by more than $(1\pm\epsilon)$ iff the norm of the vector $(x_i-x_j)$ is distorted by more than $(1\pm\epsilon)$.

Assuming Lemma 1, for any pair of indices $i,j$, the probability that the matrix $L$ distorts the norm of the vector $x_i-x_j$ by more than $(1\pm\epsilon)$ is at most $\delta$.

Since we need all pairwise distances to be approximately maintained, we need to bound the probability that there will be at least one index pair $i,j$ s.t. $x_i-x_j$ is distorted by at least $(1\pm\epsilon)$. We call an index pair $i,j$ bad if $L$ does not maintain the norm of $x_i-x_j$.

Applying the Union Bound, the probability over $L$ that there is a bad index pair is at most the sum over all $i,j$ the probability that $L$ distorts the norm of $x_i-x_j$ by more than $(1\pm\epsilon)$. We get:

$$
\Pr_L(\exists(i,j)\text{ that is bad})\le\sum_{i=1}^{m-1}\sum_{j=i+1}^m\Pr((i,j)\text{ bad})=\left(\begin{array}{cc}m\\2\end{array}\right)\frac{1}{m^3}\le\frac{1}{m}
$$

Thus with probability at least $1-\frac{1}{m}\gt0$ the random matrix $L$ maintains all the pairwise norms in the dataset within a factor $(1\pm\epsilon)$. In particular, this means that there exists a matrix that can embed the points and maintain all pairwise distances and to find one just sample a matrix and it works with probability at least $1-\frac{1}{m}$.

It remains to prove Lemma 1.

**Fact 1**. Fir any constants $a,b$, if $X\sim\mathcal N(0,1),Y\sim\mathcal N(0,1)$, then $aX+bY\sim(0,a^2\sigma_x^2+b^2\sigma_y^2)$.

Chi-square distribution: the sum of square of $k$ independent random variables which $\sim\mathcal N(0,1)$

**Lemma 2**. Let $Y\sim\chi_k^2$. Then

$$
\Pr_Y\left(\left|\frac{Y}{k}-1\right|\ge x\right)\le\exp\left(-\frac{3}{16}kx^2\right),x\in[0,\frac{1}{2})
$$

First we prove that the random variable defined as the norm of $||Ax||^2$ is $\chi_k^2$ distributed. The we apply Lemma 2 and Lemma 1.

**Lemma 3**. Given any unit vector $x\in\mathbb R^d$. Let $A$ be the random variable matrix with each $A_{i,j}\sim\mathcal N(0,1)$ independently of the other entries. Then the random variable that is the squared norm of $Ax$ is $\chi_{k,d}$ distributed:

$$
||Ax||^2\sim\chi_k^2
$$

> Let $a_i$ denote the $i$-th row of $A$:
>
> $$
> ||Ax||^2=\sum_{i=1}^k\langle a_i,x\rangle^2=\sum_{i=1}^k\left(\sum_{j=1}^da_{i,j}x_j\right)^2.
> $$
>
> By Fact 1, $a_{i,j}\sim\mathcal N(0,1)$, we know $a_{i,j}x_j\sim\mathcal N(0,x_j^2)$ and we are computing a sum of these. This means that $\sum_{j=1}^da_{i,j}x_j\sim\mathcal N(0,\sum_{j=1}^dx_j^2)=\mathcal N(0,1)$, since $||x||^2=1$. In total, we get that $||Ax||^2$ is distributed as a sum of $k$ standard Gaussians i.e.
>
> $$
> ||Ax||^2\sim\sum_{i=1}^k\mathcal N(0,1)^2\sim\chi_k^2
> $$

### Proof of Lemma 1

Fix a unit vector $x$ and consider the random variable matrix $L=\frac{1}{\sqrt k}A$ defined as usual.

First note that the expected value $\mathbb E[||Ax||^2]=k$, which means that $\mathbb E[||Lx||^2]=1$, i.e., in expectation the norm of $x$ is preserved by $L$. This is a very relevant property indeed. But not enough.

We need to argue that

$$
\Pr_L(|||Lx||^2-1|\gt\epsilon)\le\delta
$$

Luckily this is exactly captured in Lemma 2. Since $||Ax||^2\sim\chi_k^2$, then

$$
\Pr_A\left(\left|\frac{||Ax||^2}{k}-1\right|\ge\epsilon\right)\le\exp\left(-\frac{3}{16}k\epsilon^2\right),
$$

since $||Ax||^2/k=||\frac{1}{\sqrt k}Ax||^2=||Lx||^2$, this means that

$$
\Pr_L(|||Lx||^2-1|\gt\epsilon)\le\exp\left(-\frac{3}{16}k\epsilon^2\right).
$$

To finalize the proof, we need to find a $k$ s.t. $\exp(-\frac{3}{16}k\epsilon^2)$ which is easily achieved by setting $k\ge\frac{16}{3}\epsilon^{-2}\log\frac{1}{\delta}$.

## Remarks

JL Lemma generalizes to the case where the entries in $A$ are instead uniform random signs $\pm1$. Also, it can be shown that the matrix can be made sparse and the amount of randomness required reduced, all steps to make the embedding more efficient.

We can reduce the dimensionality of any dataset by using a simple randomized linear transformation.

But what if we allow non-linear embedding? Amazed: We can't!

JL embedding lemma is tight even for non-linear embeddings by showing that there exists data set s.t. there is no way to embed into a space with lower dimension than what is promised in the JL Lemma without violating some of the pairwise distances.

# Application on K-Means

lecture notes by Kasper

## K-Means

To compute $k$ centers minimizing the sum of distances each point to its nearest cluster center.

Computing the optimal k-means is NP-hard. So we use heuristic algorithm such as Lloyd's algorithm.

The Lloyd's algorithm starts by partitioning the input points into $k$ clusters $X_1,\cdots,X_k$ in some way. Repeat: compute the optimal cluster centers, then reassign each point to the cluster center and repeat until no clusters change.

This algorithm may get stuck in a local minimum and depend on initialization.

Let $t$ denote the number of iterations, the total running time is $O(tndk)$

- $t$ iterations
- compute new clusters in $O(nd)$ time
- reassign points in $O(ndk)$ time.

## Applying Johnson-Lindenstrauss

To speed up Lloyd's algorithm

Before running Lloyd's algorithm, perform a JL transform s.t. all distances between points are preserved to within $(1\pm\epsilon)$.

Let $A\in\mathbb R^{m\times d}$ be the embedding matrix s.t. each original point $x_i$ is mapped to $Ax_i$. The new dimensionality is $m=O(\epsilon^{-2}\log n)$. Performing the transform takes $O(nd\epsilon^{-2}\log n)$ time, which may be much less than $O(tdnk)$. Therefore, running Lloyd's in the lower dimensional space takes $tnk\epsilon^{-2}\log n$ for a total running time of $O(n\epsilon^{-2}(d+kt)\log n)$.

**Lemma 4**. For any set of points $x_1,\cdots,x_n$ and a partitioning of the points into clusters $X_1,\cdots,X_k$, the cost of the clustering satisfies

$$
\sum_{j=1}^k\sum_{x_i\in X_j}\left|\left|x_i-\frac{1}{|X_j|}\sum_{x_h\in X_j}x_h\right|\right|_2^2=\frac{1}{2}\sum_{j=1}^k\frac{1}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}||x_i-x_h||_2^2
$$

The interesting thing about Lemma 4 is that it shows that the cost of a clustering can be written solely in terms of pairwise distances. This means that the cost of all clusterings will be roughly preserved if all distances are roughly preserved.

Consider any clustering $X_1,\cdots,X_k$ on the dimensionality reduced data set $Ax_1,\cdots,Ax_n$ and assume its cost is $\mu$. By lemma 4, we get that the cost of the clustering equals

$$
\sum_{j=1}^k\sum_{x_i\in X_j}\left|\left|Ax_i-\frac{1}{|X_j|}\sum_{x_h\in X_j}Ax_h\right|\right|_2^2=\frac{1}{2}\sum_{j=1}^k\frac{1}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}||Ax_i-Ax_h||_2^2
$$

JL guarantees that each term $||Ax_i-Ax_h||_2^2$ is within $(1\pm\epsilon)||x_i-x_h||_2^2$, thus the cost of same clustering on the original data points lies in the interval:

$$
\frac{1}{2}\sum_{j=1}^k\frac{1}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}(1\pm\epsilon)||x_i-x_h||_2^2=(1\pm\epsilon)\mu.
$$

Since this holds for all clusterings, we can in particular conclude that if one finds a good clustering on the low dimensional data set (say one with a cost of at most $(1+\gamma)\mu^*$ where $\mu^*$ is the optimal clustering in low dimensional space), then that clustering has a cost of at most $(1+\epsilon)(1+\gamma)\mu^*$ on the original data set.

Moreover, if we let $\psi^*$ be the cost of the optimal clustering $X^*$ on the original data set, then we know that the optimal clustering must have a cost of $\frac{1}{1\pm\epsilon}\psi^*\ge\mu^*$ in low dimensional space. It follows that the clustering $X$ has a cost of no more than $(1+\epsilon)(1+\gamma)\frac{1}{1\pm\epsilon}\psi^*$. For $\epsilon\lt\frac{1}{2}$, we have $1/(1-\epsilon)=1+\epsilon(1-\epsilon)\le1+2\epsilon$ and $(1+\epsilon)(1+2\epsilon)=(1+3\epsilon+2\epsilon^2)\le1+4\epsilon$ and thus the cost is no more than $(1+4\epsilon)(1+\gamma)\psi^*$. That is, if we can find an approximately optimal clustering for the dimensionality reduced points, we have found an approximately optimal clustering for the original points.

### Proof of Lemma 4

$$
\sum_{j=1}^k\sum_{x_i\in X_j}\left|\left|x_i-\frac{1}{|X_j|}\sum_{x_h\in X_j}x_h\right|\right|_2^2\\
$$

$$
=\sum_{j=1}^k\sum_{x_i\in X_j}\left(||x_i||^2-\frac{2}{|X_j|}\sum_{x_h\in X_j}\langle x_i,x_h\rangle+\frac{1}{|X_j|^2}||\sum_{x_h\in X_j}x_h||_2^2\right)\\
$$

$$
=\sum_{j=1}^k\left(\sum_{x_i\in X_j}||x_i||_2^2-\frac{2}{|X_j|}\langle\sum_{x_i\in X_j}x_i,\sum_{x_h\in X_j}x_h\rangle+\frac{1}{|X_j|}||\sum_{x_h\in X_j}x_h||_2^2\right)\\
$$

$$
=\sum_{j=1}^k\left(\sum_{x_i\in X_j}||x_i||_2^2-\frac{2}{|X_j|}||\sum_{x_i\in X_j}||_2^2+\frac{1}{|X_j|}||\sum_{x_h\in X_j}x_h||_2^2\right)\\
$$

$$
=\sum_{j=1}^k\left(\sum_{x_i\in X_j}||x_i||_2^2-\frac{1}{|X_j|}||\sum_{x_i\in X_j}x_i||_2^2\right)\\
$$

$$
=\frac{1}{2}\sum_{j=1}^k\left(2\sum_{x_i\in X_j}||x_i||_2^2-\frac{2}{|X_j|}||\sum_{x_i\in X_j}x_i||_2^2\right)\\
$$

$$
=\frac{1}{2}\sum_{j=1}^k\left(\frac{2}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}||x_i||_2^2-\frac{2}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}\langle x_i,x_h\rangle\right)\\
$$

$$
=\frac{1}{2}\sum_{j=1}^k\left(\frac{1}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}(||x_i||_2^2+||x_h||_2^2)-\frac{2}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_h}\langle X_i,x_j\rangle\right)\\
$$

$$
=\frac{1}{2}\sum_{j=1}^k\frac{1}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}(||x_i||_2^2+||x_h||_2^2-2\langle x_i,x_h\rangle)\\
$$

$$
=\frac{1}{2}\sum_{j=1}^k\frac{1}{|X_j|}\sum_{x_i\in X_j}\sum_{x_h\in X_j}||x_i-x_h||_2^2
$$