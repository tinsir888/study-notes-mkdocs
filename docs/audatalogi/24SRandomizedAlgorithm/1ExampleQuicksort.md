---
title: 随机算法 1 Examples of Randomized Algorithm
author: tinsir888
date: 2024/1/29
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: f202fd8f
---

# Course Info

3 Project - 30% (A group of 3 people)

Oral Exam - 70%

Prof. Ioannis Caragiannis and Prof. Kasper Green Larsen

# Randomization

Randomization algorithms use random coins, dice, card shuffling etc.

## Assumptions

Fair coins $\Pr[HEADS]=\Pr[TAILS]=\frac{1}{@}$

More complicated operations

- Random selection among a finite set of items
- Access to a random permutation of elements
- Selection of a random point in the interval $[0,1]$
- Selection from a finite or infinite set according to a non-uniform probability distribution

**We don't care about implementation issues in this course:heavy_exclamation_mark:**

## Main Characteristics

Deterministic algorithms: same step in any execution on the same input.

Randomized algorithms

- different outputs in different executions
- running time may not always the same

## Why randomization

1. Simple
2. Well on average or with high probability
3. Insights to the design of better deterministic algorithms **derandomization**

# Examples of Randomized Algorithm

## Content resolution

2-nodes communication, collision happens when both send message at the same time.

Protocol make sure message will be sent correctly

If use fixed waiting time. the collision will happen again and again.

Solution: use a random period of time.

## Max Cut

Given a graph, partition the node into 2 disjoint set, trying to maximize on-cut edges between nodes at different subsets.

Max Cut: NP-Complete

### Polynomial approximation

examine the nodes in an arbitrary order

for each node, decide its side in the bipartition greedily

### Much simpler algorithm

for each node, decide its side in the bipartition randomly

- Each edge is in the cut with probability $1\over2$
- On average, half of the edges will be in the cut

$$
\mathbb E[C]=\mathbb E[\sum_{e\in E}X_e]=\sum\Pr[X_e=1]=\frac{|E|}{2}\ge\frac{OPT}{2}
$$

half-optimal on average, with high probability

second best known approximation

Best known approximation: semidefinite programming 0.878

# Quicksort

## Main idea

Recursively call a fast subroutine for partial sorting

1. select a pivot element
2. reorganize around the pivot

subroutine takes $O(n)$ steps

pivot element is in the final position

sorting is reduced to two smaller sorting problem

## What's the problem

If the input is inversed, picking the first (also largest) as pivot, it will need $\Theta(n^2)$ time.

## How to improve

Selecting better pivot!

Make median-number pivot?
$$
T(n)=2\cdot T(\frac{n}{2})+T_{partition}(n)+T_{median}(n)\\
T_{partition}(n)=O(n)
$$
There exists linear-time algorithm for median, thus
$$
T(n)=2\cdot T(\frac{n}{2})+O(n)\\
\Rightarrow T(n)=O(n\log n)
$$

## How good is Quicksort so far

Best possible running time

low space

but complicated

# Randomized Quicksort

Randomly choose pivot

On average, random pivot gives 25% - 75% split
$$
T(n)=T(3n/4)+T(n/4)+O(n)\\
\Rightarrow T(n)=O(n\log n)
$$

## Analysis of Random Quicksort

Expected number of comparisons in the calls of partition

Typically, the uniform selection of a pivot among $n$ elements can be implemented with $O(\log n)$ coin tosses, on average

So the time for selecting pivots is at most $O(n\log n)$

## Notation

$z_i$ denotes $i$-th smallest element

$i<j,X_{ij}$ as the r.v. denoting the number of times the element $z_i$ and $z_j$ get compared by Quicksort

$X_{ij}$ is 1 if $z_i$ and $z_j$ both belong to the same subarray when some of them is selected as pivot, otherwise it is 0.

Then the # of comparisons in the calls of partition is
$$
C=\sum_{i=1}^{n-1}\sum_{j=i+1}^nX_{ij}
$$
*No good sorting algorithm should perform the same comparison twice.*

## Bounding the expectation of $C$

$$
\mathbb E[C]=\mathbb E[\sum_{i=1}^{n-1}\sum_{j=i+1}^nX_{ij}]=\sum_{i=1}^{n-1}\sum_{j=i+1}^n\mathbb E[X_{ij}]=\sum_{i=1}^{n-1}\sum_{j=i+1}^n\Pr[X_{ij}=1]
$$

What is $\Pr[X_{ij}=1]$?

- $\Pr[X_{1n}=1]=\frac{2}{n}$
  1. $z_1$ selected as pivot. $\Pr=\frac{1}{n}$
  2. $z_n$ selected as pivot. $\Pr=\frac{1}{n}$
  3. neither selected, they will put into different subarrays, will never be compared.

- $\Pr[X_{ij}=1]=\frac{2}{j-i+1}$

Thus
$$
\mathbb E[C]=\sum_{i=1}^{n-1}\sum_{j=i+1}^n\frac{2}{j-i+1}=\sum_{i=1}^{n-1}\sum_{k=2}^{n-i+1}\frac{2}{k}\le2\sum_{i=1}^{n-1}H_n\le2nH_n
$$
i.e., $\mathbb E[C]=O(n\log n)$ ($H_n$ is harmonic number $\ln(n+1)\le H_n\le1+\ln n$)





# Reference Literature

## Lecture notes by Nick Harvey from UBC

What are randomized algorithms?

> :x: average-case analysis
>
> :white_check_mark: worst-case analysis
>
> May either run for long time, or output wrong answer. Possible to make these cases very tiny likelihood.

Why RA?

> Compared with deterministic algorithm, RA are often:
>
> - simpler
> - more efficient (in space or time)
> - in many scenarios: provably better
> - in many scenarios: RA can do something DA can't do.
> - ideas lead to DA design.

Example: Max Cut

> Want to find an polynomial-time algorithm for which $|\delta(G)|\ge\alpha OPT$.
>
> If we use random algorithm, randomly split the vertices into 2 sides, then $\mathbb E[|\delta(G|]\ge\frac{1}{2}OPT$.

## Lecture notes by Manuel Blum from CMU

Probabilistic Analysis and Randomized Quicksort

Basic-Quicksort: if every round the pivot choose unfortunately the maximum, the running time is $\Theta(n^2)$.

Randomized-Quicksort: The worst-case expected-time bound is $O(n\log n)$ by randomly choose the pivot.

Theorem: The expected number of comparisons made by randomized quicksort on an array of size $n$ is at most $2n\ln n$.