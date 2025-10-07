---
title: 数据挖掘 12 Frequent Itemsets and Association Rules
author: tinsir888
date: 2024/4/22
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: d0ab3e44
---

Frequent itemsets and association rules.

This problem aims to find a set of items that are bought together. These itemsets can be efficiently mined by exploiting the apriori principle of the support measure.

# Frequent Itemsets Mining

Frequent itemsets: collections of objects that appear a minimum number of times in the baskets of the customers.

Set of $d$ items $\mathcal I$, set of $n$ transactions identifiers $\mathcal T$.

Dataset $\mathcal D$ is a set of pairs $(i,t)\in\mathcal I\times\mathcal T$.

A basket is a transaction-set $t(\mathcal D)=\{i_1,\cdots,i_{|t|}\}$.

An **itemset** is a subset of $I\subseteq2^\mathcal I$ items, where $2^\mathcal I$ is the powerset of all subsets of $\mathcal I$.

Definition of **support**: For an itemset $I$ we define the support $\sigma(I)$ as the number of transactions that contain itemset $I$, i.e., $\sigma(I)=|\{t\in\mathcal T|I\subseteq t(\mathcal D)\}|$.

**Frequent itemset mining** problem finds all the itemsets $I\subseteq 2^\mathcal I$ that have a support $\sigma(I)\ge minsup$. The support can be expressed as a number or a percentage, similarly to frequent subgraph mining in last lecture. If we have $d$ items, we have potentially $2^d$ itemsets. Itemsets are typically represented in a lattice structure, in which the root is empty, and each node is the union of itemsets in the parent nodes. The question is ==how do we find frequent itemsets efficiently==.



A naive solution. To count each itemset individually. This requires to construct $2^d$ itemsets and compte the frequency for each of them, reaching $\mathcal O(2^dnw)$ time and $\mathcal O(d)$ space, where $w$ is the size of the largest transaction. :thumbsdown:IMPRACTICAL!

## The Apriori Algorithm

If $X,Y\in 2^\mathcal I$ are two itemsets s.t. $X\subseteq Y$, the support $\sigma(X)\ge\sigma(Y)$. We say that the support is **anti-monotone**.

The algorithm exploits the Apriori property by means of an iterative process that alternates **frequent itemset generation**, and **candidate generation**. Let $C_k$ be the candidate itemsets of size $k$, let $L_k$ be the frequent itemsets of size $k$. The Apriori algorithm starts from the intuition that, ==instead of generating all the itemsets, the candidates of size $k$ should only contain frequent itemsets of size $k-1$.==

### Pseudo-code

Input: Data $\mathcal D$, items $\mathcal I$, minimum support $minsup$.

1. $k\gets 1$

2. $C_1\gets\mathcal I$

3. while $C_k\neq\emptyset$ do

   // frequent itemset generation

   - Scan DB to find which itemsets in $C_k$ are frequent, put them into $L_k$

   // candidate generation

   - Use $L_k$ to generate a collection of candidate itemsets $C_{k+1}$ of size $k+1$.
   - $k\gets k+1$

**Candidate generation**. It constructs the candidate itemsets of size $k+1$ by combining frequent itemsets of size $k$. For simplicity, we assume that itemsets are ordered. Also we assume that itemsets in $L_k$ are ordered. We can generate candidates of size $k+1$ by joining two itemsets of size $k$ that share the first $k-1$ items. In a database terms, we are computing a self-join between the list of candidates of size $k$ using the first $k-1$ columns as joining attributes.

Once a new candidate is generated, we can check whether all the ordered subsets are also frequent, i.e., they are in the set $L_k$. This ensure the correct application of the Apriori principle. The generatiion of candidates $C_{k+1}$ proceeds as follows

1. Self-join $L_k$: create set $C_{k+1}$ by joining frequent $k$-itemsets that share the first $k-1$ items
2. Pruning step: Remove a $C_{k+1}$ candidate if a $k$-subset is not frequent.

**Frequent itemset generation**. Important step: counting the support for each of the candidates $C_k$. Speed up by using hash map. Better solution: hash-tree.

**Itemsets of size** $k=2$. For a typical shop-basket data, the number of $k=2$ items is by far the largest among other $k$ values. It's important to have efficient, in-memory optimizations. There are 2 approaches:

1. counting all pairs using a triangular matrix

   requires 4 bytes per pair

2. keeping a table for triples $[i,j,c]$, where $c$ is the count of the pair of items $(i_i,i_j)$.

   require 12 bytes per pair but only for those pair with positive count.

### Factors Affecting the Complexity

- Minimum support threshold
- Dimensionality of the dataset
- Size of Database
- Average transaction width. It may increase max length of frequent itemsets and traversals of has h tree.

# Association Rule Mining

It is a refinement of frequent itemsets, in which an itemset is frequent conditionally to another itemset. E.g. $\{a,b\}\Rightarrow\{c,d,e\}$ means when items $a,b$ occurs, also $c,d,e$ occur.

**Definition of association rule**. It is an implication of the form $X\Rightarrow Y$ as the number of items $Y$ appears in transactions that contain $X$, i.e., $\gamma(X\Rightarrow Y)=\frac{\sigma(X\cup Y)}{\sigma(X)}$.

The association rule mining task finds all rules at least with support $minsup$ and confidence $min con\ f$. The algorithm is apriori + additional step to generate the rules. The algorithm proceeds:

1. Frequent itemset generation. Generate all itemsets whose support is greater than $minsup$
2. Rule generation. Generate rules with confidence at least $mincon\ f$ for each frequent itemset, where a rule is a partitioning of a frequent itemset into left-hand-side and right-hand-side.

So the pending issue for new algorithm is ==how do we generate such rules==.

## Rule Generation

The naive way: enumerate all the LHS and RHS. The process takes for each frequent itemset $S$ all subsets $L\subset S$ and generates rules of the form $S-L\Rightarrow L$ that satisfy minimum confidence.

Does confidence posses the desired apriori property? Not in the strict sense.

Luckily, the apriori property holds for rules generated from the same itemset.

If we move items from LHS to RHS as the support in the denominator of the confidence increases. As such, if a rule $X\Rightarrow Y$ from the same item has confidence $\le minconf$ all rules in which the RHS is a superset of $Y$ also have low confidence and can be pruned.

So we generate a candidate rule by merging 2 rules that share the same prefix on RHS. This process is essentially the apriori algorithm on RHS.

# FP-Tree and FP-Growth

A smarter and more intricate algorithm, that exploits a tree structure called FP-tree.

**FP-tree construction**. An FP-tree is a prefix tree that contains a compressed representation of the transaction database. Each transaction in the FP-tree is a overlapping path in the tree.

The first step before constructing the tree is sorting the itemsets to ensure the uniqueness of the prefixes.

1. The construction of the FP-tree starts from an empty node. Each node has a label corresponding to an item and a counter, corresponding to the number of transactions in the paths containing the item.
2. For each transaction follow the prefix-path and increase the counters on the way.
3. If at some point we reach a leaf node we expand the path adding nodes with counter 1.
4. Add a pointer between nodes that refer to the same item.

We add a header table corresponding to the first (from the left of the tree) correspondence of the item.

The final size of the tree depends on the redundancy in the transactions. In the best case, all transaction are the same and the FP-tree is a path. In worst case, all the transactions are different and the size of the tree is bigger than that of the data.

**FP-Growth** is an algorithm that takes the FP-tree as input and performs a divide-and-conquer strategy to enumerate all the frequent itemsets.

## Pseudo-code

### Itemsets mining with FP-Tree

Input: All frequent itemsets and their support

Output: The FP-tree, minimum support $minsup$

1. Proceed in divide-and-conquer manner
2. for each Items $i\in\mathcal I$ do
   - Consider candidate itemsets $C$ ending with $i$
   - Recursion on $C$

The meta-algorithm works bottom-up. This means that for each possible last item, consider itemsets with last items one of items preceding it in the ordering.

!!! warning

    The items are lexicographically ordered and the header-table contains the first occurrence of an item.



### FP-Growth

Input: All frequent itemsets and their support

Output: The FP-tree, minimum support $minsup$

1. Proceed in divide-and-conquer manner
2. for each suffix $X$ do
   - Construct a prefix-tree for $X$, compute support using header table and pointers
   - If $X$ is frequent then
     - Recompute support
     - Prune infrequent items
     - Prune leaves and recur

!!! warning

    However, the items counter of the items need to be updated as the global FP-tree contains the counters for all transactions, including those not ending with the considered suffix. The counters propagates bottom-up from the leaves to the root.



Once the counters are updated, if any of the labels have support less than $minsup$ the node is pruned and the subtree of the node is attacked to the node's father. After pruning, the suffix is removed from the tree and the computation proceeds recursively bottom-up.

## Pros and Cons

!!! success

    - Efficient
    - Allows to compute support along with the frequent items.



!!! failure

    Efficiency depends on the compaction factor
    If the tree is bushy, the number of sub-problems to solve is large

