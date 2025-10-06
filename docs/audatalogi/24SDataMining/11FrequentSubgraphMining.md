---
title: 数据挖掘 11 Frequent Subgraph Mining
author: tinsir888
date: 2024/4/15
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: a2b14dff
---

A labeled graph is a triple $G=(V,E,\ell)$ where $\ell:V\cup E\rarr L$ is a labeling function assigning a label from a set of labels $L$ to each node and each edge.

Definition of frequency: it is encoded into a *support* measure that return the number of occurrences of the subgraph in the graph.

# FSM in Graph Collections

A **graph collection** aka a **graph database** is a set $\mathcal D=\{G_1,G_2,\cdots,G_n\}$ of labeled graphs $G_i=(V_i,V_i,\ell_i),i\in[1,n]$.

Since a subgraph is uniquely identified by the nodes and edges of a certain graph, we need to relax the definition, so that it counts the occurrences of that subgraph in different graphs.

A graph $G_1(V_1,E_1,\ell_1)$ is **isomorphic** 同构 to a graph $G_2=(V_2,E_2,\ell_2)$ if there exists a bijective function $f:V_1\rarr V_2$ such that if $u\in V_1$ then $f(u)\in V_2$ and $\ell_1(u)=\ell_2(f(u))$ and if $(u,v)\in E_1$ then $(f(u),f(v))\in E_2$ and $\ell_1((u,v))=\ell_2((f(u),f(v)))$.

If a graph $G_1$ is isomorphic to a subgraph of another graph $G$, we say $G_1$ is **subgraph isomorphic** to $G$. Subgraph isomorphism is NP-complete, which graph isomorphism is NP.

Denote: $G\sqsubseteq G_1$ a subgraph isomorphism from $G$ to $G_1$.

Given a collection of graphs $\mathcal D=\{G_1,G_2,\cdots,G_n\}$ and a graph $G$, the support set $\mathcal D_G$ is the subset of $\mathcal D$, s.t. $G$ is subgraph isomorphic to every graph in $G'\in\mathcal D_G$, i.e., $\mathcal D_G=\{G'\in\mathcal D|G\sqsubseteq G'\}$. The **support** $\sup(G)$ of $G$ is the proportion $\sigma(G)=\frac{|\mathcal D_G|}{|\mathcal D|}$.

The Frequent Subgraph Mining FSM problem in collection of graphs $\mathcal D=\{G_1,G_2,\cdots,G_n\}$ finds all the subgraphs $G$ having at least support $\min\sup$, i.e., $\sigma(G)\ge\min\sup$.

**Downward Closure Property**

- Downward closure is the property under which a support measure decreases by expanding a pattern. In particular in our case $\sup(G)\le\sup(G')$ if $G'\sqsubseteq G$.

The downward closure property leads to 2 different family of approaches: **apriori-based** approaches and **pattern-growth** approaches.

## Apriori-based Approaches

This approaches stem from the observation that any subgraph of a frequent subgraph must be frequent as well.

**FSG** is one of the first methods that utilizes the apriori principle. The algorithm builds on the idea that a frequent $k$-subgraph is built on top of frequent $(k-1)$-subgraphs, where $k$ indicates the number of edges.

The algorithm simply starts enumerating frequent $1$-subgraphs and $2$-subgraphs and store them into the set $F_1,F_2$ respectively.

Similarly, $F_k$ is the set of frequent $k$-subgraphs. FSG then builds larger $k$-subgraphs by merging the subgraphs obtained in the previous iteration.

Pseudo code

1. $F_1\gets$ frequent $1$-subgraphs with counts
2. $F_2\gets$ frequent $2$-subgraphs with counts
3. $k\gets3$
4. while $F_{k-1}\neq\empty$ do
   - `candidate_generation`: get $C_k$, the set of candidate $k$-subgraphs from $F_{k-1}$
   - `candidate_pruning`: for a candidate to be frequent, each $(k-1)$-subgraph should be frequent
   - `frequency_pruning`: scan $\mathcal D$, count occurrences of subgraphs in $C_k$
   - $F_k\gets\{c\in C_k|\sigma(c)\ge\min\sup\}$
   - $k\gets k+1$
5. return $\cup_{i=1}^kF_i$

each of 3 steps run isomorphism checks to understand whether some candidate is isomorphic to any of the other candidates. Moreover, in order to compute the support of a subgraph the algorithm runs subgraph isomorphism on all the candidate graphs in $C_k$. FSG employs heuristics to reduce the number of isomorphism tests at each iteration.

**Candidate Generation**

Generate candidates by merging the frequent $(k-1)$-subgraphs. The main idea is to first detect cores to generate the candidates easily. A core is the maximum common subgraph among two subgraphs. Although generating the core requires to run subgraph isomorphism, in practice

1. one core is common to many subgraphs.
2. the subgraphs are typically small.

**Candidate pruning**

In order to prune the candidates we can check if **every** subgraph of a candidate is also frequent. In theory, this requires to perform isomorphism tests for each of the candidate's subgraph.

In order to hash a graph, we require a signature, i.e., a numeric representation, that is unique for each graph. This is possible through **canonical labeling**. Canonical labeling, finds a unique representation of a graph in a smart manner. In particular, FSG empolys a canonical scheme that reorders the column and the rows of the adjacency matrix. The string obtained by reading the sequence of node and edge labels in the column of the matrix that is smallest lexicographic string among all of those obtained by reordering is the canonical code and is unique, i.e., $code(G)=\min code(M)|M$ is adjacency matrix.

{% note default modern %}

It seems that this method effectively solves graph isomorphism in $\mathcal O(1)$. However, this is not the case as the canonical labeling is obtained by a, potentially exponential, number of reordering of the adjacency matrix. By reordering, we are computing automorphisms over the graph. In practice, not all the permutations lead to a valid matrix and can be pruned by simple heuristics.

{% endnote %}

**Frequency Counting**

Frequency counting requires to scan the data to find which graphs contain a subgraph. In frequency counting, most of the subgraph isomorphism tests can be avoided with a simple shortcut.

Let $TID(G'),TID(G'')$ be the set of graphs in the collection $\mathcal D$ that contain the subgraph $G'$ and $G''$, respectively. If we merge the two patterns, it follows immediately that $TID(merge(G',G''))\subseteq TID(G')\cap TID(G'')$. As such all the graphs in $(TID(G')\cup TID(G''))\diagdown(TID(G')\cap TID(G''))$ can be ignored as they will not contribute to the support.

{% note success modern %}

:thumbsup:

- Finds all frequent subgraphs.
- Easy to include heuristics to reduce the computation time.

{% endnote %}

{% note danger modern %}

:thumbsdown:

- FSG generates the same subgraph a large number of times.
- The canonical labeling scheme is quadratic in the size of the subgraph.
- FSG requires several isomorphism and subgraph isomorphism tests.

{% endnote %}

## Pattern-growth Approaches

It follows a different philosophy.

Start first with empty subgraphs and progressively expand these subgraphs by adding one edge at the time. The pattern expands always in a certain order to redundancy as much as possible.

**gSpan** is the most famous algorithm in the pattern-growth family.

gSpan organizes the patterns in a *prefix-tree*, a data structure for alphabetically ordered string that store string prefixes as nodes of a tree. However, graphs do not have a predefined order of traversal, or a unique way of enumerating the nodes.

To create a prefix-tree of non-repeating patterns gSpan:

1. Defines a traversal over the nodes in the graphs
2. Identifies in advance which nodes of a subgraph to expand
3. Defines a total ordering over the subgraphs, so as to detect duplicate patterns.

**Node Traversal**

gSpan traverses the subgraphs in a DFS manner, that means that the next node $v_j$ to visit after $v_i$ is the first in the list of neighbors $N(i)$. A DFS vist defines a spanning-tree over the nodes in the subgraph. The edges in the spanning tree are called **forward edges**; the remaining edges are called **backward edges**.

In the beginning of the DFS a counter assigns a unique identifier to each node. The counter represents the discovery time of the node.

**Rightmost Expansion**

Since a DFS defines a specific traversal and the order of the visit is market by a counter, there is only one way to add an edge to the subgraph, namely expanding from the **rightmost path**. The rightmost path is the path from the root of the spanning tree to the leaf with the highest identifier.

**Total Ordering**

The main innovation of gSpan is to treat canonical labeling as a DFS-visit and thus ensure a reduced number of operations and redundant subgraphs.

### DFS Codes

Representation of a graph that is comparable among other DFS codes. Such a comparison as a total ordering for which a minimum exists. The **minimum DFS-code** is a canonical labeling of a graph.

**Definition of DFS-edge**. It's a tuple $(i,j,\ell(i),\ell((i,j)),\ell(j))$ where $i,j$ are identifiers given by the DFS traversal order, $\ell(i),\ell((i,j)),\ell(j)$ are the labels of node $i$, edge $(i,j)$ and node $j$.

**DFS-code** for a graph $G=(V,E,\ell)$ is a sequence $\langle e_1,\cdots,e_t\rangle$ where each $e_i$ is a DFS-edge and $t=|E|$.

**Valid DFS-codes**. Precedence order $\prec$ on DFS-edges corresponding to the DFS traversal. Given 2 DFS-edges $e_1$ connecting nodes with discovery time $(i_1,j_1)$ and $e_2$ connecting nodes with discovery time $(i_2,j_2)$. A DFS-code is **valid** if it complies to:

1. $i_1=i_2$ and $j_1\lt j_2\Rarr e_1\prec e_2$ backward edges ordering
2. $i_1\lt j_1$ and $j_1=i_2\Rarr e_1\prec e_2$ forward edges ordering
3. $e_1\prec e_2$ and $e_2\prec e_3\Rarr e_1\prec e_3$ transitive property

**Total ordering and minimum codes**. A graph can have multiple valid DFS-codes depending on the DFS traversal order. gSpan further defines a total ordering over the DFS-codes so to define a minimum DFS-code for a graph.

The minimum DFS-code is unique and a canonical labeling of the graph. This implies that 2 graphs are isomorphic iff they have the same DFS-code.

Let $\alpha,\beta$ two DFS-codes. We say $\alpha\prec\beta$ iff either of the following conditions are true:

- $\exist t,0\le t\le\min(n,m)$ s.t. $a_k=b_k$ for $k\lt t$ and $a_t\prec_e b_t$
- $a_k=b_k$ for $0\le k\le m$ and $n\ge m$

Simply speaking, $\alpha\prec\beta$ if either 1. $\alpha$ and $\beta$ are equal up to some point $t$ and the next corresponding DFS-edges is s.t. $a_t\prec_e b_t$, or 2. $\alpha$ and $\beta$ are the same in terms of DFS-edges but $\beta$ contains more edges.

We need to define $\prec_e$: Let $a_t=(i_a,j_a,L_{i_a},L_{i_a,j_a},L_{j_a}),b_t=(i_b,j_b,L_{i_b},L_{i_b,j_b},L_{j_b})$ be two DFS-edges. We say $a_t\prec_e b_t$ if

1. Both are forward edges
   - $i_b\lt i_a$ OR
   - $i_b=i_a$ and the labels of $a$ are lexicographical less than labels of $b$, in order of tuple.
2. Both are backward edges
   - $j_a\le j_b$ OR
   - $j_a=j_b$ and the edge label of $a$ is lexicographical less than the one of $b$.
3. $a_t$ is backward and $b_t$ is forward.

The above enforce a total order on DFS-codes and, as such, the min-DFS($G$) of a graph $G$ is a canonical labeling of $G$.

**DFS-code tree**. It allows also to organize data into DFS-tree that is prefix tree. Because the codes have a precedence order. If min-DFS($G_1$)$=\{a_0,a_1,\cdots,a_n\}$ and min-DFS($G_2$)$=\{a_0,a_1,\cdots,a_n,b\}$, then $G_1$ is parent of $G_2$, since min-DFS($G_1$) is a prefix of min-DFS($G_2$).

Given a parent-child relationship defined, we can construct a DFS Tree. An interesting property of the DFS-tree is that an in-order traveersal follows DFS lexicographic order.

**gSpan algorithm**

Input: Collection of graphs $\mathcal D$, minimum support $\min\sup$

Output: Frequent subgraph set $S$

1. Let $S$ be frequent one-edge subgraphs in $\mathcal D$ using DFS code.
2. Sort $S$ in lexicographic order
3. $N\gets S$
4. for each $n\in N$ do
   - `GSpanExtend`$(\mathcal D,n,\min\sup,S)$
5. return $S$

`GSpanExtend`$(\mathcal D,n,\min\sup,S)$ // checking minimality of DFS-code is computationally expensive

1. if $n$ not minimal then return

2. else

   - $S\gets S\cup n$

   - for each Rightmost expansion $e$ of $n$ do

     - if $\delta(e)\ge\min\sup$ then

       `GSpanExtend`$(\mathcal D,n,\min\sup,S)$

{% note success modern %}

:thumbsup:

- Finds all frequent subgraphs.
- Generates less redundant subgraphs.

{% endnote %}

{% note danger modern %}

:thumbsdown:

- Works only in collections of graphs.
- Generally slow for low $\min\sup$.

{% endnote %}

# Frequent Subgraph Mining on a Single Graph

If the support is defined a the number of time a specific subgraph appears, the downward property does not hold anymore as bigger subgraphs can have a larger support.

We defined a support measure to be *admissible* if for any graph $P$ and any subgraph $Q\sqsubseteq P$, the support of $P$ is not larger than the support of $Q$. In the last few years, a number of admissible support measures have appeared. These include,

- Maximum independent set support (MIS): Based on maximum number of non-overlapping matches
- Harmful overlap support (HO): Based on number of patterns for which no multi-node subgraph is identical
- Minimum image-based support (MNI): Based on the number of times a node in the pattern is mapped into a distinct node in the graph.

## MIS Support

The **m**aximum **i**ndependent **s**et support $\sigma_{MIS}$ counts how many times a pattern maps to a subgraph that does not overlap to any other subgraphs. The computation of MIS is as follows

1. Construct an overlap graph $G_O=(V_O,E_O)$, in which the set of nodes $V_O$ is the set of matches of a pattern $P$ into a graph $G$ and $E_O=\{(f_1,f_2)|f_1,f_2\in V_O\or f_1\neq f_2\neq f_1\sqcap f_2\neq\empty\}$, i.e., $E_O$ has an edge among each pair of overlapping matches.
2. Compute the $\sigma_{MIS}$ as the size of the maximum independent set of nodes in the overlap graph.

Recall that the maximum finding the maximum independent set of NP-hard.

## Harmful Overlap Support

The HO support is less restrictive than MIS support as it considers overlap only those matchings of a pattern that share one are more edges. Two matching subgraph are connected through a node are not considered overlapping.

## NMI Support

The minimum image-based support is even less restrictive as it considers the minimum number of times a node of the pattern matches a node in the graph.

**Definition of MNI**. Let $f_1,\cdots,f_m$ be the set of isomorphism of a pattern $P:\langle V_P,E_P,\ell_P\rangle$ in a graph $G$. Let $F(v)=|\{f_1(v),\cdots,f_m(v)\}|$ be the number of distinct mappings of a node $v\in V_P$ to a node in $G$ by functions $f_1,\cdots,f_m$. The MNI
$$
\sigma_{MNI}(P)\ of P\ in\ G\ is\ \sigma_{MNI}(P)=\min\{F(v),v\in V_P\}
$$
Since MNI considers one node at the time, given the matching of pattern in the graph, NMI support can be computed in polynomial time.

## Approaches for Large Graphs

With an anti-monotone support measure, we can apply any previous support measure such as gSPAN, just changing how the support is computed and leaving the rest unchanged. Such approaches are called grow-and-store

- Add all frequent edges to the list of frequent subgraphs
- Extend frequent subgraphs to larger candidates
- Compute candidate frequency (find all occurrences)
- Repeat step 2 until no more frequent subgraphs is found

One of the methods that exploits and optimizes gSpan is GraMi