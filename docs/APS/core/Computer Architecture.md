---
title: APS 重点复习课程之计算机体系结构
author: tinsir888
date: 2022/10/24
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 计算机体系结构
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: a346d5bd
---

# Overview

Definition

Storage System

Evaluate

- Amdahl's Law
- MIPS
- MFLOPS
- Benchmarking

Parallel Technology

Pipeline

- IF ID EX MEM WB

Conflict Avoiding

# Definition

Refers to those attributes of a system visible to a programmer, or those attributes have direct impact on logical execution of program.

The relationship between computer architecture and computer organization:

> Computer organization is the logical implement of Computer architecture.
>
> A architecture can have many computer organization.

# Storage System

Hierarchical storage system:

1. Cache
2. Memory
3. Auxiliary Storage (Disk)

## Cache:warning:

### Locality principle of program access

Locality of time: the recently accessed data perhaps will be accessed very soon.

Locality of space: the neighbor of accessed data may be accessed together. 

### Associativity

Direct mapped cache

- each location in the main memory can go in only one entry in the cache.
- Let x be block number in cache, y be block number of memory, and n be number of blocks in cache, then mapping is done with the help of the equation x = y mod n.
- good best-case time, but unpredictable in the worst case

Fully associative cache

- any memory address can be stored in any location in the cache.
- flexible
- complex and difficult to implement, as they require additional hardware  to search the entire cache for the correct location to store or retrieve data.
- the best miss rates, but practical only for a small number of entries

N-way set associative cache: a combination of direct mapped cache and fully associative cache.

### Write policies

write-through

> write is done synchronously both to the cache and to the backing store.

write-back

> initially, writing is done only to the cache. The write to the backing  store is postponed until the modified content is about to be replaced by another cache block.

# Evaluate

## Amdahl's Law

The improvement of single part of the system contributes to the whole system has relation to the percentage of the improved part.

MIPS, aka Million Instructions Per Second,

MFLOPS, aka Million Floating-point Operations per Seconds

## Benchmarking

SPEC...

# Parallel Technology

Use parallel in instruction-level to improve the performance, such as pipeline technology.

**Instruction level parallelism**

- handle multiple instructions at the same time

**Task level parallelism**

- multiple tasks

**Job level parallelism**

- multiple jobs

**Parallel in multiple computers**

- loosely coupled system

  multiple computers share the same physical memory device such as disk.

- tightly coupled system

  multiple computers share the same memory.

# Pipeline

A repetitive process is decomposed into several sub-processes, and each sub-processes is realized by a special functional component.

Stagger multiple processes in time and pass through the functional segments in turn, so that each sub-process can be performed in parallel with other sub-processes.

## 5-stage Pipeline

IF ID EX MEM WB

Instruction Fetch

> Use the content in the PC as address to access the memorizer to fetch the stored instruction and store it in instruction register IR.

Instruction Decode

> Decode the instruction to get the requested operand in the instruction.

Execute

> load and store instruction
>
> ALU: calculate the fetched operand

Memory

> load and store instruction: read or store the data into the memorize unit
>
> the other instruction: do nothing.

Write Back

> ALU and load instruction write the result into common registers.

### Conflict

Data conflict

> The later instruction read the data before it's written into common register by the former instruction.
>
> `DADD R1, R2, R3`
>
> `DSUB R4, R1, R5`
>
> DSUB needs the result of DADD.

Structure conflict

> The amount of hardware resource can't satisfy the request of parallelism.

Control conflict

> jmp instruction

# Conflict Avoiding

Linear:

Dynamic Scoreboard Scheduling Algorithm

[Tomasulo's Algorithm](https://en.wikipedia.org/wiki/Tomasulo%27s_algorithm)

uses register renaming and a common data bus to avoid data hazards and reduce the need for stalling the pipeline.

dispatch in order, execute out-of-order, commit in order. 顺序发射，乱序执行，顺序提交。

Register renaming: reservation station, ReOrder Buffer (ROB)

具体的流程记不住，问到了就直接寄。