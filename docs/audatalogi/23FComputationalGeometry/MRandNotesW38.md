---
title: 计算几何 W38 I/O Model and Permutation Lower Bound
author: tinsir888
date: 2023/9/21
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: 2dfb57d1
---

# 课前预习

## Random Access Machine Model

standard theoretical model of computation

- infinite memory
- uniform access cost

simple model crucial for success of computer industry

### Hierarchical Memory

modern machines have complicated memory hierarchy

- levels get larger and slower further away from CPU
- data moved between levels using large blocks

many issues influence performance

- cache misses, TLB misses, branch mis-predictions etc.

Disk access is $10^6$ slower than main memory access

important to store/access data to take advantage of blocks

### Scalability problems

most programs developed in RAM-model

- run on large dataset because OS moves blocks as needed

modern OS utilize sophisticated paging and pre-fetching strategies

- but if program makes scattered accesses even good OS can't take advantage of block access

## I/O Model

$N$ num of items in the problem instance

$B$ num of items per disk block

$M$ num of items that fit in main memory

$T$ num of items in output

I/O: move block between memory and disk

**assume that $M\gt B^2$**

### Fundamental bounds

|           | internal  |                  external                  |
| :-------: | :-------: | :----------------------------------------: |
| scanning  |    $N$    |               $\frac{N}{B}$                |
|  sorting  | $N\log N$ |     $\frac{N}{B}\log_{M/B}\frac{N}{B}$     |
| permuting |    $N$    | $\min(N,\frac{N}{B}\log_{M/B}\frac{N}{B})$ |
| searching | $\log_2N$ |                 $\log_bN$                  |

Note:

- Linear I/O $O(N/B)$
- Permuting not linear
- Permuting and sorting bounds are equal in all practical cases
- cannot sort optimally with search tree

Large difference between $N$ and $N/B$!!!

### Queue and Stacks

queue

- maintain push and pop blocks in main memory $O(1/B)$

stack

- maintain push and pop block in main memory $O(1/B)$

### Sorting

- $<M/B$ sorted lists and be merged in $O(N/B)$ I/O
- unsorted list can be distributed using $<M/B$ split elements in $O(N/B)$ I/O

Merge sort

- create $N/M$ memory sized sorted lists
- repeatedly merge lists together $\Theta(M/B)$ at a time

Multi-way quick-sort

- compute $\Theta(M/B)$ splitting elements
- distribute unsorted list into $\Theta(M/B)$ unsorted lists of equal size
- recursively split lists until fit in memory

### Computing splitting elements

in internal memory: linear time selection

selection algorithm: finding i-th element in sorted order

1. select median of every group of 5 elements
2. recursively select median of $N/5$ selected elements
3. distribute elements into 2 lists using computed median
4. recursively select in one of two lists.

analysis

- step 1 and 3 performed in $O(N/B)$ I/O
- step 4 recursion on at most $\sim\frac{7}{10}N$

$T(N)=O(N/B)+T(N/5)+T(7N/10)=O(N/B)$ I/O

后面的分析一点也看不懂，头大……

# 课堂笔记

## Intro and Motivation

2 trends:

- Parallelism
- Non-Uniform Memory Access, caches...

start with 2 level of memory

I/O Model

- slow but large mem
- fast but limited mem of size $M$
- block transfer of $O(B)$ elements at a time

block transfer is a common architectural phenomenon

I/O algorithms borrowed from PRAM

## I/O Model

aka external memory model

input size: $N$

memory size: $M$

blocked storage, block size: $B$

fixed cost to read/write one block

almost the same as pre-learning...

......

## Searching

store N items (values)

search for a key (value)

ordinary:

- $O(\log N)$ search

- $O(\log N+K)$ report values in an interval

static, blocking:

- $O(\log n)$ search
- $O(\log n+K/B)$ report values in an interval

### Dynamic Search Tree

like Splay tree.

rotation destroys blocking!

### B-Tree

(a,b)-tree (a>=2, b>=2*a-1)

- completely balanced
- leaves store between a and b values
- except root, degrees between a and b
- root degree between 2 and b
- ordered like a search tree

insertion

- search and insert in a leaf v
- if v not overflowing done!
- else split v

split

- split v into two
- add a key to parent of v
- recurse into parent of v

deletion

- search and delete from a leaf v
- if v not underflowing done!
- else re-balance b

### Buffer Trees

idea: to be lazy

