---
title: 数据挖掘 2 Representation-based Clustering
author: tinsir888
date: 2024/2/5
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: b140a7f0
---

# What is Clustering

Grouping a set of data objects into clusters

- Cluster: a collection of data objects
  1. Similar to one another within the same cluster
  2. Dissimilar to the objects in other clusters

Cluster = unsupervised classification **no predefined classes**

Usage

1. Get insight into data distribution
2. Preprocessing step for other algorithms

## Application of Clustering

Pattern Recognition and Image Processing

Spatial Data Analysis

WWW

Biology

Information retrieval

Marketing

City-planning

Social networks

## Basic Concept of Representative-based Approaches

Goal: Construct a partition of a database $D$ of $n$ objects into a set of $k$ clusters minimizing an objective function

- Exhaustively enumerating all possible partitions into $k$ sets in order to find the global minimum is too expensive (exponential time)
- Example objective: minimize the max distance (Manhattan, Euclidean, Geodesic, ...) among the points in the cluster

Heuristic methods:

- Choose $k$ representatives for clusters e.g. randomly
- Improve there initial representatives iteratively
  - Assign each object to the cluster it fits best in the current clustering
  - Compute new cluster representatives based on these assignments
  - Repeat until the change in the objective function from one iteration to the next drops below a threshold

Types of cluster representatives

- K-means: Each cluster is represented by the center of the cluster
- K-medoid: Each cluster is represented by one of its objects.

# K-means Clustering

## Overview

### Basic Ideas

For a given $k$, form $k$ groups so that the sum of the distances between the mean of the groups (centroids) and their elements is minimal.

### Basic Notions

Objects $\mathbf x=(x_1,\cdots,x_d)$ are points in a d-dimensional vector space

Centroid $\mu_c$: Means of all points in a cluster $C$: $\mu_c=\frac{1}{C}\sum_{x_j\in C}x_j$.

Measure of the compactness (aka total distance) of a cluster $C_i$:
$$
TD(C_i)=\sqrt{\sum_{x_j\in C_i}dist(x_j,\mu_{C_i})^2}
$$
Measure of the compactness of a clustering:
$$
TD=\sqrt{\sum_{i=1}^kTD^2(C_i)}
$$
**The ideal clustering minimizes this objective function.**

### Lloyd's Algorithm

Initialize: Partition the objects randomly into $k$ non-empty subsets.

1. Centroid Update: Compute the centroids of the clusters of the current partition. The centroid is the center of a cluster.
2. Cluster Assignment: Assign each object to the cluster with the nearest representative.

Repeat Step 1 until representatives change.

### Pros and Cons

Advantages :thumbsup:

- Relatively efficient $\mathcal O(tkn)$

  $n$ is # of objects, $k$ is # of clusters, $t$ is # of iterations.

- Normally $k,t\ll n$.

- Easy implementation

Disadvantages :thumbsdown:

- Applicable only when mean is defined
- Need to specify $k$ in advance
- Sensitive to noisy data and outliers
- Clusters are forced to have convex shapes.
- Result and runtime are very dependent on initial partition; often terminates at a local optimum.

Variant of k-means, e.g. ISODATA (eliminate small clusters, merging and split of clusters)

## Choice of Parameter $k$

Idea for a method:

1. Determine a clustering for each $k=2,\cdots,n-1$
2. Choose the best clustering

How to measure the quality of a clustering?

- measure has to be independent of $k$!
- The measures for the compactness of a clustering $TD^2$ and $TD$ are monotonously decreasing with increasing value of $k$.

## The Silhouette Coefficient

A measure to clustering

Basic idea:

- Elements in cluster should be similar to their representative

- Elements in different clusters should be dissimilar

$int(o)$: average distance between object $o$ and the objects in its cluster.
$$
int(o)=\frac{1}{C_i}\sum_{x\in C(o)\diagdown\{o\}}dist(o,x)
$$
$ext(o)$: average distance between object $o$ and the objects in its *"second closest"* cluster
$$
ext(o)=\min_{C_i\neq C(o)}(\frac{1}{C_i}\sum_{x\in C_i}dist(o,x))
$$
The silhouette of $o$ is:
$$
s(o)=\frac{ext(o)-int(o)}{\max\{int(o),ext(o)\}}\\
s(o)\in[-1,1]
$$
How good is the assignment of $o$ to its cluster?

$s(o)\rightarrow-1$: bad, on average cluster to members of second-closest cluster.

$s(o)=0$: o lies between two clusters.

$s(o)\rightarrow1$: good assignment of $o$ to its cluster.

Silhouette coefficient $s_C$ of a clustering is average silhouette of all objects

- $s_C\in(0.7,1.0]$ implies strong structure.
- $s_C\in(0.5,0.7]$ implies medium structure.
- $s_C\in(0.25,0.5]$ implies weak structure.
- $s_C\in[-1,0.25]$ implies no structure.

$s_C$ is used for determining the best value of $k$.

# Kernel K-means

Linearity of K-means: K-means assume the boundaries between the clusters are **lines**.

:question:What if the boundaries are not lines? Use Kernel K-means.

## Basic Idea

Use kernels to project points into another space

Separate the points with -means in the new space

Recall k-means objective: $\min_C\sum_{i=1}^k\sum_{x_j\in C_i}||x_j-\mu_i||^2$

Map every point $x_j$ into a different space $\phi(x_j)$

Recall that a kernel is a dot-product $K(x_i,x_j)=\phi(x_i)^T\phi(x_j)$

Thus,
$$
\min_C\sum_{i=1}^k\sum_{x_j\in C_i}||\phi(x_j)-\phi(\mu_i)||^2\\
=\sum_{j=1}^nK(x_j,x_j)-\sum_{i=1}^k\frac{1}{n_i}\sum_{x_a\in C_i}\sum_{x_b\in C_i}K(x_a,x_b)
$$
Kernel K-means can generates non-linear boundaries.

## Pro and Cons

Advantages :thumbsup:

- Allow detection of clusters with arbitrary shape
- Fits any possible kernel

Disadvantages :thumbsdown:

- Inefficient $O(n^2k)$
- Like K-means: need to specify the number of clusters $k$ in advance.
- Requires the choice of a kernel

# K-medoid

## Motivation

K-means assumes Euclidean distance while K-medoid is more general.

- Motivated by L~1~ norm (Manhattan distance)
- Works also in space, where a mean is not defined
- Only input is the possibility to compute pairwise distance

More robust to noise :thumbsup:

### L~p~ Norms

General L~p~-Metric (Minkowski-Distance)
$$
d_p(x,y)=(\sum_{i=1}^d|x_i-y_i|^p)^{1\over p}
$$
$p=2$: Euclidean Distance used in K-means

$p=1$ Manhattan Distance used in K-medoids

## Basic Idea

Find $k$ representatives in the dataset s.t., the sum of the distances between representatives and objects which are assigned to them is minimal.

Medoid: representative object "in the middle"

### Notions

Requires arbitrary objects and a distance function

Medoid $m_C$: representative object in a cluster $C$

Measure for the compactness of a cluster $C$:
$$
TD(C)=\sum_{x\in C}dist(x,m_C)
$$
Measure for the compactness of a clustering
$$
TD=\sum_{i=1}^kTD(C_i)
$$

### PAM Algorithm

Given $k$

1. Select $k$ objects arbitrarily as medoids; assign each remaining object to the cluster with the nearest representative, and compute current $TD$.
2. For each pair medoid M and non-medoid N, compute the value $TD_{M\lrarr N}$.
3. Select the non-medoid $N$ for which $TD_{M\lrarr N}$ is minimum.
4. If $TD_{M\lrarr N}$ is smaller than current $TD$
   - Swap $M$ with $N$
   - Set current $TD=TD_{M\lrarr N}$.
   - Go back to step 2.
5. Else stop

## CLARA & CLARANS

CLARA

- additional parameter: numlocal
- Draws numlocal samples of the data set
- applies PAM on each sample
- returns the best of these sets of medoids as output

CLARANS

- two additional parameters: maxneighbor and numlocal
- at most maxneighbor many pairs are evaluated in the algorithm.
- The first pair (M,N) for which TD is smaller than current one is swapped instead of finding the minimal one.
- Finding the local minimum with this procedure is repeated numlocal times.

Efficiency: runtime(CLARANS) < runtime(CLARA) < runtime(PAM)

## Pros and Cons

Advantages :thumbsup:

- Applicable to arbitrary objects + distance functions
- Not as sensitive to noisy data and outlier as K-means

Disadvantages :thumbsdown:

- Inefficient
- Need to specify the number of clusters $k$ in advance
- Result and runtime for CLARA and CLARNAS may vary largely depend on randomization.

# Expectation Maximization

Consider points $\mathbf x_j=(x_1,\cdots,x_d)$ from a d-dimensional Euclidean vector space

Each cluster is represented by a probability distribution

Typically: mixture of Gaussian distributions



Single distribution to represent a cluster $C_i$

- Center point $\mu_i$ of all points in the cluster
- Covariance matrix $\Sigma_i$ for the points in the cluster $C_i$

$$
f(\mathbf x|\mu_i,\Sigma_i)=\frac{1}{\sqrt{(2\pi)^d|\Sigma_i|}}e^{-\frac{1}{2}(\mathbf x-\mu_i)^T\Sigma_i^{-1}(\mathbf x-\mu_i)}
$$

The density for the clustering
$$
f(\mathbf x)=\sum_{i=1}^kf(\mathbf x|\mu_i,\Sigma_i)P(C_i),\sum_{i=1}^kP(C_i)=1
$$
:question: How do we find the parameters $\theta_i=\mu_i,\Sigma_i,P(C_i)$? Maximize the log-likelihood (MLE)
$$
\arg\max_{\theta_1,\cdots,\theta_k}\ln P(\mathbf D|\theta_1,\cdots,\theta_k)
$$
where $\ln P(\mathbf D|\theta_1,\cdots,\theta_k)=\ln\prod_{j=1}^nf(\mathbf x_j)=\sum_{j=1}^n\ln f(\mathbf x_js)$

$=\sum_{j=1}^n\sum_{i=1}^k\ln f(x_j|\mu_i,\Sigma_i)P(C_i)$

## EM algorithm

1. Assume we have already computed the parameters $\theta_1,\cdots,\theta_k$
2. Compute the probability of each cluster conditioned to each point
3. Update the parameters $\theta_1,\cdots,\theta_k$.

## Pros and Cons

Advantages :thumbsup:

- Flexible and powerful probabilistic model
- Captures overlapping clusters

Disadvantages :thumbsdown:

- Convergence to local minimum
- Computation effort $O(nkt)$
- Both result and runtime strongly depend on
  - initial assignment
  - a proper choice of parameter $k$
- Modification to obtain a partitioning variant