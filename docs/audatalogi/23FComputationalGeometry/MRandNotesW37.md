---
title: 计算几何 W37 Sorting and Searching in Parallel
author: tinsir888
date: 2023/9/12
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: 973ad492
---

# 阅读材料

## prefix sums and their application

by Guy E. Blelloch, CMU

parallel tasks: building blocks, and merge.

dynamic programming & divide and conquer

extended from sequential algorithms

all-prefix-sums operation on PRAM

### intro

prefix sum operation has many extension: quick-sort, lexical analysis, tree operations...

primitive instruction in some machine（原子操作）

define: scan operation is an array all-prefix-sums operation.

### implementation

array length n, EREW PRAM, time complexity: O(n/p+log p)

binary tree, for each depth: each father node first passes its left child value to right child.

if n>p, 采用分块的方式, time complexity: O(n/p+log p)

up sweep: sum[v]=sum[L[v]]+sum[R[v]]

down sweep: prescan[L[v]]=prescan[v], prescan[R[v]]=sum[L[v]]+prescan[v]

after completing down sweep, each vertex of the tree contains the sum of all the leaf value that precede it.

### applications

line-of-sight and radix-sort

recurrence equations 递归方程的计算

segmented scans

allocation processors

# 课堂笔记

## More application of prefix sums

- minimum
- broadcast
- partition

## Sorting

### Quicksort

O(n) work, O(log n) depth, EREW PRAM

randomized, sub-optimal depth, optimal work

### Merge Sort

```pseudocode
procedure BASICMS(I,S)
	split I into 2 equal part I_l and I_r of size n/2
	split S into 2 equal part S_l and S_r of size n/2
	for each I_l and I_r in parallel do
		(I_l,S_l)=BASICMS(I_l,S_l)
		(I_r,S_r)=BASICMS(I_r,S_r)
	sequentially merge S_l and S_r into I
	return (S,I)
```

- O(n) depth
- optimal work O(n log n)
- Theoretically bad
- probably lower constants compared to fully parallel
- with p processor we get $\frac{W}{p}+T=\frac{n\log n}{p}+n$

对 merge 操作并行化

假设这里有 2 个排好序的数组 A B，合并为一个数组

假设数组 B 中的一个数字 bi，它在合并后的数组的位置为 i+rank(B[i],A)，rank 用二分查找找到。

```pseudocode
procedure SEGMERGE(A,B,M,p)
	allocate array R[0,...,p]
	R[0]=0
	for each i=1...p in parallel do
		R[i]=rank(A,B[i*n/p])
	for each i=1..p in parallel do
		asymmerge(B[(i-1)*n/p+1],...,A[R[i-1],R[i]])
```

asymmetric merge

- break large trunk into small piece

so in the parallel merge sort we replace sequentially merge into SEGMERGE.

# Searching

input: A sorted array with n elements

query: a value v

output: the predecessor of v

The brutal force:

```pseudocode
procedure BF(A[1...n],v)
	r=0
	for i=0...n in parallel do
		if A[i]<=v<A[i+1] then
			r=i
	return r
```

how to do parallel?

## In CREW

parallel based on brutal force method:

```pseudocode
procedure PARALLEL-BF(A[1...n],l,h,v)
	if h-l<=p then
		return BF(A[1...n],v)
	for i=1...p in parallel do
		B[i]=A[l+(i-1)*(h-l)/p,l+r*(h-l)/p]
	r=BF(B,v)
	return PARALLEL-BF(A,(r-1)*(h-l)/p,l+r*(h-l)/p,v)
```

$Q(n)=O(1)$ $n\le p$

$Q(n)=Q(n/p)+O(1)$ $n>p$

$Q(n)=O(\log_pn)$

## In EREW

divide into p subsets of size n/p

Binary search in parallel

$T(n)=\log(n/p)=\log(n)-\log(p)$

- both results optimal
- CREW provably stronger than EREW
- for a natural problem

## Lower Bounds of PRAM

basic of lower bounds

goal:

- showing that a problem is difficult
- worst-case lower bound
  - existence of at least one difficult input

## CREW Lower bounds

### OR Problem

n boolean values 0 or 1

output: boolean or of the values

O(1) solution in common CRCW

a lower bound will also separate the weakest CRCW from CREW

- CREW provably weaker than CRCW on a natural problem

$O(\log n)$ seems impossible, even if there is at most one value of 1

### Building lower bounds

assuming

- processors have infinite registers
- processors have infinite computational power
- processors can compute arbitrary functions of their register

a computational model for lower bounds

### from PRAM to circuits

- we have a depth i circuit
- magic CPU gates
- arbitrary fan-out
- 2 fan-in

## Separations

deep connections between complexity theory, circuits and PRAM algorithms

switching lemma from complexity theory

XOR problem: compute the XOR of n bits

- $O(1)$ in sum-CRCW PRAM
- $\Omega(\frac{\log n}{\log\log n})$ Lower bound in priority/common-CRCW PRAM

OR problem

- $O(1)$ time in common-CRCW
- $\Omega(\log n)$ in CREW

Broadcast: send v to all processors

- $O(1)$ time in CREW
- $\Omega(\log n)$ in EREW

Searching: given n numbers in increasing order find predecessor of v

- $O(\log_pn)$ in CREW
- $\Omega(\log\frac{n}{p})$ in EREW

## A Searching Lower bound in EREW

searching: given n numbers in increasing order, find predecessor of 0 in 000...000111...111

proof idea:

- every change should change the output
- every change should change some memory locations
- every change should affect some processor
- at least one processor affected by $\ge\frac{n}{p}$ changes
- $\log\frac{n}{p}$ lower bound

tight!

- divide into disjoint $\frac{n}{p}$ sub-arrays
- Binary search in each

## Complexity and Related Areas

PRAM models have close ties to *Complexity theory*.

- CRCW PRAM simulated using arbitrary fan out circuits.
- CRCW can compute arbitrary boolean functions in $O(1)$ time (potentially exponentially number of processors)

