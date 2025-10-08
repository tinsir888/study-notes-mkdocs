---
title: Efficient ReliK 1 Paper Reading
author: tinsir888
date: 2024/9/15
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
abbrlink: 3bdc648b
---

This is one of the two projects I undertake in 24 Fall semester, which is related to data mining.

Supervisor: Assoc. Prof. Davide Mottin, PhD Student Maximilian Egger.

Generally speaking, they have already developed a new method, *ReliK*, for measuring knowledge graph embeddings, but they want to speed it up with GPUs.

So my project is focusing on this. I wish to speed them up with some tolerance on small accuracy loss.

I need to first get familiar with the paper, [*ReliK: A Reliability Measure for Knowledge Graph Embedding*](https://arxiv.org/abs/2404.16572). Here is some digest and notes from it.

# Abstract

Object of ReliK: To assess a priori how well a knowledge graph embedding (KGE) will perform on a downstream task and in a specific part of the knowledge graph.

ReliK aka Reliability measure of KGEs.

- It relies only on KGE embedding scores.
- Task independent
- KGE independent

Correlates well with some downstream tasks:

- tail/relation prediction, triple classification
- rule mining, question answering.

# Introduction

KGE: generate a **vector representation** for entities and relationships of a KG.

Motivation: KEG method depends on which downstream task. We want to **unified, global** measure to quantify the reliability of KGE method.

Contributions: ReliK, principled measure that quantifies the  reliability of how a KEG will perform will on a certain downstream task in a specific part of the KG ==without executing that task or training KG!!!==

ReliK is based on the relative ranking of existing KG triples w.r.t. non-existing one.

# Preliminaries

## Knowledge Graph

KG $\mathcal K:\langle\mathcal E,\mathcal R,\mathcal F\rangle$ is a triple consisting of a set $\mathcal E$ of $n$ entities, a set $\mathcal R$ of relationships, and a set $\mathcal F\subset\mathcal E\times\mathcal R\times\mathcal E$ of $m$ facts.

A fact is a triple $x_{hrt}=(h,r,t)$. head, relation, tail.

We say $x_{hrt}$ is positive if it actually exists in the KG $x_{hrt}\in\mathcal F$, negative otherwise $x_{hrt}\not\in\mathcal F$.

## Knowledge Graph Embedding

It is a representation of entities and relationships in a $d$-dimensional space, $d\ll\vert\mathcal E\vert$. The $\mathbb R^d$ or $\mathbb C^d$ space.

The **embedding score**: function $s:\mathcal E\times\mathcal R\times\mathcal E\to\mathbb R$, which quantifies how like a triple $x_{hrt}$ exists in $\mathcal K$ based on the embedding of its head, relationship, and tail.

- Intuitively, the higher $s(x_{hrt})$, the more likely the existence of $x_{hrt}$ in $\mathcal K$.

# Key Reliability

## Desideratum

1. **Embedding-agnostic**. It is independent of the specific KGE method.
2. **Learning-free**. No further KGE training.
3. **Task-agnostic**. It is independent of downstream task.
4. **Locality**. A good predictor of KGE performance locally to a given triple.

## ReliK Measure

To fulfill 1. 2.: Treat embedding score as a **black-box** function that provides only an indication of the actual existence of a triple.

Use position of a triple via a ranking based on embedding score: **Assume the relative ranking of a positive triple to be higher than that of negative.**

For 4. locality, we assume that a local approach that consider triples relative to a neighborhood is more appropriate.

### Definition

For a triple $x_{hrt}=(h,r,t)$ we compute the neighbor set $\mathcal N^-(h)$ of all possible negative triples, that is triples with head $h$ that do not exist in $\mathcal K$.

Similarly, we define $\mathcal N^-(t)$ for tail.

Head rank: $rank_H(x_{hrt})=|\{x\in\mathcal N^-(h):s(x)\gt s(x_{hrt})\}|+1$

Similarly, we define $rank_T(x_{hrt})$ for tail.

$$
ReliK(x_{hrt})=\frac{1}{2}\left(\frac{1}{rank_H(x_{hrt})}+\frac{1}{rank_T(x_{hrt})}\right).
$$

ReliK can easily be extended from single triples to sub-graphs by computing the average reliability among the triples in the subgraph.

We define the ReliK score of a set $S\subseteq\mathcal F$ of triples as

$$
ReliK(S)=\frac{1}{S}\sum_{x_{hrt}\in S}ReliK(x_{hrt}).
$$

$ReliK\in(0,1]$. The higher the $ReliK$, the better reliability.

The lower the head-rank and tail-rank, the better the ranking of $x_{hrt}$ induced by the underlying embedding scores.

## Efficiently Computing ReliK

Computing original ReliK requires $\Omega(|\mathcal C|\cdot|\mathcal R|)$, as it needs to scan the entire negative neighborhood of the target triple.

Computationally TOO HEAVY!!!

We thus propose approximate version of ReliK with efficiency.

Main intuition of approximation is that **the precise ranking of many potential triples is not needed**.

What it matters is just the **number of those triples** that exhibit a **higher embedding score** than the target triple.

Lower bound:

- $ReliK_{LB}$, a lower bound to $ReliK$ by counting the negative triples in the sample that have a lower embedding score.

Approximate

- $ReliK_{Apx}$, estimation of $ReliK$ by evaluating the fraction of triples in the sample that have a higher score than the triple under consideration and scaling this fraction to the total number of negative triples.

Let $S_H$ be a random subset of $k$ elements selected without replacement independently and uniformly at random from negative neighborhood $\mathcal N^-(h)$ of the head $h$ of a triple $x_{hrt}$.

$$
rank_H^S(x_{hrt})=\vert\{x\in S_H:s(x)\gt s(x_{hrt})\}\vert+1,
$$

to be the rank of the score $s(x_{hrt})$ the KGE assigns to $x_{hrt}$, among all the triples in the sample.

Similarly, we compute $rank_T^S$ for tail's neighborhood $\mathcal N^-(t)$.

### Lower Bound Estimator

$$
rank_H(x_{hrt})\le rank_H^S(x_{hrt})+|\mathcal N^-(h)|-|S_H|\\
$$

$$
rank_T(x_{hrt})\le rank_T^S(x_{hrt})+|\mathcal N^-(h)|-|S_T|\\
$$

$$
\Rightarrow ReliK_{LB}(x_{hrt})=\frac{1}{2}\left(\frac{1}{rank_H^S(x_{hrt})+|\mathcal N^-(h)|-|S_H|}+\frac{1}{rank_T^S(x_{hrt})+|\mathcal N^-(t)|-|S_T|}\right),
$$

It holds that $ReliK_{LB}(x_{hrt})\le ReliK(x_{hrt})$.

### Upper Bound Estimator

Theoretical analysis is omitted here.

$$
ReliK_{Apx}(x_{hrt})=\frac{1}{2}\left(\frac{1}{rank_H^S(x_{hrt})\frac{\mathcal N^-(h)}{|S_H|}}+\frac{1}{rank_T^S(x_{hrt})\frac{\mathcal N^-(t)}{|S_T|}}\right).
$$

## Pseudo Code

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

### Time Complexity

$\mathcal O(k)$.

When $k\approx 20\%\cdot n$, 2.5X faster, mean square error = 0.002.

# Experimental Evaluation

~~omitted~~