---
title: APS 重点复习课程之计算机组成原理
author: tinsir888
date: 2022/10/19
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 计算机组成原理
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: e4e96e41
---

# Overview

有一部分和计算机体系结构重合的知识点（cache 结构、流水线等内容），放在体系结构那复习。

Von Neumann Machine & Harvard Machine

- Von Neumann: ALU
- Harvard Machine: CU

Bus System

- On-chip Bus
- System Bus
- Communication Bus

Synchronous & Asynchronous Communication

- Synchronous: control by a uniform time scale
- Asynchronous: lock, half-lock, full-lock

Storage

- The Structure of Memory System
- Cache Hit

Refresh of RAM

- Centralized Refresher
- Decentralized Refresher
- Asynchronous Refresher

Hamming Code

- 1 3 5 7
- 23 67
- 4567

I/O Device Communication

- Procedure Query
- Interrupt
- DMA

Instruction Set

# Definition

The attributes used to implement specific computer architectures such as memory system, bus structure and internal CPU,

# Characteristics of Two Computer Organization

## Von Neumann Machine

- Must have a memory, a control unit, an ALU and I/O devices.
- Only one memory for both instruction and data.
- aka Princeton Architecture

## Harvard Machine

- two memory, two bus. one for instructions, the other for data.
- reduce memory visit cost while running.（减轻程序运行时的访存瓶颈）
- basically solve conflict between data-fetch and instruction-fetch

# System Bus

## Definition

The set of conductor carrying data and control signals within a computer system, to which pieces of equipment may be connected.

## Different Kinds of Bus

### On-chip Bus

Inner a chip, such as the bus between two registers.

### System Bus

(inner a computer system)

Data Bus

Address Bus

Control Bus

### Communication Bus

Communication bus is used as communication between computer systems or between computer systems and other systems

### One Structure of Bus

CPU

- linked to I/O Bus. CPU can access the info of all I/O interface.
- linked main memory bus. exchange of data and control signals between CPU & memory.

Memory

- linked to DMA(Direct memory access) bus with I/O interface.
- some high-speed I/O device can directly exchange info with main memory without participation of CPU.

# Synchronous & Asynchronous Communication

## Synchronous Communication

Both sides of the communication of data transmission are controlled by a uniform time scale.

## Asynchronous Communication

non-locking, half-locking & full-locking

不互锁（单机），半互锁（多机），全互锁（网络通信）

### Non-locking

Sender send request, no need to wait for answer.

### Half-locking

Sender send request, wait for the answer.

Receiver no need to know sender's finish, automatically delete its answer.

### Full-locking

Sender send request, wait for the answer.

Receiver send answer, after knowing sender's finish, delete it answer.

# Storage

## The hierarchical structure of memorizer

"Pyramid shape"

CPU(10 ns)→cache(20 ns)→main memory(200 ns)→additional memory(ms)

the closer to CPU, the faster speed is and the more expensive price.

## The access of cache

1. CPU start to access cache
2. hit? if yes, access cache send message to CPU. If no, goto 3.
3. access main memory and send msg to CPU.
4. Cache is full? if yes, execute the replacement algorithm to swap block into cache.
5. transfer new block in main memory into cache.

# Refresh of RAM

- Centralized Refresher: Set a refresher period, the entire memory cell relocates in a centralized period of time
- Decentralized Refresher: Memory cell of each row can be created into every access period
- Asynchronous Refresher: The combination of Centralized Refresher and Decentralized Refresher; refresh 128 rows in every 2 ms.

# Hamming Code

n bits binary code need k check digit: $2^k\ge n+k+1$

check digits' position: 1, 2, 4, 8...

check digit on 1st position check: 3(001**1**), 5(010**1**), 7(011**1**), 9(100**1**), 11(101**1**)......

check digit on 2nd position check: 3(00**1**1), 6(01**1**0), 7(01**1**1), 10(10**1**0)......

even-check(usually) or odd-check: all the digit for each check digit have even or odd number of "1".

# I/O Device Communication

## Procedures Query

In this method CPU and I/O devices work **serially**. CPU check the state of I/O device until it's ready then transfer data or info. During this period the CPU is busy waiting for the respond of I/O and don't do anything else.

## Program Interrupt

In this method CPU and I/O devices work in **parallel** and when the I/O devices are ready, they sends a interrupt signal to CPU then CPU deal with the prepared data or info.

Example: keyboard input.

## DMA(Direct Memory Access)

In this method, there is a direct bus between I/O devices and main memory and don't have to interrupt the running program. The transfer of data and the running of non-memory access program can work in parallel. When CPU is accessing memory, the I/O devices wait until CPU free the control of memory.

# Instruction Set

Consist of operation code and address code, reflects the operation and address.

## RISC

Reduced instruction set computer.

implement complex instruction with combination of simple instructions.

fixed length of instruction, few types of instructions

But instruction density is low.

z.B. ARM, RISCV, MIPS...

## CISC

Complex instruction set computer.

Instruction density is higher than RISC's.

More complex hardware for complex instructions.

z.B. x86...

## Addressing Method

- immediate addressing: addr is immediate number.
- register addressing: addr refers to a register
- direct addressing: addr refers to address of operand.
- base-plus-index addressing: addr means basic register+index register.
- scaled-index addressing
- relative addressing: addr means PC + offset

