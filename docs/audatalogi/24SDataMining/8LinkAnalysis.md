---
title: 数据挖掘 8 Link Analysis
author: tinsir888
date: 2024/3/18
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: 797948a1
---

PageRank as a fundamental tool in graph mining, connections to Markov process and linear algebra.

Random Walk

# PageRank

An algorithm that provides a score to each web page.

It follows:

- Each link's vote is proportional to the importance of its source page.
- If node $j$ with importance $r_j$ has $n$ out links, each of the $j$'s neighbors gets $r_j/n$ votes.
- Page $j$'s own importance is the sum of the votes on its in-links.

PageRank can be simply expressed as the sum of the scores of the neighbors.

Let $m_{ji}=1/d_i^-$ be the probability that from $i$ we end up to $i$'s neighbor $j$ and $d_i^-$ is the out-degree of node $i$.

The PageRank of a page is the weighted sum of the PageRank of the pages that links to it
$$
r_j=\sum_im_{ji}r_i\\
\mathbf r=\mathbf{Mr}
$$
where $\mathbf M$ is the matrix of the probability of landing on page $i$ from $j$. $\mathbf M$ can be computed as $\mathbf {A^\top\Delta}^{-1}$.

## The Power Iteration Method

Think of $\mathbf{r}=\mathbf{Mr}$ as an iterative process that updates the previous value of $\mathbf r^t$ at iteration $t$ with the new $\mathbf r^{t+1}=\mathbf{Mr}^t$. This is the idea of Power Iteration method.

The **PageRank vector** $\mathbf r$ is initially uniform with $1/n$ for all nodes. The iteration continues until convergence $||\mathbf r^t-\mathbf r^{t+1}||_2\lt\epsilon$. Note that the choice of stopping criteria is arbitrary and can be easily substituted with a simple sum of absolute differences.

### Pseudo Code of Power Iteration

Input: Transition matrix $\mathbf M$, number of nodes $n$

Output: PageRank $\mathbf r$

1. $\mathbf r^0=\frac{1}{n}\mathbf 1$

2. $t\gets0$

3. repeat

   - $t\gets t+1$
   - $\mathbf r\gets\mathbf r^t$
   - $\mathbf  r^t\gets\mathbf{Mr}$

   until $||\mathbf r^t-\mathbf r||_2\lt\epsilon$

**Dominant eigenvector**. The eigenvector of $\mathbf M$. In fact, PageRank corresponds to the eigenvalue $1$. $\mathbf{Mr}=1\mathbf r$. Furthermore, the PageRank is the dominant eigenvector of the matrix $\mathbf M$.

Power Iteration method converges to the dominant eigenvector by following theorem:

**Theorem**. The sequence $\mathbf {Mr}^0,\cdots,\mathbf M^t\mathbf r^0$ approaches the dominant eigenvector of $\mathbf M$.

**Random Walk Interpretation**. A characterization of PageRank. This assumes that at any point in time $t$ an internet surfer lands on page $i$, and at time $t+1$ the surfer follows an outlink from $i$ uniformly at random. Once the surfer lands on a different pages, they repeat the process of selecting a linked page uniformly at random.

Let $\mathbf p(t)$ be a vector whose $i$-th coordinate is the probability that the surfer is at page $i$ at time $t$. Then $\mathbf p(t)$ is a probability distribution over the pages. At time $t+1$ the surfer will reach any of the linked pages with probability $\mathbf p(t+1)=\mathbf{Mp}(t)$. At convergence $\mathbf p(t+1)=\mathbf{Mp}(t)=\mathbf p(t)$, then $\mathbf p(t)$ is a **stationary distribution** of the random walk.

The PageRank $\mathbf r$ satisfies $\mathbf r=\mathbf{Mr}$, so $\mathbf r$ is a stationary distribution for the random walk. A central result from the theory of random walks says that graphs that satisfy certain conditions have a unique stationary distribution and will eventually converge regardless of the initial distribution $t=0$.

**Convergence of PageRank**. Does the iteration $\mathbf r=\mathbf{Mr}$ converge? The answer is no. It's possible to design such an example.

**Other problems with PageRank**.

1. Sinks: a node without outgoing edges.
2. Spider trap: a group of nodes without outgoing edges.

## Pros and Cons

:thumbsup:

- Fast to compute with Power Iteration $\mathcal O(mt)$ where $t$ is number of iterations.
- It provides a measure of relevance that does not depend on the content of a page.

:thumbsdown:

- It measures generic popularity of a page, but is biased against topic-specific authorities.
- Susceptible to link spam.
- Uses a single measure of importance.

# Solving PageRank Issues

Using **teleportation**.

It is the process under which, with some probability the surfer chooses a random node where to teleport.

Process:

- At each iteration $t$ we toss a coin.

- With probability $\alpha$ we continue in our surfing by choosing randomly among the node's neighbors, else with probability $1-\alpha$ we select a random node to teleport.
  $$
  r_j=\sum_{i,j\in E}\alpha\frac{r_i}{d_i}+(1-\alpha)\frac{1}{n}
  $$

- Equivalently written in matrix notation
  $$
  \mathbf r=\alpha\mathbf{Mr}+(1-\alpha)[\frac{1}{n}]_{n\times n}
  $$

Observation: $\alpha$. If it's close to $0$ the PageRank will converge to the uniform distribution $1/n$; if it's close to $1$ the PageRank might converge slower like original version. A typical choice of $\alpha:\alpha\in[0.8,0.9]$.

## Personalized PageRank

There are $(1-\alpha)\frac{1}{n}$ teleports equally on all nodes. However, in some application we might to teleport only on a small number of nodes by substituting the vector $[\frac{1}{n}]_n$ with a generic probabilistic vector $\mathbf p$ obtaining
$$
\mathbf r=\alpha\mathbf{Mr}+(1-\alpha)\mathbf p
$$
This equation is called **Personalized PageRank**. This kind of PageRank corresponds to **Random Walks with Restart**:
$$
\mathbf r=\alpha\mathbf{A\Delta}^{-1}\mathbf r+(1-\alpha)\mathbf p
$$
where $\mathbf{A\Delta}^{-1}$ is a stochastic matrix as the sum of each column is $1$. $\mathbf p$ is the restart vector, and is also stochastic.

## Topic-specific PageRank

Topic-specific PageRank computes the Personalized PageRank for a group of nodes $S$. These nodes are selected among the pages that have the same topic.

If we cluster the pages by words we might find different communities. One way to restrict to one of these communities is to run PPR on the of initial pages with the same words. We can compute PPR for each topic set $S$ and get a different vector $r_S$. We update the transition matrix $\mathbf M$ with a probability of teleporting to the nodes in the set $S$
$$
w_{i,j}=\begin{cases}
\alpha m_{i,j}+(1-\alpha)/|S|\text{ if }i\in S\\
\alpha m_{i,j}+0\text{ Otherwise}
\end{cases}
$$
As we teleport uniformly to any node in $S$ we can simply use the personalization vector $\mathbf p$ and define
$$
p_i=\begin{cases}
\frac{1}{|S|}\ i\in S\\
0
\end{cases}
$$
In this way, the rest of the PPR definition remains the same besides $\mathbf p$.

## TrustRank

Teleportation probably solves a deal of problems, but PageRank may still be biased towards pages that spread false information.

TrustRank tries to solve this issue with the use of **trusted pages**.

Main idea: the principle of approximate isolation under which it is unlikely that a good page points to a bad page.

Two problem to solve:

1. how to select the set of trusted pages;
2. how to decide whether a page is a spam or is a good one.

**Selecting a subset of trusted pages**. In order to select a list of trusted pages we can use different approaches:

1. Sample a set of pages and ask a set of volunteers to evluate whether the pages are good or bad. ==Expensive!!!==
2. Select the $k$ pages with the highest PageRank, assuming that bad pages cannot eventually reach too high PR values.
3. Select a set of trusted domains.

**Detecting spam pages**. One way to detect spam pages is to define a threshold value and mark all pages below such threshold as spam. However, finding the right threshold is difficult.

Another solution is to compute **spam mass** as the deviation of TrustRank from PageRank. The spam mass is the fraction of a page's PageRank that comes from spam pages. Since in practice, we forget about spam pages, compute an estimate instead.

Let $r_x$ be the PR of page $x$. The $r_x^+$ is the PR of $x$ with teleport only into trusted pages. Then spam mass is $x=\frac{r_x^-}{r_x}$ where $r_x^-=r_x-r_x^+$. Pages with high spam mass are considered spam.

# Link Farms

One problem for PageRank: Link farms.

A **link farm** is a page $s$, usually controlled by a malicious spammer, that is connected to a set of good and trusted pages. Such page creates a number $T$ of fictitious pages that links from and to $s$ with the purpose of increasing artificially its PR to appear higher in search engines. But how many of such pages does $s$ need?

Let $p_s$ be the PR of $s$ and $p_X$ the PR of the "honest" pages that link to $s$. The PageRank $p_t$ of any fake pages $T$ is
$$
p_t=\frac{\alpha p_s}{T}+\frac{1-\alpha}{n}
$$
The PR $p_s$ is the sum of the contribution of the honest pages $p_X$ and the fake pages
$$
p_s=p_X+p_t\\
=\frac{p_X}{1-\alpha^2}+\frac{\alpha T}{(1+\alpha)n}
$$
If $\alpha=0.85$, the quantity ${1\over1-\alpha^2}=3.6$. As such for each honest page the malicious page $s$ is rewarded $3.6$ times the honest page's PageRank. This effect can be amplified with a large $T$.

# HITS: Hubs and Authorities

The **Hypertext-induced topic selection** (HITS) is a measure of importance of pages and documents similar to PageRank.

Main idea: in order to find good newspaper, we can find experts that support the newspaper. Any link becomes a vote.

In HITS, every page has 2 scores, a *hub score* and an *authority score*. The hub score is the sum of the scores to the authorities pointed by the hub; the authority score is the sum of votes coming from hubs.

HITS uses **principle of repeated improvement** that iterates over hubs an authorities in turn.

Each page $i$ starts with hub score $1$. The iteration works in alternating manner: after the authorities collected their votes, the hubs sum the scores from the authorities. Once again the computation of the scores is a mutually recursive definition. A good hub links to many good authorities and a good authority is linked to many good hubs. We denote hub and authority scores as $h$ and $a$.

## Pseudo Code HITS

1. Initialize $a_i^0=n^{-1/2},h_i^0=n^{-1/2}$ for all $i$

2. repeat

   - $\forall i:a_i^{t+1}=\sum_{j,i\in E}h_j^t$
   - $\forall i:h_i^{t+1}=\sum_{i,j\in E}a_j^t$
   - Normalize $\sum_i(a_i^{t+1})^2=1,\sum_j(h_j^{t+1})^2=1$
   - $t\gets t+1$

   until $\sum_i(h_i^t-h_i^{t-1})^2\lt\epsilon$ and $\sum_i(a_i^t-a_i^{t-1})^2\lt\epsilon$

**HITS converges to a single stable point**. We can see that HITS indeed converge by considering the authority vector $\mathbf a$ and the hub vector $\mathbf h$. In this way we can rewrite $h_i=\sum_jA_{ij}a_j\Rarr\mathbf{Aa}$ and $a_i=\sum_{j,i\in E}h_j=\sum_jA_{ji}h_j\Rarr\mathbf{A^\top h}$. For two consecutive iterations HITS iterations we obtain
$$
\mathbf a=(\mathbf{A^\top A})\mathbf a\\
\mathbf h=(\mathbf{AA^\top})\mathbf h
$$
and repeat until the difference of $\mathbf h,\mathbf a$ in two different iterations is less than $\epsilon$. From the two equations above it is easy to see that HITS converges to the principal eigenvalue of $\mathbf h^*$ for $(\mathbf{AA^\top})$ and $\mathbf a^*$ for $(\mathbf{A^\top A})$.