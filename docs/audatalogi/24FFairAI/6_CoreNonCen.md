---
title: Fair AI/ML 6 Some Open Problems about Non-centroid Clustering
author: tinsir888
date: 2024/11/8
cover: /img/AUFairAI.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
  - 人工智能
categories:
  - AUDatalogi
  - 算法博弈论
  - AI/ML 公平性
abbrlink: 3e85f345
---

In this post, we focus on upper and lower bound of core where non-clusterings are.

Let the loss (or cost) function for every data points be maximum distance between any other points and itself:
$$
\ell_i(S)=\max_{j\in S}d(i,j).
$$
We have known that a clustering in the exact core must exist given $N,k$.

For the approximation of core: The Greedy Capture algorithm computes the upper bound of $2$ in polynomial time. And it have the lower bound of $1$.

**What about the gap between 1 and 2???**

There are some open problems and I am trying to answer.

# Complexity for Auditing the Core

:question:What is the time complexity for verifying whether a given clustering is in the exact core?

!!! danger

    TL;DR It is a **NP-hard** problem.



The analysis of verifying problem:

We need to check if any subset $S\subseteq V$ with $|S|\ge\lceil n/k\rceil$ can form a cluster such that the maximum distance between any two points in $S$ is less than each agent's maximum distance to others in its current cluster. If such a subset exists, then the clustering violates the core property.

Consider a reduction from another NP-complete problem $MAXCLIQUE$. The problem asks if there is a clique of a specified size in an undirected graph $G=(V,E)$.

**Problem Setup**: We have an undirected graph $G = (V, E)$ with $n$ vertices. We need to decide if a given clustering is in the core, with each data point's cost being the maximum distance between itself and all other points in the same cluster.

**Constructing the Graph**: To reduce $MAXCLIQUE$ to our core-checking problem, we will build a graph with the properties:

- For every edge $(i,j)\in E$, the distance $d(i, j)$ is less than
  1. the maximum distance between $i$ and all other points in $i$'s cluster
  2. the maximum distance between $j$ and all other points in $j$'s cluster.

**Formulating the Core Condition in Terms of $MAXCLIQUE$**: If a cluster is in the core, no subset $S$ of data points, with $|S| \geq\lceil\frac{n}{k}\rceil$, can improve their maximum distance cost by clustering together. If we interpret each cluster as requiring a subset of nodes that are all pairwise connected (thus forming a clique), we can state the core problem as follows:

- Determine if there exists a subset of nodes of size at least $\lceil\frac{n}{k}\rceil$ such that every pair of nodes in it are connected.
- This requires identifying the largest clique in $G$ and checking if it can be at least as large as $\lceil\frac{n}{k}\rceil$, which directly maps to finding the maximum clique.

**Verifying Core Membership**: To verify if a clustering is in the core using this construction, we would:

- Check if there exists a subset $S \subseteq V$ with $|S| \geq \lceil\frac{n}{k}\rceil$ such that all nodes in $S$ have edges to each other (i.e., a clique).
- If such a subset $S$ exists, it would imply that these nodes could deviate by forming their own cluster with a smaller maximum distance, violating the core property.
- Thus, solving this for all clusters to determine if the clustering as a whole satisfies the core property would require us to solve a clique-finding problem.

**Completing the NP-Hardness Reduction**: Since finding a maximum clique is NP-hard, and since verifying the core property requires solving the maximum clique problem to check for the presence of sufficiently large cliques in each cluster, the problem of deciding if a clustering is in the core is **NP-hard**.

# Complexity for Computing Core

:question:What is the complexity for computing a clustering in the exact core?

I don't have a clear idea yet.

But it maybe harder than $NP$. Maybe $P^{NP}$, $NP^{NP}$ or $PSPACE$?

# Complexity for Computing α-Core

:question:What is the complexity for computing a clustering in $\alpha$-core ($1\lt\alpha\lt2$)?

Is there exist polynomial algorithm?