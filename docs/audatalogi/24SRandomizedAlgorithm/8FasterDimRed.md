---
title: 随机算法 8 Faster Dimensionality Reduction
author: tinsir888
date: 2024/3/18
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: 67b87074
---

Lecture notes by Kasper.

This lecture: different approaches used to speed up the JL transform.

Recall: classic JL transform works by drawing a random matrix $A$ with coordinates i.i.d. either $\mathcal N(0,1)$ or uniform random among $\{-1,+1\}$ and then embedding a vector $x$ to $m^{-1/2}Ax$. $m=O(\epsilon^{-2}\log n)$ if one wants to preserve all pairwise distances to within $(1\pm\epsilon)$ and embedding time is $O(dm)$. The target dimensionality is known to be optimal via a result by Larsen and Nelson.

# Sparse Embeddings

Using an embedding matrix $A$ that is sparse.

## Sparsity

A sparse matrix is one with few non-zeroes.

If each column of $A$ has only $t$ non-zeroes, then the embedding time improves to $O(dt)$.

Example of sparse: bag of words (words are embedded to vector.)

With vector representation, documents with similar words map to points that are close. Here vectors are very sparse.

The **sparsity** of vector $x$: $||x||_0=$ number of non-zeroes in $x$.

The embedding time of classic JL is $O(m||x||_0)$ and with a matrix having $t$-sparse columns, it's $O(t||x||_0)$.



Kane and Nelson showed that the following construction with high probability preserves all pairwise distances for a set of $n$ points:-

- For each column of the matrix $A\in\mathbb R^{m\times d}$, pick a uniform random set of $t=O(\epsilon^{-1}\log n)$ rows and assign the corresponding entries either $-1$ or $+1$ uniformly at random and independently.
- Then embed $x$ as $t^{-1/2}Ax$. Hence the embedding time compared to standard JL went from $O(||x||_0\epsilon^{-1}\log n)$, $\epsilon^{-1}$ improvement.
- The target dimensionality $m$ remains optimal $O(\epsilon^{-2}\log n)$.

The expected length of the embedded vector is the same as the original vector:

$$
\mathbb E[||t^{-1/2}Ax||_2^2]=t^{-1}\mathbb E[||Ax||_2^2]\\
$$

$$
=t^{-1}\sum_{i=1}^m\mathbb E[(\sum_{j=1}^da_{i,j}x_j)^2]\\
$$

$$
=t^{-1}\sum_{i=1}^m(\sum_{j=1}^d\mathbb E[a_{i,j}^2x_j^2]+\sum_{j=1}^d\sum_{h\neq j}\mathbb E[a_{i,j}a_{i,h}x_jx_h])\\
$$

$$
=t^{-1}\sum_{i=1}^m\sum_{j=1}^d\mathbb E[a_{i,j}^2]x_j^2\\
$$

$$
=t^{-1}\sum_{i=1}^m\sum_{j=1}^d(t/m)x_j^2\\
$$

$$
=\sum_{j=1}^dx_j^2\\
$$

$$
=||x||_2^2
$$
In the above, we use $\mathbb E[a_{i,j}]=\mathbb E[a_{i,j}]\mathbb E[a_{i,h}]=0$ by independence and the fact that $a_{i,j}$ is symmetric around 0.

Moreover, we use $\mathbb E[a_{i,j}^2]=t/m$ since $a_{i,j}$ takes either the value $-1$ or $+1$ with probability $t/m$ and in both cases, $a_{i,j}^2$ takes value $1$.

Nelson and Nguyen proved a lower bound: any sparse embedding matrix $A$ must have $t=\Omega(\epsilon^{-1}\log n/\log(1/\epsilon))$, i.e., the upper bound can be improved by at most a $\log(1/\epsilon)$ factor.

Setting largest possible sparsity remains a fundamental open problem in dimensionality reduction.

## Feature Hashing

Another line of work tries to improve the embedding time further by making assumptions on the input data.

Take bag of words as example. If one removes very frequently occurring words, then it seems reasonable to assume that the vector representing a document has no large coordinates. More formally, the ratio between $||x||_\infty$ and $||x||_2$ is small.

Under such assumptions, it's possible to speed up the sparse transforms from above.

**Feature hashing** by Weinberger et al. takes this to the extreme by letting $A$ have exactly one non-zero per column, chosen at a uniform random row and with a value that is uniform among $-1$ and $+1$. So feature hashing is really the construction of Kane and Nelson with $t=1$. Therefore it follows from the above calculations that the expected length of embedded vectors are correct. Feature hashing clearly has the fastest possible embedding time of just $O(||x||_0)$.

Freksen, Kamma and Larsen showed that Feature Hashing into the optimal $m=O(\epsilon^{-2}\log n)$ dimensions has the JL guarantees (preserve all distances to within $(1\pm\epsilon)$) exactly when

$$
\frac{||x||_\infty}{||x||_2}=O(\epsilon^{-1/2}\cdot\min\{\frac{\log(1/\epsilon)}{\log n},\sqrt{\frac{1}{\log n}}\}).
$$

And more generally, if one embeds into $m\ge\epsilon^{-2}\log n$ dimensions, then distances are preserved when

$$
\frac{||x||_\infty}{||x||_2}=O(\epsilon^{-1/2}\cdot\min\{\frac{\log(\epsilon m/\log n)}{\log n},\sqrt{\frac{\log(\epsilon^2 m/\log n)}{\log n}}\}).
$$

# Fast JL Transform

Instead of using sparse matrices for embedding, another approach is to use matrices for which there are efficient algorithms to compute the product $Ax$.

The Fast JL transform by Ailon and Chazelle.

First observation is similar to what is used in Feature Hashing: If data vectors have only small coordinates ($\frac{||x||_\infty}{||x||_2}$ is small), then one can use very sparse matrices for the embedding. The main idea is to multiply $x$ with a matrix which ensures that coordinates becomes small, and then use a sparse matrix afterwards.

## Walsch-Hadamard Matrix

Assume without loss of generality that $d$ is a power of $2$. Let $H_d$ be the $d\times d$ Walsch-Hadamard matrix.

> $H_d$ can be defined recursively as follows:
>
> - The $2\times2$ Walsch-Hadamard matrix $H_2$ has $\bar h_{1,1}=\bar h_{1,2}=\bar h_{2,1}=1$ and $\bar h_{2,2}=-1$. Clearly all rows are orthogonal.
> - The $2d\times2d$ Walsch-Hadamard matrix $H_{2d}$ consists of four $d\times d$ Walsch-Hadamard matrices $H_d$, one in each of the for quadrants.
>
> $$
> \bar H_2=\left(\begin{array}{cc}1&1\\1&-1\end{array}\right)\\
> $$
>
> $$
> \bar H_{2d}=\left(\begin{array}{cc}\bar H_d& \bar H_d\\ \bar H_d& -\bar H_d\end{array}\right)\\
> $$
>
> $$
> \bar H_4=\left(
> \begin{array}{cc}
> 1&1&1&1\\
> 1&-1&1&-1\\
> 1&1&-1&-1\\
> 1&-1&-1&1
> \end{array}
> \right)
> $$
>
> Again, all rows of $\bar H_{2d}$ are orthogonal:
>
> - For two distinct rows in the top half of the matrix, the orthogonality follows by induction.
> - Similarly for two in the bottom half. For one row in the top half of the matrix and one from the bottom half, orthogonality foolows since the top row vector has the form $(v\circ v)$ and bottom has the form $w\circ-w$ where $\circ$ denotes concatenation.
> - Hence the inner product is $\langle v,w\rangle-\langle v,w\rangle=0$.

The crucial part about WH matrices is that one can compute the matrix-vector product $\bar H_{d}x$ efficiently.

Write $x$ as $(\begin{array}{cc}x_1\\ x_2\end{array})$ where $x_1$ is the first $d/2$ coordinates of $x$ and $x_2$ is the last $d/2$ coordinates.

Then $\bar H_dx=(\begin{array}{cc}\bar H_{d/2}x+\bar H_{d/2}x_2\\\bar H_{d/2}x_1-\bar H_{d/2}x_2\end{array})$. Thus if we compute $\bar H_{d/2}x_1$ and $\bar H_{d/2}x_2$ recursively, we can compute $\bar H_{d}x$ in $O(d)$ time. The total time to compute $\bar H_{d}x$ thus satisfies the recurrence $T(d)=2T(d/2)+O(d)$, by Master's Theorem it resolves to $O(d\log d)$.



Let $H$ be the normalized WH matrix, i,e, $H=d^{-1/2}\bar H_{d}$. Then all rows are still orthogonal and the norm of any row is $1$. Hence $H$ is an orthogonal matrix and $||Hx||_2^2=||x||_2^2$ for all vectors $x\in\mathbb R^{d}$.

That is, $H$ preserves all norms. The construction of Alion and Chazelle is then as follows:

- Draw a random diagonal matrix $D\in\mathbb R^{d\times d}$ where each entry is uniform be $\pm1$.

- Draw a matrix $P\in\mathbb R^{m\times d}$ with $m=O(\epsilon^{-2}\log n)$ s.t. for every entry, with probability $1-q$ we set the entry to $0$, and otherwise, we let the entry be $\mathcal N(0,(mq)^{-1})$ distributed. The expected number of non-zeroes of $P$ is $qmd$.
- The embedding of $x$ is the $PHDx$.

Computing $Dx$ takes $O(d)$ time. Multiplying the result with $H$ takes $O(d\log d)$ time and multiplying with $P$ takes expected $O(qmd)$ time.

Ailon and Chazelle showed that the construction achieves the JL guarantee if one sets $q=O(\log^2n/d)$, resulting in an embedding time of $O(d\log d+m\log^2n)=O(d\log d+\epsilon^{-2}\log^3n)$.

Proving that the construction satisfies the JL guarantee consists of two steps.

In the first step, we need to show $||HDx||_\infty=O(\sqrt{\log(nd)/d})$ with probability $1-1/n^3$ for any unit vector $x\in\mathbb R^d$. That is, $HD$ ensures that most coordinates of $x$ are small.

In the second step, one shows that, assuming $||HDx||_\infty=O(\sqrt{\log(nd)/d})$, the application of $P$ preserves norms to within $(1\pm\epsilon)$ with good probability. Here we remark that $HD$ preserves the norm of all vectors perfectly but the dimension is still $d$. (Only do first step here.)

Consider the $i$-th coordinate of $HDx$. Each entry of $H$ is either $-d^{-1/2}$ or $d^{-1/2}$ and $D$ multiplies a random sign onto each coordinate of $x$. Hence the $i$-th coordinate is distributed as $\sum_i\sigma_id^{-1/2}x_i$ where the $\sigma_i$'s are independent and uniform among $-1$ and $1$. Clearly $\mathbb E[(HDx)_i]=0$ by linearity of expectation. We prove Hoeffding's theory first.

**Hoeffding's Inequality**. Let $X_1,\cdots,X_d$ be independent random variables where $X_i$ takes values in $[a_i,b_i]$. Let $X=\sum_iX_i$, then

$$
\Pr[|X-\mathbb E[X]|\gt t]\lt2\exp(-\frac{2t^2}{\sum_{i=1}^d(b_i-a_i)^2}).
$$

For sum $\sum_i\sigma_id^{-1/2}x_i$, we have the random variable $X_i=\sigma_id^{-1/2}x_i$ takes values in the interval $[-d^{-1/2}x_i,d^{-1/2}|x_i|]$ and thus $(b_i-a_i)^2=(2d^{-1/2}|x_i|)^2=4d^{-1}x_i^2$. Thus,

$$
\Pr[|X-\mathbb E[X]|\gt t]\lt2\exp(-\frac{2t^2}{\sum_{i=1}^d4d^{-1}x_i^2})\\
$$

$$
=2\exp(-\frac{2t^2}{4d^{-1}||x||_2^2})\\
$$

$$
=2\exp(-t^2d/2).
$$

For $t=C\sqrt{\ln(nd)/d}$ for a big enough constant $C$, this probability is less than $1/(dn)^3$. A union bound over all $d$ coordinates shows that $||HDx||_\infty=O(\sqrt{\ln(nd)/d})$ with probability at least $1-1/n^3$.

Conclude by showing $\mathbb E[||Px||_2^2]=||x||_2^2$ for all vectors $x$, i.e., $P$ preserves norms in expectation. Each entry of $P$ is distributed as the product of a Bernoulli variable $b_{i,j}$ taking value $1$ with probability $q$ and $0$ otherwise, and a variable $n_{i,j}\sim\mathcal N(0,(mq)^{-1})$. We thus get:

$$
\mathbb E[||Px||_2^2]=\sum_{i=1}^m\mathbb E[(\sum_{j=1}^db_{i,j}n_{i,j}x_j)^2]\\
$$

$$
=\sum_{i=1}^m\sum_{j=1}^d\mathbb E[b_{i,j}^2n_{i,j}^2x_j^2]+\sum_{j=1}^d\sum_{h\neq j}\mathbb E[b_{i,j}b_{i,h}n_{i,j}n_{i,h}x_ix_j]\\
$$

$$
=\sum_{i=1}^m\sum_{j=1}^d\mathbb E[b_{i,j}^2]\mathbb E[n_{i,j}^2]x_j^2+\sum_{j=1}^d\sum_{h\neq j}\mathbb E[b_{i,j}]\mathbb E[b_{i,h}]\mathbb E[n_{i,j}n]\mathbb E[_{i,h}]x_ix_j\\
$$

$$
=\sum_{i=1}^m\sum_{j=1}^dq(mq)^{-1}x_j^2\\
$$

$$
=||x||_2^2
$$

In the above, we use $\mathbb E[n_{i,j}]=0$ and $\mathbb E[b_{i,j}^2]=\mathbb E[b_{i,j}]=q$. We also use for $n_{i,j}\sim\mathcal N(0,\sigma^2)$ random variables, we have $\mathbb E[n_{i,j}^2]=\sigma^2$. We also use independence of the entries in $P$ to split the expectation of the product into the product of expectations.