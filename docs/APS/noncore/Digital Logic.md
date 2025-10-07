---
title: APS 非重点复习课程之数字逻辑
author: tinsir888
date: 2022/11/02
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 数字电路
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 8a237dse2
---

# Number System

Decimal, binary, octal, hexadecimal (how to transfer)

# Logic Operation

and or not nand(与非) nor(或非) xor(异或)

# Boolean Function

$Y=A\overline B+\overline AB+\overline B\overline C+AC$

# Karnaugh Map

simplify the Boolean function.

| A\\BC |  00  |  01  |  11  |  10  |
| :---: | :--: | :--: | :--: | :--: |
|   0   |  1   |      |  1   |  1   |
|   1   |  1   |  1   |  1   |      |

Adjacent 2 '1' can be merged

result is not only.

both $\overline A\overline C+A\overline B+BC$ and $\overline AB+AC+\overline B\overline C$ are correct.

# SR Latch

aka Set-reset Latch. [detail](https://en.wikipedia.org/wiki/Flip-flop_(electronics)#Simple_set-reset_latches)

| $S_D$ | $R_D$ |  state(determined by Q)  |
| :---: | :---: | :----------------------: |
|   1   |   0   |            1             |
|   0   |   1   |            0             |
|   0   |   0   | keep the state unchanged |

# JK flip-flop

synchronous timing circuit. vending machines. [detail](https://en.wikipedia.org/wiki/Flip-flop_(electronics)#JK_flip-flop)

$Q_{next}=J\overline Q+\overline KQ$

Characteristic Table

|  J   |  K   |  Comment   | Qnext |
| :--: | :--: | :--------: | :---: |
|  0   |  0   | Hold state |   Q   |
|  0   |  1   |   Reset    |   0   |
|  1   |  0   |    Set     |   1   |
|  1   |  1   |   Toggle   |   Q   |

Excitation Table

|  Q   | Qnext |  Comment  |  J   |  K   |
| :--: | :---: | :-------: | :--: | :--: |
|  0   |   0   | No change |  0   |  X   |
|  0   |   1   |    Set    |  1   |  X   |
|  1   |   0   |   Reset   |  X   |  1   |
|  1   |   1   | No change |  X   |  0   |

