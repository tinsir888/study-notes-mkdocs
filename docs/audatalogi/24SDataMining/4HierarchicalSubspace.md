---
title: 数据挖掘 4 Hierarchical and Subspace Clustering
author: tinsir888
date: 2024/2/19
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: e78fa7f8
---

When data has high dimensions or clusters have different densities, methods like DBSCAN and $k$-means might fail.

**Hierarchical clustering** partitions the space in progressively smaller clusters.

- Hierarchical agglomerative clustering 层次凝聚聚类:single-link, complete-link and average-link strategies to merge clusters and form larger clusters.
- OPTICS clustering that order density reachable points to find cluster structures
- BRICH that summarizes the data to understand similarities

**Subspace** finds different clusters in different subsets of the dimensions in which the data lies.

- CLIQUE: A clustering algorithm which divides the space into grids and exploit the apriori principle on density to compute dense subspaces
- SUBCLU: Similar to CLIQUE but uses DBSCAN
- PROCLUS: A projected clustering which finds the best dimensions for each clusters.

# Agglomerative Hierarchical Clustering

**Dendrogram**: one of the main visualization tools for hierarchical clustering.

- A tree, in which the root is a cluster containing all the points in the dataset $\mathcal  D$ and the leaves are clusters of single points.
- Can be constructed **bottom-up (agglomerative)** by progressively merge smaller clusters into large one or **top-down (divisive)** by splitting larger clusters.
- Height of a branch in the dendrogram show the distance.
- Cut-off value as a vertical line identifies a partition of the data points.

Agglomerative hierarchical clustering takes a **bottom-up** approach.

- Small clusters are progressively merged to form larger clusters.
- Require the choice of a distance function.

## Pseudo-code of Agglomerative Hierarchical Clustering

Input: dataset $\mathcal D$, clustering distance $dist$

- $t\leftarrow1$

- $\mathcal C^t\leftarrow\{\{\mathbb x\}_{\mathbb x\in\mathcal D}\}$

- while $\mathcal C^t\neq\mathcal D$ do

  - for each $C_i,C_j\in\mathcal C^t$ do
    - Compute $dist(C_i,C_j)$

  // merge the clusters with the minimum distance

  - $C_i,C_j\leftarrow\arg\min_{C_i,C_j\in\mathcal C^t}dist(C_i,C_j)$

  // Create a new level in the dendrogram

  - $\mathcal C^{t+1}\leftarrow(\mathcal C^t\diagdown C_i)\diagdown C_j\cup C_j\cup(C_i\cup C_j)$
  - $t\leftarrow t+1$

- return $\mathcal C$

## Distance Among Clusters

The choice of distance metric determines the quality of the clustering

- Single-link distance $dist_{sl}(C_i,C_j)=\min_{\mathbb x\in C_i,\mathbb y\in C_j}dist(\mathbb x,\mathbb y)$: compute the minimum distance among two clusters
- Complete-link distance $dist_{cl}(C_i,C_j)=\min_{\mathbb x\in C_i,\mathbb y\in C_j}dist(\mathbb x,\mathbb y)$: compute the maximum distance among two clusters
- Average-link distance $dist_{al}(C_i,C_j)={1\over|C_i||C_j|}dist(\mathbb x,\mathbb y)$: compute the average distance among two clusters

# OPTICS

DBSCAN performs poorly when the clusters have different densities.

OPTICS abbr. for *Ordering Points To Identify the Clustering Structure*, solves the problem.

DBSCAN have fixed value of $\epsilon$, a point might have a very large $\epsilon$-neighborhood $N_\epsilon(\mathbb x)$. Observation: the possibility to define the minimum distance under which a point is a core point. This distance is called the core distance $cdist$.

**Core distance**: the smallest distance s.t. $\mathbb x$ is a core point

$$
cdist(\mathbb x)=\begin{cases}
MinPts-th\text{ smallest distance }dist(\mathbb x,\mathbb y)\text{ if }|N_\epsilon(\mathbb x)|\ge MinPts
\\?\text{ otherwise}
\end{cases}
$$

**Reachability distance**: the distance of the closest point reachable to $\mathbb x$.

$$
rdist(\mathbb x)=\begin{cases}
dist(\mathbb x,\mathbb y)\text{ if }dist(\mathbb x,\mathbb y)\gt cdist(\mathbb y)\\
cdist(\mathbb y)\text{ if }dist(\mathbb x,\mathbb y)\lt cdist(\mathbb y)\\
?\text{ if }dist(\mathbb x,\mathbb y)\gt\epsilon
\end{cases}
$$

OPTICS is built upon the idea of core distance and reachability distance.

- OPTICS maintains a basic data structure that stores the shortest reachability-distance seen so far
- OPTICS visits each point once and moves to the next point based on the smallest reachability distance
- OPTICS outputs the order of points, the core and the reachability distance of all points in a *reachability plot*.

## Pseudo Code of OPTICS

- for each $\mathbb x\in\mathcal D$ do

  - if $\mathbb x$ is not processed then

    - insert $(\mathbb x,?)$ into $CL$

  - while $CL\neq\emptyset$ do

    - select first element $(\mathbb x,rdist)\in CL$

    - retrieve $N_\epsilon(\mathbb x)$ and $cdist\leftarrow cdist(\mathbb x)$

    - $\mathbb x.processed\leftarrow True$

    - Write $(\mathbb x,rdist,cdist)$ to file

    - if $\mathbb x$ is a core object at distance $dist\le\epsilon$ then

      - for each $\mathbb p\in N_\epsilon(\mathbb x)$ do

        - if $\mathbb p.processed=$ false then

          $rdist_p=rdist(\mathbb p,\mathbb x)$

          if $(\mathbb p,\_)\not\in CL$ then

          - insert $(\mathbb p,rdist_p)$ into $CL$

          else if $(\mathbb p,old\_rdist)\in CL$ and $rdist_p\lt old\_rdist$ then

          - update $(p,rdist_p)$ in $CL$

- return $CL$

**Finding a density based clustering** w.r.t. $\epsilon^*\le\epsilon$ and $MinPts$ is easy:

- start with $\mathbb x$ with $cdist(\mathbb x)\le\epsilon^*$ and $rdist(\mathbb x,\_)\gt\epsilon^*$
- continue while $rdist(\mathbb x,\_)\le\epsilon^*$

**Running time.** OPTICS running time is bounded by the running time of DBSCAN, $\Omega(n^{4/3})$. OPTICS only maintains additional data structures, that do not affect the overall time complexity.

**Analysis of reachability plot.** The reachability plot shows both reachability and the core distance. Contiguous areas in the plot with large reachability and core-distance indicate regions with low density. At the same time, low reachability-distance indicates high-density areas. A jump in the plot occurs when the next point in order falls outside the current cluster.

OPTICS is relatively insensitive to parameter settings, and results are goods if the parameter are large enough.

## Pros and Cons

:thumbsup:

- Doesn't require the number of clusters to be known in advance.
- Very robust
- Computation complete hierarchically
- Good visualization
- A flat partition can be derived, e.g. dendrogram or reachability plot

:thumbsdown:

- No backtracking, greedy splits and merges
- May not scale well. It examines many objects in order to split and merge. Runtime for standard methods is $O(n\log n)$, OPTICS without indexing is $O(n^2)$

# BRICH

Balanced Iterative Reducing and Clustering using Hierarchies.

It first summarizes the data and then clusters the summarized data with any clustering algorithm.

Can cluster huge datasets that do not fit into memory, with only very small loss in accuracy.

Incremental algorithm for accommodating new data points.

2 main phases of BRICH

1. (Summarize) Scan the dataset $\mathcal D$ to build an in-memory Clustering Feature tree. CF-tree is a multi-level compression that preserves inherent clustering structure,
2. Use an arbitrary clustering algorithm to cluster the leaf nodes of the CF-tree.

BRICH scales linearly $O(n)$ in the size of the dataset. To improve quality, BRICH can optionally scan the data multiple times.

Fast, but can only deal with numerical data.

## Basic Idea of BRICH

- Constructs a partitioning of the data into micro-clusters using an efficient index-like structure
- The micro-clusters are representations of multiple points described by Clustering Features
- CFs are organized hierarchically in a balanced tree
- A standard clustering algorithm is then applied to the leaf entries of the CF-tree.

## Clustering Features

A clustering features $CF=(N,LS,SS)$ of a micro-cluster $C\subseteq D$ consists of

- $N=|C|$: Number of points in the micro-cluster
- $LS=\sum_{\mathbb x\in C}\mathbb x$: Linear sum of the data points in $C$
- $SS=\sum_{\mathbb x\in C}\mathbb x_i^2$: Square sum of the data points in $C$

Note that both $LS$ and $SS$ are vectors. The CF representation, although quite simple, is enough to compute a number of statistics about a cluster, such as centroids, measures of compactness, and distance measures of clusters.

**Additivity Theorem**: CFs satisfy additivity theorem that allows CFs to be computed incrementally on unions of micro-clusters. If two clusters are merged the CF of the new cluster is simply the element-wise sum of the respective CFs.

## CF-tree

The CF-tree is an index similar to a B+ tree that contains the CF representations of the micro-cluster.

The tree-structure can be easily updated without scanning the entire data, by virtue of a nice data organization.

A CF-tree with parameters $B,L,T$ is a tree-like structure, s.t.

- An internal node contains at most $B$ entries $[CF_i,child_i]$
- A leaf node contains at most $L$ entries $[CF_i]$
- The diameter or radius of all entries in a leaf node is at most $T$ according to a user-defined distance metric
- Leaf nodes are connected via `prev` and `nxt` pointers

The tree is constructed bottom-up in the following manner:

- Transform point $\mathbb x$ into CF vector $CF_x=(1,\mathbb x,\mathbb x^2)$
- Insert $\mathbb x$ in the same way as in a B+ tree.
- If threshold $T$ is violated by insertion, then split the leaf node and reorganize tree, analog to that of B+ tree.

## The BRICH Algorithm

1. Scan all data points and build in memory CF tree using given amount of memory and recycling space

2. (optional) Condense into desirable length by building a smaller CF tree.

3. Global clustering with any clustering algorithm on the CF features vectors.

4. (optional) refine with more passes

## Pros and Cons

:thumbsup:

- Compression factor can be tuned to the available memory
- Efficient construction of a micro-clustering $O(n)$
- Good clustering result for the partitioning iterative refine clustering algorithm such as $k$-means and $k$-medoid when applied to only the leaf nodes of a CF tree.

:thumbsdown:

- Only for data from a euclidean vector space
- Handles only numeric data
- Sensitive to the order of data records
- Entries are limited by disk page size
- Different parameters to tune

# Subspace Clustering

Problem with high dimensional data: *curse of dimensionality*

> A general phenomenon that occurs on points as the number of dimensions increases:
>
> - Sparsification: the data points grow further apart with dimension
> - Incomparability: the probability that points fall into a sphere of a certain radius becomes negligible, thus the distances of points from a center or another points become closer with the dimension.

A cluster in low dimension might not appear as such in higher dimension.

*Subspace clustering* attempts to tackle this problem by clustering the points in different subsets of the dimensions. Intuitively, in 3D it is like projecting the points on some of the coordinates.

The selection of the coordinates where to project is the main difference between the subspace clustering algorithms.

Another major difference is the clustering algorithm employed in the subspaces.

## CLIQUE

One of the first algorithms for subspace clustering.

Density-based: divide the space into $n/h$ cubic regions, prune regions that have less than a certain number of points $\xi$ (density threshold) in low dimension and repeat the process on subspaces obtained by aggregating dimensions that contains at least one cluster.

**The naive approach** computes clustering for subsets of dimensions. However this approach considers $O(2^d)$ clusters, which is prohibitive.

CLIQUE exploit the **monotonicity of density** for which a dense cluster in dimension $d$ is dense in every subset of the $d$ dimensions. A cluster is the union of dense regions. Therefore, we can construct a **greedy algorithm** based on pruning regions that are not dense.

The idea starts with an initial empty set of dimensions and iteratively include dimensions that are not pruned in the previous step.

The greedy algorithm in CLIQUE first finds 1-dimensional $R_1$ candidate regions, then generate 2-dimensional $R_2$ from the 1-dimensional candidates, prune the candidates using the monotonicity of density, and repeat the process increasing the dimension until no candidate is generated.

### Pseudo Code of CLIQUE

Input: dataset $\mathcal D$, regions size $h$, density threshold $\xi$

- $R_1\leftarrow$ Candidate regions in 1-dimension

- $d\leftarrow2$

- repeat

  - $R_d\leftarrow$ Generate all candidate $d$-dimensional cells from $R_{d-1}$

  // Prune cells with fewer than $\xi$ points

  - $R_d\leftarrow\{R:|R|\ge\xi\}$
  - $d\leftarrow d+1$

- until $R_d\neq\emptyset$

- $\mathcal C\leftarrow$ Compute clusters from each $R_i$

- Summarize cluster

// In the last two steps, CLIQUE computes the cluster from the dense regions and summarizes them.

**Compute clusters** from the union of adjacent candidate regions. Compute a graph for each candidate subspace in dimension $d$. The graph has a node for each dense region and an edge if two regions are adjacent. The connected components of the graph represent the clusters. This steps takes $O(2dn)$ for each candidate subspace.

**Summarizes cluster** with a minimal number of inequalities, by defining boundaries on the regions. Since each region is represented by a number of dimensions, its boundaries are values of the dimensions that describe the region's hypercube.

### Pros and Cons

:thumbsup:

- Automatic detection of subspaces with clusters
- Automatic detection of clusters
- No assumptions about the distribution of data
- Insensitivity to the order of the data
- Good scalability w.r.t. to the number $n$ of data points

:thumbsdown:

- Accuracy of result depends on the number of partitions $h/n$
- Requires heuristics to avoid constructing all subspaces

## SUBCLU

CLIQUE might miss clusters due an early pruning of the regions that cross the clusters.

SUBCLU remedies to CLIQUE's deficiency by using DBSCAN as the basis to find dense clusters in a subspace. Without entering into details the definition of core-objects, density reachability and connectivity can easily adapt to subspaces. Similarly, SUBCLU exploits the **monotonicity of density connectivity**:

- If $\mathbb x,\mathbb y$ are density-connected in $d$-dimensional space, all their projections in $k-1$ are also density-connected.

The algorithm iteratively applies DBSCAN at each step to generate $d$-dimensional clusters as in CLIQUE

### Pros and Cons

:thumbsup:

- Automatic detection of subspaces containing clusters
- Automatic detection of clusters
- No assumptions on data distributions (density-based approach)

:thumbsdown:

- Parameter setting highly affects clustering results
- Inherits the drawback of DBSCAN, such as the sensitivity w.r.t. clusters with different densities.
- At least $O(n^2)$ time. Typically worse depending on max-dimensionality of the subspace containing clusters.

# Projected Clustering

It tackles the curse of dimensionality differently from subspace clustering.

Main idea: To project each point individually into the **only** subspace in which the point is clustered.

The goal of projected clustering is correctly identify the right projections, s.t. each point is assigned exactly to one cluster. Projected clustering avoids rediscovering the clusters in multiple subspaces but at the same time, might miss clusters.

## PROCLUS

A representative method for projected clustering.

Top-down approach that iteratively refines clusters and projections starting from an initial clustering of the data in the full dimension.

PROCLUS uses $k$-medoid algorithm to find clusters.

**Projection cluster**: a set of objects $C$ and a set of dimension $D$, s.t. the objects in $C$ are closely clustered in the subspace defined $D$.

**Closely clustered**: A cluster $C$ is closely clustered if it is compact under the Manhattan segmental distance

$$
d(\mathbb x,\mathbb y)={1\over|D_C|}\sum_{i\in D_C}|x_i-y_i|
$$

that measures the Manhattan distance of the objects in the projected dimensions $D$ of cluster $C$.

PROCLUS requires the specification of the number $k$ of clusters and the average number $\hat d$ of dimensions. PROCLUS follows a multi-step approach in three phases

1. **Initialization**: selects the initial set of medoids

   Select a sample of representative data points from the entire dataset, where the sample should ideally characterize the entire dataset and should contain at least $k$ medoids. In practice, since finding such an ideal a sample is hard, PROCLUS greedily selects $A\cdot k$ well-separated points.

2. **Iterative**: refines the medoids and computes the projections.

   Main body of PROCLUS that determines the best set of dimensions for a set of candidate medoids

   - Choose random set of $k$-medoids from representative points
   - Determine optimal set of dimensions for each medoid
   - Assign all objects to the nearest medoid
   - Update current clustering if it is better than the previous until termination

   The iteration continues from step 2. By replacing bad medoids with random medoids from the sample of representatives until the clusters do not change anymore.

   First issue: how to determine an optimal set of dimensions for each medoid?

   > PROCLUS proposes a score $Z_{ij}$ that measures the reduction in dispersion of the cluster $C_i$ on dimension $j$ as opposed to the full dimensionality $d$.

   For each medoid $\mathbb m_i$ calculate the average distance $X_{ij}$ on dimension $j$ of objects in the hyper-sphere $\mathcal L_i$ with radius the distance from $\mathbb m_i$ to its nearest medoid. The dispersion of cluster $i$ is the average distance $X_{ij}$ for all the dimensions $Y_i={1\over d}\sum_{j=1}^dX_{ij}$. The deviation $X_{ij}-Y_i$ is negative if the clusters on dimension $j$ are correlated to those in the full dimension

   The score $Z_{ij}$ is normalized deviation $X_{ij}-Y_i$: $Z_{ij}={1\over\sigma_i}(X_{ij}-Y_i)$ where $\sigma_i$ is the empirical standard deviation of $X$ in cluster $i$.
   
   $\sigma_i=\left({{\frac{1}{d-1}}\sum_{j=1}^d(X_{ij}-Y_i)^2}\right)^{1/2}$

   Small values indicate better dimensions to find clusters. The score $Z$ allows to assign each cluster to the best dimensions. PROCLUS assigns greedily each cluster to $k(\hat d-1)$ dimension and computes the set of projected dimensions $D_i$ for cluster $i$.

   After determining the set of dimensions $D_1,\cdots,D_k$ for each medoid $\mathbb m_1,\cdots,\mathbb m_k$, PROCLUS assigns the points to the nearest medoid using the Manhattan segmental distance.

   Finally, the algorithm updates the current set of medoids by computing the spread around the centroids in each dimension. A measure of dispersion is similar to $Z_{ij}$ is used to refine the clusters.

   By substituting uninformative clusters with a random representative from the initial sample. A medoid is substituted if the cluster it represents has the smallest number of points among the rest or if the number of points is below a threshold.

3. **Refinement**: improves the quality of the clustering

   This phase improves the clustering. First it determines the optimal dimension for each medoid, using a procedure similar to that of the iterative phase, but substituting $\mathcal L_i$ with the clusters $C_i$. Second, each point is assigned to the closest cluster $C_i$. A point is an outlier if its segmental distance to each medoid exceeds the radius of the medoids sphere of influence.

### Pros and Cons

:thumbsup:

- Based on full high-dimensional space
- Simple but efficient projected clustering

:thumbsdown:

- Relies on cluster-based locality assumption: subspace of each cluster is learned from local neighborhood of its medoid
- Forces projected clusters to have convex shape
- Medoids are chosen only from initial sample set
- Highly heuristic and is sensitive to a large number of input parameters $(k,l,A,min\_deviation)$

