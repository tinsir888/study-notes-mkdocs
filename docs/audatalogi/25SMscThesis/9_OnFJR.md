---
title: Fair AI/ML 9 On Fully Justified Representation
author: tinsir888
date: 2025/4/3
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
abbrlink: 1b140fcb
---

A clustering $C$ satisfies Fully Justified Representation (FJR), if there doesn't exist a subset of points $V$, such that

-  $\vert V\vert\ge\lceil n/k\rceil$.
- $\forall i\in V:cost_i(V)\lt\min_{j\in V}cost_j(C(j))$.

It is proven that FJR is guaranteed to exist. An exact FJR solution can be found in exponential time by brute force algorithm.

The paper also mentioned that we can achieve the approximation of FJR in polynomial time by greedy capture algorithm with subroutine **smallest agent ball**. (For maximum cost is $2$, average cost is $4$).

:thinking:We are trying to push these lower bounds.

Let's start from some special scenarios.

# Metric Space (Arbitrary Distance)

Important quote from original paper:

> The most cohesive cluster: Given a set of agents $N$ and a threshold $\tau$, the **most cohesive cluster** problem asks to find a cluster $S\subseteq N$ of size at least $\tau$ such that the maximum loss of any $i\in S$ for $S$ is minimized, i.e., find $\arg\min_{S\subseteq N':|S|\ge\tau}\max_{i\in S}\ell_i(S)$.

This can be done in exponential time by brute force. By calling the most cohesive cluster as subroutine, the greedy capture are guaranteed to find the clustering solution satisfying FJR.

:question:Can we find FJR solution in **POLYNOMIAL** time?

Look back at smallest agent ball. It iteratively find the point with minimal **RADIUS** centered around that point that covers $\tau$ points.

What if we consider **DIAMETER** instead?

## FJR in Polynomial Time

**Greedy Capture with Diameter Growth**

**Diameter** is the maximum distance between a pair of points in the same cluster.

Similar to *centroid clustering*, we regard each point as a cluster center.

Iteratively, do among the remaining point:

- Start from $0$, slightly increase the diameter of the "ball" around each "cluster center".
- If a ball includes $\lceil n/k\rceil$ points, secure it as a cluster.

:thinking:It seems that **for the maximum cost**, this version output the exact FJR solution in polynomial time. Because the diameter growth version actually finds the most cohesive cluster. Because in this case, the maximum loss of a group of points is minimized.

### Pseudo code

Greedy Cohesive Clustering is omitted here. We focus on the subroutine `DiameterGrowth`

Input: (Remaining) subset of agents $N'\subseteq N$, metric $d$, threshold integer $\tau$.

Output: Cluster $S$.

1. If $|N'|\le\tau$ then return $S\gets N'$;
2. $\delta\gets0$ and $List\gets$ sorted pairwise distance (in ascending order); // initialize diameter
3. while there is no ball including $\tau$ points, do
   - $\delta\gets$ next value in $List$;
   - for $i\in N'$ do
     - $Ball_i\gets Ball_i\cup$ (all points have distance less than $\delta$ to any point in $Ball_i$)
4. return $S\gets$ the ball including $\tau$ points.

### Running Time Estimation

1. The number of distances is $O(n^2)$. So sorting of distances takes $O(n^2\log n)$ time.

2. We have at most $O(n^2)$ times to increase the diameter $\delta$.

   In each increasing round, we iterate through $O(n)$ balls. For each ball, the number of comparison is the size of the ball multiply the remaining point.

Obviously the subroutine runs in polynomial time.

The number of calling the subroutine is at most $k$ times.

Conclusion: Greedy Capture with Diameter Growth output the clustering in FJR in polynomial time.

# Binary Distance

:warning:In this setting, the "distance" may not necessarily submit to triangle inequality.

## Maximum Cost

:dart:Claim: For maximum cost and binary distance scenario, the FJR is equivalent to the core.

> It is not hard to see a clustering in the core implies FJR.
>
> Reverse implication also holds, because in the definition of FJR, the inequality of cost suggests that LHS to be $0$ and RHS to be $1$. Thus the FJR-violator should be a clique where every point has cost $1$ in their original clusters.
>
> This definition aligns with definition for the core.

:question:Can we find FJR (core) solution by polynomial time algorithm in this setting?

Polynomial time means we need to get rid of finding cliques. Some unconfirmed ideas:

1. Find the big "stars", and play with them.

## Average Cost

No idea so far.

