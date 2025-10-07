---
title: 数据挖掘 3 Densiy-based Clustering
author: tinsir888
date: 2024/2/12
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: 87c5c549
---

Density-based departs from the rigid geometrical structure of representative-based clustering and defines cluster as *high-density regions separated by low density regions*.

*Density* is number of points in a certain region in proportion to the size of the region. Different **definition of density**, different density-based approaches.

# DBSCAN

A density-based clustering algorithm grounded into the intuition that a point in a cluster should be **density reachable** from any other point in that cluster, i.e., there should be "enough" points around each point in a cluster.

This standard does not include any specific on the shape of the cluster, but only constrains the **neighborhood** of a point. Thus, DBSCAN can handle clusters with arbitrary shape.

## Definitions

**Neighborhood**: For each point $\mathbf x$, we define the neighborhood the set of points that are at distance at most $\epsilon$ from $\mathbf x$, formally $N_\epsilon(\mathbf x)=\{\mathbf y\in\mathcal D|dist(\mathbf x,\mathbf y)\le\epsilon\}$.

Additionally, a neighborhood is dense if it contains at least $MinPts$ points, i.e., $|N_\epsilon(\mathbf x)|\ge MinPts$.

**Core object**: A point $\mathbf x$ whose neighborhood contains at least $MinPts$ points.

**Density-reachable**: $\mathbf y$ is **directly** density-reachable from $\mathbf x$ within $\epsilon,MinPts$, if $\mathbf y\in N_\epsilon(\mathbf x)$, and $\mathbf x$ is a core-object.

A point $\mathbf p$ is density reachable from $\mathbf x$ if $\mathbf p$ is directly density reachable from a point $\mathbf y$ in the neighborhood $N_\epsilon(\mathbf x)$. Density reachability is the transitive closure of directly density reachability. Note that the density reachability is **not symmetric**. In particular, since $\mathbf p$ might not have $MinPts$ in its neighborhood, it cannot "reach" $\mathbf x$ with a sequence of dense areas.

**Density-connected**: $\mathbf x$ is density-connected to a point $\mathbf y$ if there is a point $\mathbf p$ such that both $\mathbf x$ and $\mathbf y$ are density-reachable from $\mathbf p$.

> $\mathbf x$ and $\mathbf y$ are not necessarily density-reachable in this case.

**Density-based cluster**: a non-empty subset $S\subseteq\mathcal D$ of the data $\mathcal D$ that satisfies both

- Maximality: If a point $\mathbf x\in S$ and $\mathbf y$ is density-reachable from $\mathbf x$, then $\mathbf y\in S$.
- Connectivity: Each object in $S$ is density-connected to all other objects.

A density-based clustering of a database $\mathcal D$ is a partition $\{C_1,\cdots,C_k;N\}$ s.t. $\bigcup_{i=1}^kC_i\cup N=\mathcal D$, $C_i\cap C_j=\emptyset,j\gt i,i,j\in[1,k]$, and $C_i\cap N=\emptyset$, where $C_1,\cdots,C_k$ are the density-based clusters. $N=\mathcal D\diagdown\{C_1,\cdots,C_k\}$ is the noise cluster of objects that do not belong to any cluster.

## DBSCAN Algorithm

### Pseudo code

Input: dataset $\mathcal D,\epsilon,MinPts$

- for each $\mathbf x\in \mathcal D$, do

  - if $\mathbf x$ is not part of any cluster $C$ then

    - if $\mathbf x$ is a core project then

      $C\leftarrow$ Compute the density-reachable points from $\mathbf x$

      Create a new cluster $C$.

    - else

      $N\leftarrow N\cup\{x\}$



The main theoretical idea behind the DBSCAN algorithm is that each object in a density-based cluster $C$ is density-reachable from any of its core objects and nothing else is reachable from the core objects.

The DBSCAN is complete and ensures the two properties of maximality and connectivity. The DBSCAN algorithm is a simple iteration around the objects in the database, starting from a random point and finding all reachable points.

Density-reachable objects are collected by performing successive $\epsilon$-neighborhood queries that find the points at distance $\epsilon$ from another point.

### Complexity Analysis

When dimensionality is high, DBSCAN runs in $\mathcal O(n^2)$. By using specialized indexes for spatial queries, e.g., $R^*$ trees, DBSCAN can run faster than CLARANS that as a runtime complexity of $\mathcal O(k^3+nk)$.

|                                   |  $N_\epsilon$-query  |        DBSCAN         |
| :-------------------------------: | :------------------: | :-------------------: |
|  Without support (worst case)   |   $\mathcal O(n)$    |   $\mathcal O(n^2)$   |
| Tree based support (e.g. $R^*$) | $\mathcal O(\log n)$ | $\mathcal O(n\log n)$ |
| Direct access to the neighborhood |   $\mathcal O(1)$    |    $\mathcal O(n)$    |

## Tuning $\epsilon$ and $MinPts$

Before using DBSCAN, we need to decide on parameters $\epsilon$ and $MinPts$. This choice determines the shape and the number of clusters; low $\epsilon$ or high $MinPts$ would find too many small clusters, a high $\epsilon$ might trivially terminate with a single cluster containing the entire dataset.

To find good $\epsilon$ and $MinPts$, there are some heuristics. For example, one could use the point density of the least dense cluster to determine the parameters. One common heuristic uses the distance from $k$-nearest neighbors.

- $kdist(\mathbf x)$ is the distance from $\mathbf x$ to its $k$-nearest neighbor
- $kdist$ plot is a chart of the $k$-distances of all points, sorted in decreasing order of distance.

The heuristic works as follows

- Fix a value for $MinPts$. If there is no domain knowledge available, a common choice is $2d-1$ where $d$ is the dimension.
- Select a "border object" $\mathbf x$ from the $MinPts$ distance polt, $\epsilon$ is set to $MinPtsdist(\mathbf x)$

## Pros and Cons

:thumbsup: Advantages:

1. Does not require to specify number of clusters.
2. Performs well with clusters of arbitrary shape.
3. DBSCAN is robust to outlier and is able to handle them.

:thumbsdown:Disadvantages:

1. It requires domain knowledge for $MinPts$ and it is not easy to determine $\epsilon$.
2. If clusters are very different in terms of in-cluster densities, then DBSCAN is not well suited to define clusters as it cannot generalize well to clusters with very different densities.

# DENCLUE

DENCLUE detects dense regions without specifically specifying the size of the neighborhood.

Main idea: a generalization of neighborhood density in terms of **density distribution**.

## Density Estimation

To estimate an unknown density function in a dataset.

"Non-parametric"

Density estimation:

- determines an unknown probability density function
- it is non-parametric and does not assume a fixed probability model
- estimates the probability density at each point

### Univariate density estimation

In one dimension, we can model the data as a random variable $X$.

Unidimensional data points can be treated as $n$ samples or observations $\{x_i\}$ from such a random variable.

We can estimate the cumulative density function CDF by counting the number of points less than a certain value $v$

$$
\hat F(v)=\frac{1}{n}\sum_{i=1}^nI(x_i\le v)
$$

where $I$ is the indicator functions. which is $1$ if the conditions inside the $(\cdot)$ is satisfied and $0$ otherwise.

The density function is then estimated by taking the derivative of the CDF

$$
\hat f(x)=\frac{\hat F(x+{h\over2})-\hat F(x-{h\over2})}{h}={k/n\over h}={k\over nh}
$$

where $k$ is the number of points in a window of width $h$ centered on $x$. The density estimate is ratio of points in the window $(k/n)$ to the volume of the window $h$.

**Kernel density estimation**: We need to be careful on the window size $h$. We define a **kernel function** $K$ that is a probability density function: :warning:~~Don't mess up with kernel in k-means~~

- non-negative $K(x)\ge0$
- symmetric $K(-x)=K(x)$
- integrates to $1$, i.e., $\int K(x)dx=1$.

The discrete kernel is the simplest kernel and it corresponds to a degenerate uniform probability density function

$$
K(z)=\begin{cases}
1\text{ if }|z|\le{1\over2}\\
0\text{ otherwise}
\end{cases}
$$

when $|z|=\frac{x-x_i}{h}\le{1\over2}$, the point $x_i$ is inside a window of size $h$ centered at point $x$. We can then rewrite the density estimator as

$$
\hat f(x)=\frac{1}{nh}\sum_{i=1}^nK({x-x_i\over h})
$$

The discrete kernel is not smooth, meaning that the kernel estimate looks again like a histogram with the consequence that many useful calculus operator cannot be comfortably performed.

**Gaussian kernel**: Kernel used in DENCLUE. The Gaussian kernel is one of the most popular kernels and represents a Gaussian distribution centered around a point.

$$
K(z)=\frac{1}{\sqrt{2\pi}}\exp(-{z^2\over2}).
$$

As usual, by $z={x-x_i\over h}$ we obtain

$$
K({x-x_i\over h})=\frac{1}{\sqrt{2\pi}}\exp(-{(x-x_i)^2\over2h^2})
$$

where $x$ at the center of the window plays the role of the mean, and the window size $h$ becomes standard deviation.

### Multivariate density estimation

For $d$-dimensional data $\mathbf x=(x_1,\cdots,x_d)$, the window $h$ becomes a hypercube centered at $x$ with volume $h$, that is $vol(H_d(h))=h^d$. Density estimation is the same:

$$
\hat f(x)=\frac{1}{nh^d}\sum_{i=1}^nK({x-x_i\over h})
$$

where the multivariate kernel must preserve non-negativity, symmetry, and integrate to 1. It is then straightforward to redefine the discrete and Gaussian kernels.

**Discrete multivariate kernel** Similar to univariate counterpart, the multivariate kernel is

$$
K(z)=\begin{cases}
1\text{ if }|z|\le{1\over2}\text{ for all dimensions}\\
0\text{ otherwise}
\end{cases}
$$

The Gaussian kernel uses a equi-variate covariance matrix which translates into using the identity matrix $\Sigma=\mathbf I$

$$
K(\mathbf z)=\frac{1}{(2\pi)^{d\over2}}\exp(-{\mathbf z^T\mathbf z\over2})
$$

### Nearest neighbor estimation

Instead of fixing the size $h$ of the region, the nearest neighbor estimate fixes the number of points $k$ in the neighborhood of a point $\mathbf x$ and finds the size of the region.

KNN proceeds:

1. Fix $k$, the minimum number of points that defines a dense region.
2. Compute the volume $vol(S_d(h_\mathbf x))$ as a function of $k$.

Given $k$, we estimate density at $\mathbf x$ as

$$
\hat f(\mathbf x)={k\over n\cdot vol(S_d(h_\mathbf x))}
$$

where $h_\mathbf x$ is the distance from $\mathbf x$ to its $k$-nearest neighbor. The volume is the volume of the $d$-dimensional hypersphere centered at $\mathbf x$.

## Density Attractors

How can we find dense clusters?

> Look at the density function and set a threshold $\xi$ i.e. a horizontal line in a density points histogram. However, if so, some points in low-density regions might not be assigned to any cluster.

**Density attractor**: A point $\mathbf x^*$ is a density attractor if it is a local maxima of the probability density function $f$.

In order to discover these peaks in the density function, we need to compute its gradient.

$$
\nabla\hat f(\mathbf x)={\partial\over\partial\mathbf x}\hat f(\mathbf x)={1\over nh^d}\sum_{i=1}^n{\partial\over\partial\mathbf x}K({\mathbf x-\mathbf x_i\over h})
$$

For the Gaussian kernel, the gradient takes a particularly neat form

$$
{\partial\over\partial\mathbf x}K({\mathbf x-\mathbf x_i\over h})=K({\mathbf x-\mathbf x_i\over h})\cdot({\mathbf x-\mathbf x_i\over h})\cdot({1\over h})
$$

which yields

$$
\nabla\hat f(\mathbf x)={1\over nh^{d+2}}\sum_{i=1}^nK({\mathbf x-\mathbf x_i\over h})\cdot(\mathbf x_i-\mathbf x)
$$

**Density-based cluster**: A set of $C$ of data points from a data set $\mathcal D$ is a density-based cluster w.r.t. some threshold $\xi$ if there exist a set of attractors $\mathbf x_1^*,\cdots,\mathbf x_k^*$ if

- Each point $\mathbf x$ in $C$ attracted to some attractor $\mathbf x_i^*$.
- Each density attractor $\mathbf x_i^*$ exceeds some density threshold $\xi$, i.e., $\hat f(\mathbf x_i^*)\ge\xi$.
- Any two density attractors $\mathbf x_i^*,\mathbf x_j^*$ are density reachable. That is, there exists a path from $\mathbf x_i^*$ to $\mathbf x_j^*$ such that for all points $\mathbf y$ on the path, $\hat f(\mathbf y)\ge\xi$.

Not all points will be part of a cluster. We treat them as noise.

The DENCLUE algorithm: For each point in the data, it finds a candidate attractor. If the attractor's density is above the threshold $\xi$, it is added to the set $A$ of attractors and the point is added to the set $R$ of points attracted by $\mathbf x^*$. Then all the maximal sets of mutually density reachable attractors computed.

To find an attractor for a point $\mathbf x$ we can solve $\nabla\hat f(\mathbf x)=0$, thus

$$
{1\over nh^{d+2}}\sum_{i=1}^nK({\mathbf x-\mathbf x_i\over h})\cdot(\mathbf x_i-\mathbf x)=0\\
$$

$$
\Rightarrow\mathbf x=\frac{\sum_{i=1}^nK({\mathbf x-\mathbf x_i\over h})\mathbf x_i}{\sum_{i=1}^nK({\mathbf x-\mathbf x_i\over h})}
$$

We can use it as iterative rule until convergence. The algorithm runs in $\mathcal O(n^2T)$ where $T$ is the number of iterations.

## DENCLUE vs DBSCAN

DBSCAN corresponds to a discrete kernel, more efficient bit more rough. $\epsilon,MinPts$ corresponds $h,\xi$.

DENCLUE uses Gaussian kernel density based attractors, which is smooth model and more able to capture underlying data distribution than DBSCAN.

## Pros and Cons

:thumbsup: Advantages

- Clusters can arbitrary shape and size, not necessarily be convex hull.
- Number of clusters is determined automatically.
- Can separate clusters from surrounding noise.
- Can be supported by spatial index structures.

:thumbsdown:Disadvantages

- Input parameters may be difficult to determine
- Can be sensitive to input parameter setting

# Clustering Evaluation

## External Measures

Take criteria into account that are not part of the clustering data.

### Contingency Table

A $r\times k$ matrix $\mathbf N$ s.t. (cluster $C$, ground-truth clustering $T$)

$$
\mathbf N_{ij}=|C_i\cap T_j|
$$

Computing the contingency table takes $\mathcal O(nrk)$

### Purity

To what extent a cluster $C_i$ contains points **only** from one ground truth cluster.

$$
\text{purity}_i=\frac{1}{|C_i|}\max_{j=1,\cdots,k}\{n_{ij}\}
$$

The purity of a clustering is the weighted sum of the purity of each cluster.

$$
\text{purity}=\sum_i\text{purity}_i
$$

### Maximum Matching

Consider a 1-to-1 assignment that maximizes the overall overlap between ground-truth clusters and computed clusters.

### F-measure

precision and recall

### Conditional Entropy

## Internal Measures

Based only on clustering data

Silhouette coefficient

Davies-Bouldin index

...