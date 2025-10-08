---
title: 随机算法 12 Nearest Neighbor Search and Locality Sensitive Hashing
author: tinsir888
date: 2024/4/29
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: b29af35a
---

# Nearest Neighbor Search

2D scenario: Voronoi Diagram

3D or higher dimensions: Not efficient.

Fine-grained complexity

## Approximate nearest neighbor search

Assume the distance between query point and its nearest neighbor is $R$, $c$-approximate $R$-near neighbor is all the points within range $cR(c\ge1)$.

- input: point $q$, constant $c$, radium $R$.
- if there is a point $p$ with $dist(p,q)\le R$, then return point $p'$ with $dist(p',q)\le cR$ with probability $\ge 1-\delta$.

# Locality Sensitive Hashing

abbr. LSH

Distance: $dist:\mathbb R^d\times\mathbb R^d\rightarrow\mathbb R$. $\ell_1,\ell_2$, Manhattan, Hamming.....

Hash function $h$ is $(R,cR,P_1,P_2)$-sensitive w.r.t. $dist$ if

1. $\forall p,q$ with $dist(p,q)\le R:\Pr[h(p)=h(q)]\ge P_1$.
2. $\forall p,q$ with $dist(p,q)\ge cR:\Pr[h(p)=h(q)]\le P_2$.
3. $P_1\ge P_2$.

Store all points $p\in S$ in hash table with key $h(p)$ which $h$ is $(R,cR,P_1,P_2)$-sensitive.

$A[y]$ is linked list of all points $p'\in S$ with $h(p')=y$,

```java
HashMap<Integer,linkedList<Point>>: hm:...
//......
for(Point p: points){
    Integer key=h(p);
    if hm.contains(key){
        hm.get(key).add(p);
    }
    else {
        linkedList<Point> l=...
        l.add(p);
        hm.put(key,l);
    }
}
```

Query:

```java
Point Query(Point q){
	Integer key=h(q);
    linkedList<Point>l=hm.get(key);
    for(Point p:l){
        if(dist(p,q)<=c*R){
            return p;
        }
    }
    return null;
}
```

Let $X_p$ be $1$ if $h(p)=h(q)$ and $0$ otherwise.

$$
\mathbb E[\sum_pX_p]=\sum_p\mathbb E[X_p]=\sum_p\Pr_h[h(p)=h(q)]
$$


Parameter $k$, $g(p)$ is the concatenates of $0-1$ bits of $h_1(p),\cdots,h_k(p)$. $h_i$ are independent.

For $dist(p,q)\le R$, $\Pr_g[g(p)=g(q)]\ge P_1^k$.

For $dist(p,q)\ge R$, $\Pr_g[g(p)=g(q)]\le P_2^k\le1/n$.

Let $X_p$ be $1$ if $g(p)=g(q)$ and $0$ otherwise.

$$
\mathbb E[\sum_pX_p]=\sum_{dist(p,q)\ge cR}\Pr[g(p)=g(q)]+\sum_{dist(p,q)\le R}\Pr[g(p)=g(q)]
$$


Choosing $k=\log_{P_2}(1/n)$

Better probability:

For parameters $L,k$. For $i=1,\cdots,L$, $g_i(p)$ is the concatenates of $0-1$ bits of $h_{i1}(p),\cdots,h_{ik}(p)$. $h_i$ are independent.

Distance metric: Hamming distance.

for $i=1,\cdots,L$: store $S$ in hash table $H_i$.

Query(q):

- For $i=1,\cdots,L$

  - scan list $H_i[g_i(q)]$:

    if see $p$ with $dist(p,q)\le cR$

    - return p

  - if scan $>3L$ points

    - abort

Space: $O(Ln+nd)$

Query time: $O(Lkt+Ld)$ $L$ is time to evaluate $h$.

## Probability Bound for Failure

Two types of failure:

$E_1=\{\forall_i:g_i(q)\ne g_i(p^*)\}$

$E_2=\{\sum_{i=1}^L|\{p:dist(p,q)\gt cR\land g_i(q)=g_i(p)\}|\gt3L\}$

If none of them happens, the results is correct.

We want $\Pr[E_1\cup E_2]\le\Pr[E_1]+\Pr[E_2]\le1/2$.

$$
\Pr[E_2]\le\mathbb E[\sum_{i=1}^L|\{p:dist(p,q)\gt cR\land g_i(q)=g_i(p)\}|]/3L\\
$$

$$
=\sum_{i=1}^L....\\
$$

$$
\le L/3L=1/3\\
$$

$$
\Pr[E_1]=\Pr[\forall i:g_i(p)\neq g_i(p^*)]\\
$$

$$
=\prod_{i=1}^L\Pr[g_i(q)\neq g_i(p^*)]\\
$$

$$
=\prod_{i=1}^L(1-\Pr[g_i(q)=g_i(p^*)])\\
$$

$$
=(1-P_1^k)^L\\
$$

Choosing $L=\log_{1-P_1^k}\frac{1}{6}$, we will get $1/2$ probability bound for failure.

## Upper bound for L

$$
L=\frac{\ln 1/6}{\ln(1-P_1^k)}\le\frac{\ln(1/6)}{\ln(\exp(-P_1^k))}=\frac{\ln1/6}{-P_1^k}=\ln6/P_1^k=O(P_1^{-k})\\
$$

$$
P_1^{-k}=\frac{1}{P_1^{\log_{P_2}1/n}}\\
$$

$$
=\cdots\cdots\\
$$

$$
=n^{\ln P_1/\ln P_2}=n^{\ln(1/P_1)/\ln(1/P_2)}
$$

For hamming distance:

$$
P_1=1-R/d\\
$$

$$
P_2=1-cR/d\\
$$

$$
\sup L=n^{1/c}
$$

# Digest from Reference Paper

Efficient algorithms for the approximate and exact nearest neighbor problem.

Goal: Preprocess a dataset of objects. Later, given a new query object, one can quickly return the dataset object that is most similar to the query.

Nearest Neighbor Problem

Given a collection of $n$ points, build a data structure which, given any query point, reports the data point that is closest to the query.

A point $\mathbb R^d$ and the distance metric. It's hard when $d$ is large.

In approximation version, the algorithm is allowed to return a point whose distance from the query is at most $c$ times the distance from the query to its nearest points.

$c\gt1$ is called the *approximation factor*.

One of the most popular algorithms for performing approximate search in high dimensions based on **locality-sensitive hashing**.

Key idea: hash the point using several hash functions to ensure that for each function, the probability of collision is much higher for objects that are close to each other than for those that are far apart. Then one can determine near neighbors by hashing the query point and retrieving elements stored in buckets containing that point.

Definitions

We say point $p$ is an $R$**-near neighbor** of a point $q$ if the distance between $p$ and $q$ is at most $R$.

Definition of **Randomized $c$-approximate $R$-near neighbor** (abbr. $(c,R)$-NN). Given a set $P$ of points in a $d$-dimensional space $\mathbb R^d$, and parameters $R\gt0,\delta\gt0$, construct a data structure s.t., given any query point $q$, if there exits an $R$-near neighbor of $q$ in $P$, it reports some $cR$-near neighbor of $q$ in $P$ with probability $1-\delta$.

- We can assume $R=1$, just by simply dividing all coordinates by $R$. Thus $c$-NN.

Definition of **Randomized $R$-near neighbor reporting**. Given a set $P$ of points in a $d$-dimensional space $\mathbb R^d$, and parameter $R\gt0,\delta\gt0$, construct a data structure that, given any query point $q$, reports **each** $R$-near neighbor of $q$ in $P$ with probability $1-\delta$.

Locality-Sensitive Hash Function

The LSH algorithm relies on the existence of *locality-sensitive hash functions*.

Let $\mathcal H$ be a family of hash functions mapping $\mathbb R^d$ to some universe $U$. For any 2 point $p$ and $q$, consider a process in which we choose a function $h$ from $\mathcal H$ uniformly at random, and analyze the probability that $h(p)=h(q)$.

The family $\mathcal H$ is called $(R,cR,P_1,P_2)$**-sensitive** if any two points $p,q\in\mathbb R^d$.

- if $\Vert p-q\Vert\le R$ then $\Pr_\mathcal H[h(q)=h(p)]\ge P_1$
- if $\Vert p-q\Vert\ge cR$ then $\Pr_\mathcal H[h(q)=h(p)]\le P_2$

> 如果两个点很接近的话，那它们哈希碰撞的概率很高。
>
> 如果两个点很远的话，那它们哈希碰撞的概率很低。

In order for a locality-sensitive hash (LSH) family to be useful, it has to satisfy $P_1\gt P_2$.

LSH Algorithm

An LSH family $\mathcal H$ is used for efficient algorithm for approximate near neighbor search.

We need to amplify the gap between $P_1$ ad $P_2$.

For parameters $k$ and $L$, we choose $L$ functions $g_j(q)=(h_{1,j}(q),\cdots,h_{k,j}(q))$, where $h_{t,j}(1\lt t\lt k,1\lt j\lt L)$ are chosen independently and uniformly at random from $\mathcal H$. We are also given a distance function dist: $\mathbb R^d\times\mathbb R^d\rightarrow\mathbb R$. These are the actual functions that we use to hash the data points.

The data structure is constructed by placing each point $p$ from the input set into a bucket $g_j(p)$. This way the data structure uses only $O(nL)$ memory. It suffices that buckets store the pointers to data points, not the points themselves.

To process a query $q$, we scan through the buckets $g_1(q),\cdots,g_L(q)$, and retrieve the points stored in them. If there is an $R$-near neighbor $p$ of $q$ $dist(p,q)\le R$, we must report some point $p'$ that is a $cR$-near neighbor of $q$.

Two concrete scanning strategies:

1. Interrupt the search after finding the first $L'$ points for some parameter $L'$.

   **$(c,R)$-near neighbor problem**

   With $L'=3L$, yields a solution to the *randomized $c$-approximate $R$-near neighbor problem*, with $R$ and $\delta$ for some constant failure probability $\delta\lt1$. To obtain this guarantee, it suffices to set $L$ to $\Theta(n^\rho)$, where $\rho=\frac{\ln 1/P_1}{\ln 1/P_2}$.

2. Continue the search until all points from all buckets are retrieved

   **$R$-near neighbor reporting problem**

   The value of the failure probability $\delta$ depends on the choice of the parameters $k$ and $L$. For each $\delta$, one can provide parameters $k$ and $L$ so that the error probability is smaller than $\delta$. The query time is also dependent on $k$ and $L$, the worst case could be as high as $\Theta(n)$. But for many natural datasets, a proper choice of parameters results in a sublinear query time.

   The details of the analysis are as follows. Let $p$ be any $R$-neighbor of $q$, consider any parameter $k$. For any function $g_i$, the probability that $g_i(p)=g_i(q)$ for some $i=1,\cdots,L$ is at least $1-(1-P_1^k)^L$. If we set $L=\log_{1-P_1^k}\delta$ so that $(1-P_1^k)^L\le\delta$, then any $R$-neighbor of $q$ is returned by the algorithm with probability at least $1-\delta$.
   
   How to choose $k$? Larger $k$ lead to a larger gap between the probabilities of collision for close points and far points.

LSH Library

Several distance metrics is defined here.

Hamming distance, $\ell_1$ distance, $\ell_s$ distance, Jaccard, Arccos etc.

Near-Optimal LSH Function for Eucildean Distance

A new LSH family, yielding an algorithm with query time exponent $\rho(c)=1/c^2+O(\log\log n/\log^{1/3}n)$. For large enough $n$ the value of $\rho(c)$ tends to $1/c^2$.

