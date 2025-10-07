---
title: APS 重点复习课程之操作系统
author: tinsir888
date: 2022/10/23
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 操作系统
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: b11e128
---

# Overview

Definition

- Resources Management

Process

- State
- PCB
- Process Communication
  - Message Passing
  - Shared Memory
- Process Scheduling
  - Priority
- Thread
  - User Thread
  - Kernel Thread
  - Thread Pool
- Deadlock
  - Solutions: resources allocation diagram
  - Preventions: apply and release concentrate

Memory Management

- Linking
  - Static Link
  - Dynamic Link
- Memory Allocation
  - Contiguous Allocation
    - Fixed Partition
    - Variable Partition: first fit / best fit
  - Pagination Allocation
- Page Table (Logical Addr to Physical Addr)
  - Page Fault
  - Page Replacement

Disk Space

- Continuous Allocation
- Linked Allocation
- Index Allocation

File System

- Tree Structure
- No-ring Graph Directory
- VFS

# Definition

- The essence of OS is a **program** running between computer user and the computer hardware.
- It provides **resource management services** that including both hardware resources and software resources.
- It also provides some convenient services for computer users such as user interaction and the environment to run applications.

What kind of OS are we using today?

> It's similar to Multi-programming System make sure that there is always a task running in the CPU to improve the utilization, which means there should be multiple tasks in the memory to be selected and executed.

# Process

## Conception

> Process is a running program, when a program is loaded into memory it turns to a process. Process consist of stack, heap, data and code section.

## Feature

> Concurrency, Independence, Asynchrony

## State

### FSM description:warning:

Can be described in a finite state machine

{% mermaid %}
graph TB
1([new])
2([ready])
3([running])
4([terminated])
5([waiting])
1 --admitted--> 2
3 --IO or event wait--> 5
5 --IO or event completion--> 2
3 --exit--> 4
2 --scheduler dispatch--> 3
3 --interrupt--> 2
{% endmermaid %}

- Process Control Block: Store info of Process (PID)
- Process Synchronization:
  - The critical section.
  - Semaphore (信号量): wait (var), signal (var)

### Process Communication

Message Passing

> When processes p and q want to passing message they should build communication link first, then they can send or receive messages to the port that built on the communication link.

Shared memory

> to ensure that the producer and the consumer can work in parallel we allocation a shared buffer to store the data produced by producer and the consumer read the buffer when it's necessary.

## Process Scheduling

### Method

Non-preemptive: 非抢占式

> Once CPU have be dispatched to a specific process it won't be allocated to another process until it come to the end.

Preemptive: 抢占式

> The process can be interrupted by another process when it's working in the CPU.

### Algorithm

- First-come First Served: dispatch in order

- Short job First: The shorter the job, the higher the priority.

- Priority-scheduling algorithm: scheduling according to priority.

- Time slice round-robin scheduling algorithm:warning:

  The CPU time is divided into time slices of the same size, and all process are placed in the queue in order. the process at the head of the scheduling queue executes a time slice. After the end of a time slice. if the process has not finished execution, it is inserted at the end of the queue.

## Dead Lock:warning:

when will deadlock happen?

1. a resources can only be occupied by one process.
2. a process have occupied one resource and request for another resource.
3. the resources is non-preemptive.
4. processes are occupying resource and waiting for each other release the other resource they need.

How to solve?

- Break the loops in Resource Allocation Graph
- Banker Algorithm

# Thread

Lightweight process. It's included in the process and is the actual operating unit in the process.

- Thread is a smaller working unit than process, a process can contain many threads and they will share the same memory space.
- Creating a process costs more than creating a thread because the former copy not only the stack and register but also the global variables and code, naturally we can't forget that context switch is also time-consuming.
- We can create different threads to deal with different tasks in parallel without to much extra efforts.

The thread can be divided into kernel-level thread and user-level thread.

- Kernel-level thread is scheduled by kernel while the user-level is scheduled by **thread library**.
- The operating of user-level thread is based on the kernel-level thread.

## Thread Pool

The essence of thread pool is that creating a certain amount of thread and this method have these advantages as follows:

1. Faster than always waiting for creating new threads.
2. Limits the number of accessible thread at any time then there won't be too much of threads be created.

# Memory Management

## Linking

Static Link

> merge the contents of the library into the binary image, which always stay in the memory.

Dynamic Link

> delay the link process to the moment of execution, a little process called stub will locate proper range in the memory for the library.
>
> The stub will check if the represented program is in the memory or not. If not, the stub will load the program into the memory. Then stub will use address of program to replace itself and run the program.

## Memory Allocation Mode

### Contiguous Allocation

Each process is located in a contiguous memory area.

Fixed partition allocation

Variable partition allocation

- Best fit
- First fit

Both these two methods are wasting of memory, external pieces and internal pieces.

### Pagination

Frame: divide the physical memory into fixed blocks and call it frame

Page: Similar to frame but it represents the logical memory.

One page corresponds to one frame and the frames don't need to be contiguous.

#### Logical Address:warning:

Page number (index of page table in the corresponded unit of page table is the physical address) + page offset.

*So the page offset part of logical address and physical address are the same.*

#### The Content of Page Table

Every entry of page table is the frame number that corresponds to the page number and the frame number can be not contiguous.

#### Page Fault

If the accessing page is not in the memory, a page fault happen, or it's illegal:

1. Find a free frame and load the needed page to this frame.
2. Reset the valid bit in the page table.
3. Restart the interrupted instruction.

#### Page Replacement

when there is no free frame during the page fault a page replacement will happen.

1. Use a replacement algorithm to find a sacrifice frame.
2. Replace the page on the sacrifice frame with the needed page.
3. Reset the page table
4. Reboot the process

#### Replacement Algorithm

1. FIFO

2. LRU (Least Recently Used):

   Use a stack to record all the pages in the memory, once a page is executed, put it on the top of the stack, another pages move down in order, the least recently used page is in the bottom of the stack.

3. Best Replacement Algorithm:

   Replace the page that will not be used for the longest time in the future. Basically impossible.

# File System

## Tree Structure Directory

Tree Structure Directory has two forms:

1. Absolute directory
2. Relative directory

## No-ring Graph Directory

In this structure, the directory can share files and directories

The same file or sub-directory can be contained in multiple directories.

The implement of sharing data and sub-directory

- hard link: copy
- symbolic link (soft link): a short cut

## VFS

aka virtual file system, separate the common operation of file system with the specific implement.

# Disk Space

## Continuous Allocation

Use the address of first block and the length of the file to define it structure: filename|start|length

## Link Allocation

The structure is: filename|start|end

every block have a pointer area to point at the next block

advantage: don't waste space

disadvantage: request more space.

## Index Allocation

Structure: filename|index block(a array of the addresses of block)