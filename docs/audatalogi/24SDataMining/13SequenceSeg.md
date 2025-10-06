---
title: 数据挖掘 13 Sequence Segmentation and Similarities
author: tinsir888
date: 2024/4/29
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: 9551f211
---

How to segment a sequence of points with an efficient algorithms and how to find similar documents in a linear manner.

2 algorithm.

The first is clustering a sequence to form $k$ partitions with a dynamic programming.

The second is an approximation that allows to compute pairwise distance avoiding $\mathcal O(n^2)$. The idea is to use efficient signatures and hash the signatures in bands so as similar items will likely end up in the same bucket.

# Sequence Segmentation

A sequence is a vector $(x_1,\cdots, x_n)\in\mathbb R^n$.

Sequence segmentation: Given a set sequence $(x_1,\cdots,x_n)$, find $k$ contiguous segments that are as homogeneous as possible.

The sequence segmentation problem requires a notion of homogeneity similar to one of clustering. Points in the segment should be subsequences rendering the problem different from that of clustering.

A sequence is **homogeneous** if its points have a small deviation from the mean. Let $\mathcal D$ be a sequence of $n$ $d$-dimensional points.

Definition of $k$-segmentation: A $k$-segmentation $S$ is a partition of $\mathcal D$ into $k$ **contiguous** segments $(s_1,\cdots,s_k)$. Each segment $s_i$ is represented by a $d$-dimensional **mean vector** $\mu_i=\frac{1}{|s|}\sum_{\mathbb x\in s}\mathbb x$.

The mean vector has the same role of the centroid for $k$-means clustering. The error $E(S)$ of a $k$-segmentation $S$ is the error of replacing each individual point with the means. Similar to $k$-means, one of the most common errors is **sum of squares error**:
$$
E(S)=\sum_{s\in S}\sum_{\mathbb s\in S}(\mathbb x-\mu_s)^2
$$

## Problem of k-segmentation problem

Given a sequence $\mathcal D=(\mathbb x_1,\cdots,\mathbb x_n)$ of length $n$, find a set of $k$ segments $S$ of $\mathcal D$ s.t. the SSE error is minimized.

## A Dynamic Programming Solution

A dynamic programming solution from Bellman solves the $k$-segmentation problem in polynomial time.

The idea is to build the solution bottom-up solving progressively larger instances of the problem.

DP requires the property of optimal sub-problem that states that the solution on a subset of the input is also optimal.

Denote $D[1,i]$ the sub-sequence $\mathbb x_1,\cdots,\mathbb x_i$ with $i\le n$. $E(i,s)$ is the error of the optimal segmentation of the sub-sequence $D[1,i]$ with $s$ segments with $s\le k$. The dynamic programming algorithm follows the recursion
$$
E(i,s)=\min_{s\le j\le i-1}\left(E(j,s-1)+\sum_{j+1\le t\le i}(\mathbf t-\mu_{j+1,i})^2\right)
$$


In practice we can use a 2-dimensional table that stores in position $s,i$ the value $E(i,s)$.

### Pseudo-code

Input: Sequence $\mathcal D,k,E(\cdot)$

1. for each $i\gets1\cdots n$ do
   - $A[1,i]=E(D[1\cdots i])$
2. for each $s\gets1\cdots k$ do
   - $A[s,s]=0$
3. for each $s=2\cdots n$ do
   - for each $i=s+1,\cdots,n$ do
     - $A[k,i]=\min_{j\lt i}(A[s-1,j]+E(D[j+1\cdots i]))$

{% note warning flat %}

To recover the actual segmentation, the matrix $A$ should store also the values $j$ minimizing the error.

{% endnote %}

### Complexity

The algorithm needs to fill $\mathcal O(nk)$ table, each cell computes the minimum, takes $\mathcal O(n)$ cells to check, each of it computes the error in $\mathcal O(n)$. Thus total complexity of $\mathcal O(n^3k)$ for naive version.

The errors per cell can be further optimized by noticing that
$$
\sum_{j+1\le t\le i}(\mathbf t-\mu_{[j+1,i]})^2=\sum_{j+1\le t\le i}\mathbf t^2-\frac{1}{i-j}(\sum_{j+1\le t\le i}\mathbf t)^2
$$
that requires the repeated computation of $\mathbf t^2$ and $\mathbf t$. These two terms can be easily pre-computed for all $i=1\cdots n$. By this optimization, complexity is reduced to $\mathbf O(n^2k)$.

Heuristic: we can further reduce the computational burden by using **greedy approaches** or local search that assigns border points randomly and move the in a way to reduce the errors. The total time is $\mathcal O(nk)$.

# Finding Similar Points Efficiently

Comparing each point in the data with other, raising $\mathcal O(n^2)$.

Given a dataset $\mathcal D=\{\mathbb x_1,\cdots,\mathbb x_n\}$ of $d$-dimensional data points, find all the pairs of data point $(\mathbb x_i,\mathbb x_j)$ s.t. $d(\mathbb x_i,\mathbb x_j)\le s$ for some distance function $d$.

With Locality-Sensitive Hashing, we can get rid of $\mathcal O(n^2)$.

> LSH 不是 [今天上午的随机算法课](https://tinsir888.github.io/posts/b29af35a.html) 才讲的么……这下闭环了。

Definition of Jaccard similarity: of two sets is the size of their intersection $sim(C_1,C_2)=|C_1\and C_2|/|C_1\or C_2|$. The Jaccard distance is then $d(C_1,C_2)=1-|C_1\and C_2|/|C_1\or C_2|$.

A large number of documents do not fit into memory and we need a representation of each document that allows for preserving the distances while considerably reducing the memory footprint. In order to achieve such a gold, we use the 3 steps:

1. Shingling: Convert documents to sets.
2. Min-hashing: Convert large sets to short signatures, while preserving similarity.
3. LSH: Focus on pairs of signatures likely to be from similar documents.

## Shingling

A $k$**-shingle** is a set of $k$ tokens that appear in the document.

Idea: we return a large number of sparse vector with low similarity.

For instance, the set of $2$-shingle for document $D_1=abcab$ is $S(D_1)=\{ab,bc,ca\}$.

Long shingles can be easily compressed by hashing. Documents are compared through the hash values. Finally each document is a binary vector in the space of $k$-shingle in which a shingle is a dimension.

{% note warning flat %}

We assume that documents have a large number of common shingles if they are similar. However, $k$ is a critical choice to compare documents. A small $k$ leads to a large number of similar documents. As rule of thumb $k=5,10$ is a reasonable choice for short/large documents.

{% endnote %}

Shingling doesn't solve the problem of a large number of similarity computation.

## Min-hash

The document is converted into a set of shingles and consequently as a binary vector, where each dimension is a shingle and a $1$ in position $i$ of document $j$ indicates that the document contains the shingle $i$. This way can we represent our dataset as a matrix where each row represents. 

Computing Jaccard similarity in such a matrix relies on simple bit-wise AND, intersection and OR.

Min-Hashing is used for faster comparison. It converts the binary representation into small signatures through hashing. The key idea is to hash each column to a small signatures $h(C)$ s.t.

- $h(C)$ is small enough that the signature fits in RAM
- $sim(C_1,C_2)$ is the same as the similarity of signatures $h(C_1),h(C_2)$.

Definition of min-hash function: The hash function $h_\pi(C)$ returns the index of the first row in which column $C$ has value $1$.
$$
h_\pi(C)=\min_\pi(C)
$$
Hash function is a approximation of a set.

Min-hash preserves the property that $\Pr[h_\pi(C_1)=h_\pi(C_2)]=sim(C_1,C_2)$.

### A trick

For each column $C$, and hash function, $k_i$ keep a slot for the min-hash value. Initialize $Sig(C)[i]=\infty$, then scan rows looking for $1$s. How to choose $k_i$? Using *universal hashing*.

> 又是随机算法前面学过的东西。

## LSH

LSH tackles the problem of the comparison by hashing the signatures in an appropriate manner.

Intuition: use a function $f(x,y)$ to determine whether $x$ and $y$ is a candidate pair whose similarity must be evaluated. For Min-hash matrices we hash the columns of the signature matrix $M$ to different buckets. If two documents hashes into the same buckets, they become a candidate pair with similarity $sim\ge threshold$.

LSH works:

- Pick a similarity threshold $s$.
- The columns of $x$ and $y$ of $M$ are a candidate pair if their signatures agree on at least a fraction $s$ of their rows. $M(i,x)=M(i,y)$ for at least a fraction $s$ of $i$.
- The idea is to hash columns of signatures matrix $M$ several times. Similar columns should be likely to hash to the same bucket with high probability.

LSH partitions $M$'s columns into $b$ bands each of $r$ rows. For each band, compute a signatures using a hash function with $k$ buckets, where $k$ is arbitrarily large. The candidate column pairs are those that hash to the same bucket for at least $1$ band. Tune $b$ and $r$ to catch most similar pairs, few non-similar pairs.

### How to choose $b$ and $r$?

Balances false positives / negatives.

We need to strike a trade-off with the number of bands and the number of rows.

As $b$ increases, the method becomes more reliable, but the time increases as well.

If $t=sim(C_1,C_2)$ the probability that all rows in a band are equal is $t^r$ and probability that some row in a band are different is $1-t^r$.

As such the probability that no band is identical is $(1-r^t)^b$ that leads to $(1-(1-r^t)^b)$ the probability that at least $1$ band is identical.

## Pros and Cons

{% note success flat %}

:thumbsup:Finds similar items in linear time

:thumbsup:Easy and fast to implement

:thumbsup:Generalizes to other similarity measures

{% endnote %}

{% note danger flat %}

:thumbsdown:Performance depend on the choice of $b,r$

:thumbsdown:Requres a fairly large number of buckets

{% endnote %}