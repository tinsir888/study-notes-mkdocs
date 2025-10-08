---
title: 随机算法 6 Streaming Heavy Hitters
author: tinsir888
date: 2024/3/4
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: e210bfb2
---

Lecture notes by Kasper.

# Review of Count Sketch

This course: extends the results for count sketch algorithm (Lecture 5).

Strict turnstile model, where we maintain estimates of some underlying frequency vector $f$. The vector $f$ has coordinates indexed by integers in $[U]$ for some universe size $U$.

We receive updates one at a time.

An update: $(i,\Delta)$ and has the effect of updating $f_i\gets f_i+\Delta$ for $i\in[U]$ and $\Delta\in\{x\}_{x=-M}^M$.

Assume: $M$ is bounded by a polynomial in $n$ i.e. $M=n^{O(1)}$. (Weight change fits in $O(\log n)$ bits)

We also promise that at any given time, $f_i\ge0\forall i$.

In the count sketch, we learned how to use count min sketch to support frequency estimation queries i.e. we have a data structure using $O(\frac{1}{\epsilon}\log\frac{1}{\delta})$ space that upon receiving an index $i\in[U]$ as query, can return a value $\hat{f_i}$ that with probability at least $1-\delta$ satisfies $f_i\le\hat{f_i}\le f_i+\epsilon||f||_1$ where $||f||_1=\sum_i|f_i|$.

update time for count min sketch: $O(\log\frac{1}{\delta})$

query time for count min sketch: $O(\log\frac{1}{\delta})$

# Problem Formulation

In many applications, we concern about finding *heavy hitter*s rather than estimating frequency.

**An $\epsilon$ heavy hitter is an index $i$ s.t. $f_i\ge\epsilon||f||_1$.**

We still receive frequency updates. However, there is just one query. To answer a query, we must produce a list $\mathcal L$ satisfying the following:

- All $\epsilon$ heavy hitters are in $\mathcal L$, i.e., all indices $i$ with $f_i\ge\epsilon||f||_1$ are in $\mathcal L$.
- No index $i$ with $f_i\lt\frac{\epsilon}{2}||f||_1$ is in $\mathcal L$.

The algorithm is allowed to fail with probability $\delta$. 

We have no requirements on indices $i$ s.t. $\frac{\epsilon}{2}||f||_1\le f_i\lt \epsilon||f||_1$.

# Solution of Cormode and Muthukrishannan

For simplicity, assume $U$ is a power of $2$.

A complete binary tree $\mathcal T$ on top of $U$ (one leaf per index $i\in U$). Such a tree has $\log U+1$ levels and we denote the level of the leaves by level $0$ and the root's level by $\log u$. For a node $v\in\mathcal T$, let $g_v$ denote the sum of $f_j$'s over all $j$ corresponding to leaves in the sub-tree rooted at $v$.

We think of the list of values $g_v$ for the $U/2^i$ nodes at level $i$ as forming another frequency vector on a universe of size $U/2^i$. We use $f^i$ to denote this vector of frequencies for the $i$-th level of the tree.

Obviously $f_j^i=\sum_{h=0}^{2^i-1}f_{j2^i+h}$, the frequency for a node is also just the sum of the frequencies of its two children.

We focus on the case where we are aiming at an error probability $\delta$ of $1/U$, i.e., polynomially small in the universe size. The data structure is as follows:

- For every level $i$ of $\mathcal T$, we store a count min sketch of $f^i$ having error probability $\delta'=1/|U|^2$ and approximation $\epsilon'=\epsilon/2$. On a update $(j,\Delta)$, we conceptually add $\Delta$ to the frequency of all nodes on the root-to-leaf path leading to the leaf corresponding to $j$.

  This can be done by adding $\Delta$ to $f^i_{\lfloor j/2^i\rfloor}$ for $i=0,\cdots,\log U$. Thus an update makes $O(\log U)$ updates on count min data structures, each with error probability $1/U^2$ thus the total update time is $O(\log^2U)$. similarly, we get a space usage of $O(\frac{1}{\epsilon}\log^2U)$.

- The query algorithm: Start at the root node $r$. When visiting a node $v$ at level $i$, if $i=0$ (leaf), add $v$ to the output list. Otherwise, $v$ is an internal node at some level $i\gt0$. Query the count min sketch on the vector $f^{i-1}$ to obtain estimates of the frequencies stored at the two children of $v$. Recurse into those children for which the estimate is at least $\epsilon||f||_1$.

  The query algorithm works. First define a node $v$ in $\mathcal T$ to be heavy if its frequency is at least $(\epsilon/2)||f||_1$. Otherwise call it light. Since we are in the strict turnstile model and all frequencies in $f$ are non-negative, it follows that $||f||_1=||f^0||_1=\cdots=||f^{\log U}||_1$. We have some observations as follows.

**Observation 1**. For any level $i$ in $\mathcal T$, there are no more than $2/\epsilon$ heavy nodes.

> If we had more than $2/\epsilon$ heavy nodes at some level $i$, then we would have $||f^i||_1\ge(2/\epsilon)(\epsilon/2)||f||_1=||f||_1=||f^i||_1$, a contradiction.

**Observation 2**. If $i$ is the index of an $\epsilon$ heavy nodes at some level $i$, then all the nodes on the root-to-leaf path leading to leaf corresponding to $i$ are heavy.

> Since all frequencies are non-negative, it follows that the frequency of an internal node is always at least as large as any of its children's.

By observation 2, the list $\mathcal L$ produced by the query algorithm always contains all the heavy hitters. Now let $i$ be an index with $f_i\lt(\epsilon/2)||f||_1$. Note that the leaf $v$ corresponding to $i$ is light. It follows that if $i$ is added to $\mathcal L$, then the query algorithm visited $v$'s parent and made the estimate of $v$'s frequency that was off by more than $(\epsilon/2)||f||_1=\epsilon'||f||_1$. This happens with probability no more than $1/U^2$ by our setting of parameters for the count min sketches. We can thus union bound over all light leaves and conclude that none of them are added to $\mathcal L$ with probability at least $1-1/U$.

To analyze the query time, observe that if no estimate made by a count min sketch fails, the we only visit heavy nodes. By observation 1, there are $O(\frac{1}{\epsilon}\log U)$ heavy nodes in $\mathcal T$. Thus the query time is $O(\frac{1}{\epsilon}\log^2U)$ since we make two queries to count min sketches with error probability $1/U^2$ in each.

Note that if we want the query time to be worst case, we can simply abort with an empty output list in case we visit more than $(2/\epsilon)(\log U+1)$ nodes in $\mathcal T$ since this means that at least one light node was visited, and this happens with probability no more than $1/U$.

To summarize, we have a data structure with worst case update time $O(\log^2U)$, worst case query time $O(\frac{1}{\epsilon}\log^2U)$ and space $O(\frac{1}{\epsilon}\log^2U)$.

# Solution of Larsen, Nelson, Nguyen and Thorup

This solution improves by a factor $\log U$ in all parameters at the cost of moving to expected query time.

The change is simple: Keep the exact same data structure as above, expect that all the count min sketches stored for levels $i\gt0$ are replaced by count min sketches with error probability $1/4$ instead of $1/U^2$. The count min sketch at the leaves still has error probability $1/U^2$. The query algorithm and update algorithm remains the same.

The space usage is $O(\frac{1}{\epsilon}\log U)$ as each internal level new uses space only $O(\frac{1}{\epsilon})$. 

Similarly, the update time drops to $O(\log U)$ because each internal level costs $O(1)$ to update. For the correctness probability, note that the argument from the previous section only argues about queries made to the count min sketch at level $0$, thus the correctness probability still holds.

The only thing changed is the query time due to more nodes in $\mathcal T$ being visited.



We want to bound the expected number of light nodes visited.

Define a light node $v$ its light depth $d(v)$ as the distance to its **nearest heavy ancestor** in $\mathcal T$.

Consider all light nodes at levels $i\ge2$. Let $V$ be the set of such nodes. If we visit such a node, we spend $O(1)$ time making two queries to a count min sketch. Thus if we define an indicator random variable $X_v$ for each such node $v\in V$, taking the value 1 if $v$ is visited by the query algorithm, and 0 otherwise, then the expected time spend on visiting all light nodes $v\in V$ is

$$
O(\mathbb E[\sum_{v\in V}X_v])=O(\sum_{v\in V}\mathbb E[X_v])=O(\sum_{v\in V}\Pr[v\text{ is visited}]).
$$

Thus we need to bound $\Pr[v\text{ is visited}]$. For this, the trick is to use the light depth.

Let $V_i\subseteq V$ be the set of nodes in $V$ with light depth $i$ for $i=1,\cdots,\log U$. Then we have

$$
O(\sum_{v\in V}\Pr[v\text{ is visited}])=O(\sum_{i=1}^{\log U}\sum_{v\in V_i}\Pr[v\text{ is visited}]).
$$

Let $v\in V_i$ and consider $\Pr[v\text{ is visited}]$.

- For $v$ to be visited, it must be the case that query made in every single ancestor up to its nearest heavy ancestor failed, i.e. returned an estimate that was off by at least $\epsilon'||f||_1$.

- Since these queries to $d(v)=i$ independent count min data structures with failure probability $1/4$ each, the probability they all fail is at most $1/4^i$.

  Hence, we have

  $$
  O(\sum_{i=1}^{\log U}\sum_{v\in V_i}\Pr[v\text{ is visited}])=O(\sum_{i=1}^{\log U}\sum_{v\in V_i}4^{-i}).
  $$

Next we bound $|V_i|$. (By examine a heavy node)

- Any heavy node can be the nearest heavy ancestor of at most $2^i$ light nodes with light depth $i$. This is trivial because any node has no more than $2^i$ descendants $i$ levels down.

- By observation 1, there are no more than $O(\epsilon^{-1}\log U)$ heavy nodes in $\mathcal T$ and therefore we have $|V_i|\le O(2^i\epsilon^{-1}\log U)$.

  We thus conclude that

  
  $O(\mathbb E[\sum_{v\in V}X_v])=O(\sum_{i=1}^{\log U}\sum_{v\in V_i}4^{-i})=O(\sum_{i=1}^{\log U}2^i4^{-i}\frac{1}{\epsilon}\log U)$ 

  
  $=O(\frac{1}{\epsilon}\log U\sum_{i=1}^{\log U}2^{-i})=O(\frac{1}{\epsilon}\log U\sum_{i=1}^{\log U}2^{-i})=O(\epsilon^{-1}\log U)$
  
  as claimed.


It remains to bound the cost of the queries made in light nodes at level $i=1$. Recall that these query the data structure at level $0$ which had an error probability of $1/U^2$, and thus the cost of visiting such a light node is $O(\log U)$ rather than $O(1)$. If we this time define $V_i$ as the set of light nodes at level $1$ that have a light depth of $i$, then by argument similar to above, the expected time spend visiting nodes $v\in V_i$ is $O(\sum_{i=1}^{\log U}|V_i|4^{-i}\log U)$.

Fortunately, the new sets $V_i$ are a factor $\log U$ smaller than before. To see this, note that any node in $V_i$ must have its nearest heavy ancestor at level exactly $1+i$. But observation 1 tells that there are only $O(\frac{1}{\epsilon})$ heavy nodes at level $i+1$, and each such node has only $2^i$ descendants at level $1$.

Therefore we have $|V_i|=O(\epsilon^{-1}2^i)$. Inserting this, we conclude that

$$
O(\sum_{i=1}^{\log U}|V_i|4^{-i}\log U)=O(\sum_{i=1}^{\log U}\frac{1}{\epsilon}2^i4^{-i}\log U)\\
$$

$$
=O(\frac{1}{\epsilon}\log U)
$$

## Summary

Improved space to $O(\frac{1}{\epsilon}\log U)$.

Update time in worst case $O(\log U)$

Expected query time $O(\frac{1}{\epsilon}\log U)$