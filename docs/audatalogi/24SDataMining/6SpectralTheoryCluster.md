---
title: 数据挖掘 6 Spectral Theory and Clustering
author: tinsir888
date: 2024/3/4
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: 788ade8c
---

This module introduces **graph mining**.

Data points from networks, graphs, of connections.

Purpose: extract patterns from graphs.

3 different areas of graph mining:

1. Community detection: finding groups of nodes with similar characteristics.
2. Link analysis: understanding which node is more important than others.
3. Embedding: capturing similarities among nodes.



Graph $G=(V,E)$: a set of vertices $V$ connected through edges $E\subseteq V\times V$.

undirected & directed graph



Graph representations. A graph can be represented by adjacency lists or an adjacency matrix.

Degree of a node.

Graph construction:

- KNN graph: connect every point $\mathbb x$ with its closest $k$ points.
- $\epsilon$-neighborhood: connect all points $\mathbb x$ with the points within $\epsilon$ distance.
- Fully-connected graph: choose a distance $dist$, then connect any point to any other point. The weight of each edge $(i,j)$ corresponds to $1-dist$ between $i$ and $j$.

# The Adjacency Matrix

Consider undirected graphs. Their adjacency matrix is symmetric.

By multiplying a vector $\mathbf x\in\mathbb R^n$ with a adjacency matrix.

$\mathbf x$ can be thought as the vector form of a function $f:V\rightarrow\mathbb R$.

$$
\mathbf{Ax}=\mathbf{y}\\
$$

$$
\Rightarrow
\left( \begin{array}{cc}a_{1,1} & \cdots & a_{1,n}\\
\vdots & \ddots & \vdots\\
a_{n,1} & \cdots& a_{n,n} \end{array} \right)
\left({\begin{array}{cc}x_1\\ \vdots\\ x_n\end{array}}\right)
=
\left({\begin{array}{cc}y_1\\ \vdots\\ y_n\end{array}}\right)
$$

The vector $\mathbf y$'s $i$-th element is $y_i=\sum_{(i,j)\in E}x_j$. Therefore, the entry $y_i$ is the sum of labels $x_j$ of neighbors of $i$. This means that $\mathbf A$ is a matrix that transforms the value of a node as a sum of the value of the neighbors. If we repeat this aggregation multiple times we obtain a propagation of the initial labels $\mathbf x$ over the network $\mathbf x^{(t)}=\mathbf{Ax}^{(t-1)}$

There exists a fixed-point in this iteration:

$$
\exists\lambda,\mathbf{Ax}=\lambda\mathbf{x}
$$

We want to find an eigenvector $\mathbf x$ and eigenvalue $\lambda$ for the matrix $\mathbf A$.

# Spectral Graph Theory

Analyzing properties of the graph by inspecting the eigenvalues and eigenvectors of matrices that represent the graph structure.

Given a graph $G$ and a matrix $\mathbf M$ that describes the graph's structure, the **graph spectrum** is the set of $\mathbf M$'s eigenvalues $\Lambda=\{\lambda_1,\cdots,\lambda_n\}$ sorted in descending order $\lambda_1\ge\lambda\ge\cdots\ge\lambda_n$

$\lambda\in\mathbb C^1$ is an eigenvalue if there exists a vector $\mathbf x\in\mathbb C^n,\mathbf x\neq0$ s.t. $\mathbf{Ax}=\lambda\mathbf x\Leftrightarrow(\mathbf A-\lambda\mathbf I)\mathbf x=0\Rightarrow\mathbf\det(A-\lambda\mathbf I)=0$

For symmetric matrices. $\mathbf A$ is symmetric, This means $a_{j,i}=a_{i,j}\forall i,j$. Thus,

- All eigenvalues are positive $\lambda\ge0$
- The matrix is positive semi-definite, $\mathbf x^\top\mathbf{Ax}\ge0\forall\mathbf x$.
- $\exists$ matrix $\mathbf N$, s.t. $\mathbf A=\mathbf N^\top\mathbf N$

- All eigenvalues are real numbers, all eigenvectors are orthogonal ($\mathbf u^\top\mathbf v=0$).

In a $d$-regular graph, that $\mathbf 1$ is an eigenvector. That means all the eigenvectors $\mathbf u$ should be $\mathbf 1^\top\mathbf u=0\Rightarrow\sum_iu_i=0$. This is a very important fact that will be used later.

> A graph is $d$-regular, if the degree for every vertex is $d$.

## Graph Matrices

The **degree matrix** denoted $\mathbf \Delta$ is an $n\times n$ diagonal matrix where $\mathbf\Delta=\sum_ja_{i,j}$ is the degree of node $i$.

**Unnormalized Laplacian Matrix** or $L$ is an $n\times n$ matrix. $L=\mathbf\Delta-\mathbf A$

**Normalized Symmetric Laplacian** or $L^{sym}=\mathbf\Delta^{-1/2}L\mathbf\Delta^{-1/2}=\mathbf I-\Delta^{-1/2}\mathbf{A\Delta}^{-1/2}$ is the normalized version of $L$.

**Asymmetric (Random Walk) Laplacian** or $L^{rw}=\mathbf I-\mathbf\Delta^{-1}\mathbf A$.

Laplacian's properties: It has a trivial eigenvector $\mathbf 1$ corresponding with the eigenvalue $0$ since $L\mathbf 1=0$. Moreover, Laplacian is symmetric and positive semi-definite, all the eigenvalues are non-negative real numbers and the eigenvectors are real and orthogonal.
$$
\mathbf x^\top L\mathbf x=\sum_{(i,j)\in E}(x_i-x_j)^2
$$

# Spectral Clustering

A cut is a set of edges with only one vertex in a group
$$
W(A,B)=\sum_{i\in A,j\in B}a_{i,j}
$$
If we directly minimize the cut we might end up in unfortunate solution in which a partition contains only one node. To avoid this, we can minimize a **normalized cut**
$$
J_{rc}(C)=\frac{W(A,B)}{|A|}+\frac{W(A,B)}{|B|}
$$
Where $C=\{A,B\}$. Computing optimal normalized cuts is NP-hard.

Another approach to write the cut is considering a variable $x_i$ for each vertex $i$, s.t.
$$
x_i=\begin{cases}
+1\ \ (i\in A)\\
-1\ \ (i\in B)
\end{cases}
$$
Thus $W(A,B)=\sum_{(i,j)\in E}|x_i-x_j|$.

So if we want to minimize the cut we can just minimize the sum of absolute differences among $\mathbf x$ entries. It's more convenient instead  to minimize $\sum_{i,j\in E}(x_i-x_j)^2$. However, as we have seen this corresponds to $\mathbf x^\top L\mathbf x$.

Similarly, we can see that
$$
\frac{\mathbf x^\top L\mathbf x}{\mathbf x^\top\mathbf x}
$$
corresponds to a normalized version of the cut and is called the **Rayleigh quotient**. （瑞利商）

## Minimum-cut Relates to Spectral Properties

Variantional characterization of eigenvalues. Consider a matrix $\mathbf M$ real and symmetric. The eigenvalues are sorted in decreasing order. The variational characterization of the eigenvalues states that states that the smallest eigenvalue corresponds to
$$
\lambda_n=\min_{\mathbf x\neq 0}\frac{\mathbf x^\top\mathbf{Mx}}{\mathbf x^\top\mathbf x}=\min_{\mathbf x\neq0}\frac{\sum_{i,j}m_{i,j}x_ix_j}{\sum_ix_i^2}
$$
where $\mathbf x_n$ is the eigenvector corresponding to the eigenvalue $\lambda_n$. Similarily, the second smallest eigenvector $\lambda_{n-1}$ is the solution
$$
\lambda_{n-1}=\min_{\mathbf x\neq0,\mathbf x^\top\mathbf x_1=0}\frac{\mathbf x^\top\mathbf{Mx}}{\mathbf x^\top\mathbf x}
$$
And so on with all the eigenvalues. Finally, $\lambda_1$
$$
\lambda_1=\max_{\mathbf x\neq0}\frac{\mathbf x^\top\mathbf{Mx}}{\mathbf x^\top\mathbf x}
$$
Minimum cut as eigenvalues of the Laplacian.

- All eigenvectors a symmetric matrix are orthogonal and that the Laplacian matrix for an undirected graph is a symmetric matrix.
- The eigenvalue that minimizes $\lambda_n$ is the eigenvalue $0$ that corresponds to the eigenvector $\mathbf 1$ of the graph's Laplacian $L$. This corresponds to placing all nodes into a single partition.
- The second smallest $\lambda_{n-1}$ corresponds to an eigenvector $\mathbf x$, which is orthogonal to the eigenvector $\mathbf 1$, i.e. $\sum_ix_i=0$.
- The eigenvector is called the **Fiedler's vector** and minimizes a relaxed version of the normalized cut.

## Spectral Clustering Algorithm

Spectral clustering works in 3 phases:

1. Preprocessing: Construct a matrix representation of the graph.
2. Decomposition: compute the eigenvalues and eigenvectors of the matrix. Map each point to a lower-dimensional representation based on one or more eigenvectors.
3. Grouping: Assign points to two or more clusters, based on the new representation.

The *Spectral Partitioning Algorithm* finds a partition of the graph that is close to the optimal partition. In the decomposition phase, Spectral clustering maps vertices to corresponding components of  $\lambda_{n-1}$ and its vector. In the grouping phase, we sort the components of the new 1-dimensional vector and split then nodes in positive and negative or using the median value.

## $k$-way Spectral Clustering

We extend spectral clustering to the case of finding $k$-partitions.

One strategy is to recursively split the graphs into two partitions, until $k$ partitions are returned.

Another strategy leverages multiple eigenvectors besides the one corresponding to the second smallest eigenvalue. By stacking on the eigenvectors each node is embedded into a multi-dimensional space. Finally, we run $k$-means in this new space to find $k$-partitions.

Advantages of using multiple eigenvectors:

- Approximates the optimal cut
- Emphasizes cohesive clusters by increasing the unevenness in the distribution in the data.
- Provides a well-separated space. It transforms data to a new embedded space consisting of $k$ orthogonal basis vectors.
- Multiple eigenvectors prevent instability caused by information loss.

### Pros and Cons

:thumbsup:

- Clusters can have arbitrary shapes and size and are not restricted to convex shapes.
- Efficient in normal space graphs.
- Robust to noise is theoretically grounded and well-connected to graph properties.

:thumbsdown:

- Inefficient on dense graph - $O(n^3)$ worst case.
- Need to provide number of clusters $k$.