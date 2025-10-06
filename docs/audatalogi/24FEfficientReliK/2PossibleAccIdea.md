---
title: Efficient ReliK 2 Possible Parallelization Idea
author: tinsir888
date: 2024/9/17
cover: /img/AUrelik.png
katex: true
tags:
  - 笔记
  - 数据挖掘
  - 人工智能
  - 知识图谱
categories:
  - AUDatalogi
  - 数据挖掘
  - 知识图谱嵌入可靠性衡量
abbrlink: 970b0da7
---

Some ideas from Maximilian.

# Recap Pseudo Code

Look at the Pseudo code again:

Input: KG $\mathcal K:\langle\mathcal E,\mathcal R,\mathcal F\rangle$, triple $x_{hrt}=(h,r,t)\in\mathcal F$, embedding score function $s:\mathcal E\times\mathcal R\times\mathcal E\to\mathbb R$, sample size $k\in\mathbb N$

Output: $ReliK_{LB}(x_{hrt})$ or $ReliK_{Apx}(x_{hrt})$

1. $S_H\gets$ sample $k$ triples from $\mathcal N^-(h)$; $S_T\gets$ sample $k$ triples from $N^-(t)$.

2. $rank_H\gets1$; $rank_T\gets1$

3. for $x_{h'r't'}\in S_H\cup S_T$ do

   - If $s(x_{hrt})\le s(x_{h'r't'})$ then

     - if $h'=h$ then

       $rank_H\gets rank_H+1$

     - if $t'=t$ then

       $rank_T\gets rank_T+1$

4. return $\frac{1}{2}\left(\frac{1}{rank_H+|\mathcal N^-(h)|-|S_H|}+\frac{1}{rank_T+|\mathcal N^-(t)|-|S_T|}\right)$ for $ReliK_{LB}$

   or $\frac{1}{2}\left(\frac{1}{rank_H\frac{\mathcal N^-(h)}{|S_H|}}+\frac{1}{rank_T\frac{\mathcal N^-(t)}{|S_T|}}\right)$ for $ReliK_{Apx}$

# Ideas for Parallelization

## Idea 1 Parallel Sampling

Sampling the negative sets $S_H$ and $S_T$ can be done in parallel for both head and tail entities. GPU-based libraries like PyTorch can handle large-scale sampling efficiently on GPUs.

I will think about it.

## Idea 2 Rank Update

For each sampled triple $x_{h'r't'}$, it can be parallelized across all samples in $S_H\cup S_T$. **For-parallel**

GPUs are very efficient at handling this kind of parallel computation, especially with batched matrix operations.

:white_check_mark:I will be working on this.

{% note info flat %}

Will there be some write-after-write or read-after-write issues? Should I add some write-lock?

{% endnote %}