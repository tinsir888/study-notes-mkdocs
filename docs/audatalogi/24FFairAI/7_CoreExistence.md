---
title: Fair AI/ML 7 The Existence of Core for Max Distance Loss
author: tinsir888
date: 2024/11/22
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
abbrlink: '198e9682'
---

# The Existence of Core

Is the core empty?

In the paper, we have proved that Greedy Capture computes $2$-core in $O(kn)$ time for maximum distance loss.

:question:Is the $2$ the lower bound for code?

# Special Case: Binary Distance Value

Consider a graph $G=(V,E)$, where for every pair $i,j\in V$, $(i,j)\in E$ if $dist(i,j)=1$, else $dist(i,j)=2$.

If there exist a graph such that no matter how we assign the clusters, there always exist a clique of size at least $\lceil n/k\rceil$, then the lower bound of $2$ is proved.

## Split&Merge Algorithm

In any given aforementioned graph $G$, the cluster in the core always exist. (But this can not prove the actual core always exist.)

Consider the algorithm below:

Algorithm Split-and-Merge:

Input: Graph $G=(V,E)$, $k$

Output: Clustering $C=(C_1,\cdots,C_k)$ in the actual core

1. $n\gets\#V,C\gets\empty,X\gets\empty$
2. while $X\neq V$, do // SPLIT PHASE
   - $C'\gets MAXCLIQUE(V\diagdown X,E)$
   - $C\gets C\cup\{C'\}$
   - $X\gets X\cup C'$
3. while $|C|\gt k$, do // MERGE PHASE
   - $c_1\gets\arg\min_{c\in C}|c|,c_2\gets\arg\min_{c\in C\diagdown\{c_1\}}|c|,c'=c_1\cup c_2$
   - $C\gets C\diagdown\{c_1,c_2\},C\gets C\cup \{c'\}$
4. return $C$

## Analysis of Split&Merge

It's straightforward to see that Split&Merge is a greedy algorithm.

Since Split&Merge uses $MAXCLIQUE$ as an oracle, it is a NP solution.

:dart:Theorem: Split-and-Merge always return the cluster in the actual core.

> Claim: If a set of points form a cluster, which means, the cost of everybody is $1$. All of them have no incentive to form a coalition with anyone outside of the clique.
>
> Thus before merging, any subset of $V$ that intersects at least two item-sets in $C$, can not be a clique.
>
> :heavy_exclamation_mark:By pigeonhole principle, before merging, any item-set in $C$ with size already greater than $\lceil n/k\rceil$ would not be picked in the merge phase! All data points in these clusters have no incentive to deviate.
>
> In the merge phase, every point who would have been merged have the cost $2$. But they can not find other points to form a clique with size at least $\lceil n/k\rceil$.
>
> **If there exist a clique with size at least $\lceil n/k\rceil$ in the merge phase, by pigeonhole principle and the property of maximum clique, it should be chosen in the split phase.** Which leads to a contradiction.

:thinking:Can we extend Split&Merge to more general cases?

# Special Case: Trinary Distance Value

Consider a group of points $N$, for every pair of points, their distance can only be $1,\sqrt2,2$. (Now matter how the distances are, they always subject to triangle inequality)

If there exists an instance, where no matter how we assign $k$ clusters, the approximation of core cannot be better than $\sqrt2$, then we prove that the bound for inapproximability of core is $\sqrt2$.

~~It seems that we can extend Split&Merge in this case to produce clustering in the core.~~

## Extend Split&Merge for Trinary Distance Value

The extended version of Split&Merge:

Input: $k$ and the distance between each pair of points

1. construct a graph $G=(V,E)$, where $V$ corresponds to the set of data points. For a pair $i,j\in V$, there is an edge $(i,j)\in E$ if the distance between $i$ and $j$ is $1$.
2. $X\gets V,C\gets\empty$
3. $c\gets MAXCLIQUE(X,E)$
4. while $|c|\ge\lceil n/k\rceil$ do // find the clique of distance $1$ with large enough size
   - $C\gets C\cup\{c\}$
   - $X\gets X\diagdown c$
   - $c\gets MAXCLIQUE(X,E)$
5. construct a graph $G'=(X,E)$, where $X$ corresponds to the free data points. For a pair $i,j\in V$, there is an edge $(i,j)\in E$ if the distance between $i$ and $j$ is either $1$ or $\sqrt2$
6. $C'\gets$Split&Merge$(G',k-|C|)$ // run Split&Merge on remaining points
7. $C\gets C\cup C'$
8. return $C$

## Analysis Split&Merge for Trinary Case

For simplicity, we introduce the notation of *core-violator*: A **core-violator** is a subset $S\subseteq N$ with size $\ge\lceil n/k\rceil$, where each point in $S$ has a strictly better cost if they are clustered together.

:thinking:Claim: extended version of Split&Merge always return a clustering in the core.

> Assume that Split&Merge returns a clustering not in the core. Which means there exist a core-violator $S$.
>
> - Since the minimal distance is $1$ and the cost of each points in the clique in $C$ is exactly $1$ before Split&Merge, core-violator can't exist there.
>
> - The only possible existence of core-violator is in the Split&Merge step.
>   - Claim: It is not possible that the a free point at step 5 can collude with any point in the clique formed in step 4.
>   - The only possible core-violator can formed only among free points at step 5.
>   - ❗️By the property of step 4, any subset of free points at step 5 can not form a core-violator **with distance $1$ between any pair**. Otherwise they they should be clustered in step 4.
>     - Thus, the possible core-violator must contain a free point improving cost from $2$ to $\sqrt2$, meaning that there exist a large enough clique in $G'$ after the merge phase.
>     - By pigeonhole principle, this contradicts the property of split phase (This large enough clique must be chosen in split phase).
>
> Thus the extended version of Split&Merge guarantees the core property.

:question:Can we further extend Split&Merge to general cases where there are $n(n-1)/2$ distances?

~~The answer is YES if I can successfully extend splir&merge to general cases~~

# Extend Split&Merge to General Cases

For $n$ data points in a metric space, given number of clusters $k$.

We construct the complete weighted undirected graph $G=(V,E)$ where $V$ corresponds to the set of data points, for each pair $(i,j)\in E$, the weight is the distance between $i$ and $j$. There are $n(n-1)/2$ edges in $G$.

First, we sort the edges by weight.

Start from another graph with $V$ and no edge. We iteratively add an edge from the scratch in the ascending order.

- At each time, we check whether there exist a clique of size at least $\lceil n/k\rceil$.
  - If so, we immediately include them into a cluster and delete them temporarily.

:thinking:Intuitively I think this extension outputs a cluster in the core. But I haven't well organized a formal proof.