---
title: 随机算法 10 Invertible Bloom Filters
author: tinsir888
date: 2024/4/15
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: '24797e83'
---

# Brief Introduction

Invertible Bloom Lookup Table (abbr. IBLT)

a fancy hash-table stores key-value pair $(x,y)$

space budget: $t$

## Guarantee

It "works" when the number of key-value pairs, $n$, in table has $n\le t$.

"temporarily" stops working if $n\gt t$.

starts working again if $n$ drops below $t$.

## Operations

`(x,y)` is key-value pair.

`insert(x,y)`, `delete(x,y)`, `get(x)`, `listentries`.

# Details

Start with table with $k\ge3$ rows, $m=2\times t$ columns.

In each row, we have a hash function $h_1,\cdots,h_k$.

{% note success modern %}

$h_i$ is universal.

{% endnote %}

Each of the entry, called cell, stores values:

- `count`
- `key_sum`
- `value_sum`

## Insert Operation

1. for $i=1$ to $k$
   - $c=A[i][h_i(x)]$
   - $c.count++$
   - $c.key\_sum+= x$
   - $c.value\_sum+=y$

## Delete Operation

"undo" insert operation

{% note danger modern %}

We must know the pair is there before deletion.

{% endnote %}

1. for $i=1$ to $k$
   - $c=A[i][h_i(x)]$
   - $c.count--$
   - $c.key\_sum-= x$
   - $c.value\_sum-=y$

## Get Operation

slightly different from min sketch count

1. $ret=\infty$

2. for $i=1$ to $k$

   - $c=A[i][h_i(x)]$

   - if $c.count==0$ return "not there"

   - else if $c.count==1$ then

     - if $c.key\_sum==x$

       return $c.value\_sum$

     - else return "not there"

3. return "don't know"

### Probability of Success

If $n\le t$, then `get(x)` succeeds with probability $\ge1-2^{-k}$.

Assume $k\gt1$, $n$ pairs: $(x_i,y_i)$ is stored in IBLT, $n\le t$.

For `get(x)`

- If $x$ is in IBLT

  > 1. 当 $x$ 在 IBLT 的这一行里面，但是 get 函数发现对应的哈希值指向的位置都发生了碰撞的概率。
  > 2. 明明 $x$ 不在 IBLT 中，但是 get 函数发现对应的哈希值指向的位置中存在一个值，即 count=1 但 value 不是 $x$ 对应的。

  Define fail-event $E_i:\{h(x)=h(x_i)\}$,

$$
\Pr[get(x)\text{ fails}]=\Pr[\bigcup_{x_i\neq x}E_i]\le\sum_{x_i\neq x}\Pr[E_i]\\
\le\sum_{x_i\neq x}\frac{1}{m}=\frac{n-1}{m}\le\frac{t-1}{m}=\frac{t-1}{2t}\le\frac{1}{2}
$$

- If $x$ is not in IBLT.

  > 明明 $x$ 不在 IBLT 的这一行里面，但是 get 函数得出该键对应的哈希值指向的位置在 IBLT 这一行中产生碰撞的概率。

  Define fail-event $E_{ij}=\{h(x)=h(x_i)=h(x_j)\}$
  $$
  \Pr[\bigcup_{i\neq j}E_{ij}]\le\sum_{i\lt j}\Pr[E_{ij}]=\sum_{i\lt j}\frac{1}{m^2}=C_n^2\frac{1}{m^2}\\
  =\frac{n(n-1)}{2m^2}\le\frac{t^2}{2m^2}\le\frac{t^2}{8t^2}=\frac{1}{8}
  $$

{% note success modern %}

If we choose $k=\log_2t+\log_2\frac{1}{\delta}$, the probability of failure is at most $\delta$.

{% endnote %}

### How to Avoid False Deletion?

another set of universal hash function $g_1,\cdots,g_k:U\rarr[R]$

while query, check whether $c.hash\_sum==g(c.key\_sum)$

## ListEntries Operation

1. $Q=\empty$

2. for $c$ in IBLT

   - if $c.count==1$
     - $Q.push(c.key\_sum,c.value\_sum)$

   ==// Max size of $Q$ is $mk$ here==

   - while $Q.size\gt0$

     - $(x,y)=Q.top()$

     - $Q.pop()$

     - if $output.continas(x)==false$

       $output.add(x,y)$

     - for $i=1$ to $k$

       - $c'=A[i][h_i(x)]$
       - $c'.count--$
       - $c'.key\_sum-=x$
       - $c'.value\_sum-=y$
       - if $c'.count==1$
         - $Q.push(c'.key\_sum,c',value\_sum)$

### Analysis of Success Probability

hyper-graph

node: memory location

$h$ is truly random

$n$ edges, $e_1,\cdots,e_n$ are independently uniformly random.

List entries fails iff non-empty 2-core exists.

i.e. largest subgraph where every node has $degree\ge2$

$F=\{ListEntries\text{ fails}\}$

$F_j=\{ListEntries\text{ fails with j key-value left}\}$
$$
\Pr[F]=\Pr[\bigcup_{j=2}^nF_j]\le\sum_{j=2}^n\Pr[F_j]\ \ \ (1)
$$
For every $S\in C_{x}^j$, $F_{jS}=\{ListEntries\text{ fails, with S as j keys}\}$
$$
(1)=\sum_{j=2}^n\Pr[\bigcup_{S\in C_x^j}F_{jS}]\le\sum_{j=2}^n\sum_{S\in C_x^j}\Pr[F_{jS}]\ \ \ (2)
$$
Denote: $(T_1,\cdots,T_k)\in (C_{[m]}^{j/2})^k$

$F_{j,S,T_1,\cdots,T_k}=\{LE\text{ fails for S and }\forall_i:h_i(S)\subseteq T_i\}$
$$
(2)=\sum_{j=2}^n\sum_{S\in C_x^j}\sum_{(T_1,\cdots,T_k)\in (C_{[m]}^{j/2})^k}\Pr[F_{j,S,T_1,\cdots,T_k}]\ \ \ (3)
$$

$$
\Pr[h_1(x_1)\subseteq T_1]=\frac{j}{2m}\\
\Pr[h_1(S)\subseteq T_1=(\frac{j}{2m})^j\\
\Pr[\forall_i:h_i(S)\subseteq T_i]=(\frac{j}{2m})^{jk}\\
\Pr[F_{j,S,T_1,\cdots,T_k}]\le\Pr[\forall_i:h_i(S)\subseteq T_i]=(\frac{j}{2m})^{jk}\\
(3)\le\sum_{j=2}^n C_n^j (C_m^{j/2})^k(\frac{j}{2m})^{jk}\le\sum_{j=2}^n(\frac{en}{j})^j(\frac{m2e}{j})^{jk/2}(\frac{j}{2m})^{jk}\\
=\sum_{j=2}^n(\frac{en}{j})^j(\frac{je}{m2})^{jk/2}=\sum_{j=2}^n(\frac{e^2n}{2m})^j(\frac{je}{2m})^{j(k/2-1)}\\
\le\sum_{j=2}^n2^{-j}(\frac{je}{2m})^{j(k/2-1)}\le2\cdot\frac{1}{4}\cdot(\frac{e}{m})^{k-2}\le(\frac{e}{m})^{k-2}\le(1/t)^{k-2}
$$

:skull_and_crossbones::skull_and_crossbones::skull_and_crossbones: