---
title: 计算几何 W36 Introduction to Parallel Computation
author: tinsir888
date: 2023/9/5
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: 6cee78e5
---

# 阅读材料

## Parallel Algorithms

by Guy E. Blelloch and Bruce M. Maggs

Selected topics from (Sec. 1 and 2.1)

### Modeling parallel computations

sequential algorithm: a single processor connected to a memory system (RAM)

parallel computations: more complicated - requires understanding of the various models and the relationship among them

two classes of parallel models

- multiprocessor

  - n processors, shared memory

  - three types multiprocessor models:

    1. local memory machine model

       each processor can access its own local memory directly, but can access the memory in another processor only by sending a memory request through the network.

    2. modular memory machine model

       processor accesses the memory in a memory module by sending a memory request through the network.

    3. PRAM (parallel random-access machine) model

       a processor can access any word of memory in a single step. *these accesses can occur in parallel.*

       controversial: no real machine lives up to its ideal of unit-tie access to shared memory

  - network topology

    1. bus
    2. 2-dimensional mesh
    3. hypercube
    4. 2-level multistage network
    5. fat-tree

    routing capabilities: latency and bandwidth

  - primitive operation

    1. instruction that perform concurrent accesses to the same shared memory location
    2. instructions for synchronization
    3. instructions that perform global operations on data

- work-depth model

  alternative to focusing on machine, focusing on the algorithm

  cost of algorithm: total number of operations it performs & dependencies among those operations.

  work W, depth D, parallelism P: $\mathcal P=\frac{W}{D}$

  three classes of work-depth model:

  1. circuit models

     node (represents basic operation)

     arcs (provide inputs to other node)

     acyclic (无环的)

  2. vector machine models

     algorithm is expressed as a sequence of steps, each of which performs an operation on a vector of input values, and produces a vector result.

     work: sum of the work of its steps

     depth: number of vector steps

  3. language-based models

     work: calling 2 func in parallel = sum of the work of the 2 calls

     depth: equal to maximum of the depth of the 2 calls

assigning costs to algorithms: roughly work = time*num of processors, slightly additional cost than same sequential computer.

### Parallel algorithmic techniques

divide-and-conquer 分治

even divide-and-conquer algorithms that were designed for sequential machines typically have some inherent parallelism.

example: merge-sort algorithm

（后面的内容不用看了）

# 课堂笔记

## Motivation and History

1967 Amdahl's law

SIMD, MIMD, vector processing etc

motivation

- some tasks are naturally parallel
- multiple computational units in one chip
- possibility of using special hardware GPU, TPU
- possibility of have multiple machines

## PRAM: Intro

PRAM aka Parallel Random Access Machine

simpler than other models

components:

- a shared memory
- p processors
  - i-th processor has an ID
  - different, they can run different codes
- synchronized

time complexity

- O(1) memory access time

- O(1) computation time
- tot: O(1)

## PRAM: Variants

exclusive read, exclusive write (EREW) PRAM

- no concurrent accesses by multiple processors at any time step

concurrent read, exclusive write (CREW) PRAM

- only concurrent accesses for reading are allowed

concurrent read, concurrent write (CRCW) PRAM

- concurrent access for both reading and writing are allowed
- ambiguous

CRCW PRAM variants:

- Common-CRCW PRAM
  - concurrent accesses must write the same value
- Arbitrary-CRCW PRAM
  - one processor succeeds, don't know which one
- Priority-CRCW PRAM
  - processor with the smallest ID succeeds
- min-CRCW, sum-CRCW, OR-CRCW, XOR-CRCW PRAM
  - the values of concurrent accesses are combined using a predetermined combining operation and the result is written

## PRAM example

given an array A of size n, find the smallest value.

RAM:

```
min = +inf
for i=1 to n do
	if a[i]<min then
		min=a[i]
```

Trivial O(n) solution

min-CRCW PRAM:

```
for i = 1 to n in parallel do
	min=a[i]
```

trivial O(1) solution (n processors)

common-CRCW PRAM (all processor must write the same thing)

- use $n^2$ processors
- $C_{i,j}$ for every pair of A[i] and A[j]
  - $C_{i,j}$ reads and compares A[i] and A[j]
  - writes the results in cell $M_{i,j}$ of matrix M
  - find which row of M is all 0 (只要有一行有 1 的值，那么该行对应的数肯定不是最小值)

EREW PRAM? Divide and conquer (recursion)

Synchronization

- processors wait for all to be done

## Other Models

### Fork-join variant

n threads

- start with 1
- thread t can spawn 2 child threads c1 and c2
- can wait for children to finish

can simulate PRAM with +O(log n) overhead

assume atomic operations

concurrency control via mutexes

### Other models

- BSP
- OpenMP
- Open MPI
- Massively Parallel: lots of processors connected
- GPU
- MapReduce
- Distributed computing ......

## Work Depth and Optimality

n: input size

p: number of processors

T(n, p): parallel runtime

work: T(n, p) * p

sequential time: T(n, 1)

depth aka span: T(n, ∞)

optimality

- work matches best sequential
- depth matches best PRAM (with p=∞)

### Example

comparison-based sorting

- $\Omega(n\log n)$ lower bound
- $T(n,p)\times p=\Omega(n\log n)$
- cannot sort in $O(\log n)$ time with $\infty$ processors

## Brent's Scheduling Principle

Theorem: Any PRAM algorithm with work $W(n)$ and span $T(n)$ ban be implemented on a p-processor PRAM to run in time $T_P(n)=O(\frac{W(n)}{P}+T(n))$.

## Implications

difficulty of parallelism

- keeping the same work
- reducing the depth

example

- $O(\log n)$ time sorting with $O(n\log n)$ work.
- implies $O(n)$ processors

  ## Fundamental Problems in PRAM

### Prefix Sums

input: Array $I$ of size $n$. We have n processors. Compute all the prefix sums.

sequential $O(n)$ time

but parallel $O(1)$ is wrong.

Correct parallel method: recursion depth: $O(\log n)$ work $O(n\log n)$

unified approach:

1. split into pairs

2. add and join adjacent pairs

3. recurse

4. unjoined

   depth $O(\log n)$

   optimal sequential work of $O(n)$

```pseudocode
procedure PREFIX-SUMS(a[1...n])
	if n<=1 then return
	for i=1 to n/2 in parallel do
		b[i]=a[2*i-1]+a[2*i]
	PREFIX-SUMS(b[1...n/2])
	for i=1 to n/2 in parallel do
		a[2*i-1=b[i]-a[2*i]
		a[2*i]=b[i]
```

$O(\frac{n}{p}+\log n)$



Many fundamental problems are solved with

- recursion
- basic divide and conquer

prefix sums: solve many fundamental, RAM trivial problems

- array compaction
- merging
- partition
- sorting