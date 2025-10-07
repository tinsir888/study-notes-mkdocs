---
title: APS 非重点复习课程之计算机系统设计
author: tinsir888
date: 2022/11/08
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 操作系统
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 7c2cf633
---

# How to store in computer?

## Two's Complement

0000 0000 is 0

0000 0001 is 1

0000 0010 is 2

0111 1110 is 126

1000 0000 is -128

1000 0001 is -127

1000 0010 is -126

1111 1110 is -2

1111 1111 is -1

> -x equals reverse all bits then +1.

## Floating Point

In IEEE 754,

32 bit:

1-bit s for +-

8-bit E for power

23-bit M for number after dot

> $+/-0.1M\times2^E$

## Character Set

ASCII 1 byte

UTF-8 1~4 byte

Unicode usually 2 byte.

# Instruction Set

CISC RISC

# Exception Control

## Reasons

- internal exception (page fault, privilege, divided by zero...)
- external interruption (ctrl+c, printer lack of paper...)
- context switch
- signal passed from a process to another

## Context Switch

- The state of the currently executing process must be saved so it can be restored when rescheduled for execution.
- The process state includes all the registers that the process may be using, especially the [program counter](https://en.wikipedia.org/wiki/Program_counter), plus any other operating system specific data that may be necessary. Usually stored in PCB or switchframe
- OS suspends execution of one process, switches context by choosing a process from the ready queue and restoring its PCB.

## Exception and Interruption

Internal

- Hard error: power shutoff, circuit error 
- Soft error: divide 0, illegal instruction, stack overflow, out of time, page fault...

External

- external clock, device ready...

When exception or interruption occurs, OS get into kernel to handle.

current instruction → OS get into Kernel to response to exception/interruption → return to current/next instruction or abort.

