---
title: 算法和计算复杂度 13 Random Access Machine
author: tinsir888
date: 2024/11/1
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 6c19248d
---

More precise model

Problem of integer multiplication.

Registers can contain arbitrary integers. Consider repeated squaring to number $2$. After just $n$ multiplications, the resulting number is $2^{2^n}$, a doubly exponential large number in $n$. This requires exponential number of bits.

A RAM has an infinite sequence of registers each able to contain an arbitrary non-negative integer.

- Machine is controlled by a program consisting of a list of instructions.
- Registers can be added or subtracted.
- Contents of a register can be stored or retrieved in a second register by means of indirect addressing using a third register.
- Execution can be controlled by conditional jumps in the list of instructions.

The cost for using a register containing the integer $n$ is given by a cost function $\ell(n)$.

- Having $\ell(n)=1$ gives the unit-cost RAM
- Having $\ell(n)=\lceil\log_2(n+1)\rceil$ gives the logarithmic-cost RAM.

Don't include multiplication as a basic operation in this model.

The RAM and the Turing machine models of computation can simulate each other with only a logarithmic factor.



The word RAM model refines the integer RAM by bounding the magnitude of the numbers that can be stored in a single register, and having unit cost operations on these.

This is formally done by introducing a parameter $w$, called the **word-size**, and having each register can store an element of $\{0,1\}^w$.

The word size $w$ will increase with the length of the input $n$, and in particular we have $w=\Omega(\log n)$ to be able to perform indirectly random access to the entire input.



The basic set of instructions, gives **restricted word RAM**, consists of addition, subtraction, left and right shifts, and bit-wise AND, OR, NOT.

A common extension is to include integer multiplication as a unit cost operation.

Another extension is exclude integer multiplication but allow all functions computable by $AC^0$ circuits of polynomial size in $w$, thus giving the $AC^0$ **word RAM**.



The typical setting of the word size, known as the trans-dichotomous setting, is to have $w=\Theta(\log n)$.

Some Algorithms requires a larger word size.

A notable example is the task of sorting $n$ integers. When $w=\Omega(\log^{2+\epsilon}n)$ a randomized algorithm using expected time $O(n)$ exists.

The best known algorithm that works for all word sizes, uses $O(n\sqrt{\log\log n})$ time.