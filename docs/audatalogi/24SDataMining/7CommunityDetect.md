---
title: 数据挖掘 7 Community Detection
author: tinsir888
date: 2024/3/11
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: a7aa7d79
---

Community detection finds sets of nodes, i.e., that are highly connected inside the community and coarsely connected outside the community.

Community: a group of individuals sharing common interests.

A community is a subset $C\subseteq V$ of the nodes $V$. Non-overlapping communities or partitions are pairwise disjoint set $C_i,C_j$ s.t. $C_i\cap C_j=\emptyset$ for each $i,j$. Overlapping communities are not necessarily pairwise disjoint.

# Non-overlapping Community Detection

Disjoint aka partitions.

Two common algorithms for non-overlapping community detection are Spectral clustering and Modularity optimization.

## Modularity Optimization

Modularity is a measure of cohesiveness 凝聚力 among communities.

Modularity $\mathcal Q$ compares the density inside communities with a null model of density. The null model assumes that nodes can form random connections with any other node.

$\mathcal Q\varpropto\sum_{C\in\mathcal C}$ \[number of edges within community $C$ - expected number of edges within community $C$\]

The main question is how do we quantify the edges within a community and what is a good null model.

**Edge within community**. In order to find how many edges belong to the same community we simply sum the edges in each community and normalize by the total number of edges.
$$
\frac{1}{2m}\sum_{C\in\mathcal C}\sum_{i\in C}\sum_{j\in C}a_{ij}
$$
We consider all pairs of nodes, we need to normalize by twice number of edges $2m$ as we count both $ij$ and $ji$.

**Modularity's null model**. Given a graph $G$ with $n=|V|$ vertices and $m=|E|$ edges, construct a rewired graph $G'$. The rewired graph has the same degree distribution but the connections among nodes appear at random. By ensuring the same degree, we assert that we do not compare the graph $G$ with a completely random one.

> Node with large degrees represents celebrities. Altering their degrees might end up transforming a star into a ordinary person.

If nodes are allowed to form any connection with any other node as long as the degree is preserved, the probability that given one node $i$ the other one $j$ is
$$
p_{ij}=\frac{d_id_j}{4m^2}
$$
where $d_i,d_j$ are the degrees of $i$ and $j$ respectively. The expected number of edges between nodes $i$ and $j$ is $2m\cdot p_{ij}=2mp_{ij}=\frac{d_id_j}{2m}$. As such, the expected number of edges in $G'$ is
$$
\frac{1}{2}\sum_{i\in V}\sum_{j\in V}\frac{d_id_j}{2m}=\frac{1}{2}\cdot\frac{1}{2m}\sum_{i\in V}d_i\sum_{j\in V}d_j\\
=\frac{1}{4m}2m\cdot2m=m
$$
**Modularity**. Putting all together, the Modularity of a partition $\mathcal C$ of $G$ is
$$
\mathcal Q(G,\mathcal C)=\frac{1}{2m}\sum_{C\in\mathcal C}\sum_{i\in C}\sum_{j\in C}(a_{ij}-\frac{d_id_j}{2m})
$$
This value is taken in $[-1,1]$. A modularity close to $1$ indicates a good partition, while a negative value indicates a partition in which connected nodes are separated. A strong community structure is usually considered between $\mathcal Q=0.3$ and $0.7$,

The direct modularity optimization is NP-hard. The Louvain algorithm propose a greedy algorithm that runs in $\mathcal O(n\log n)$. The Louvain method works in a bottom-up fashion by progressively merging the two communities that bring the largest increase in modularity.

### Spectral Modularity

A spectral method for modularity maximization.

First, with a bi-partition of the graph i.e. dividing the graph in 2 communities $C_1,C_2$. Let $\mathbf c\in\mathbb R^n$ be a real vector that indicates whether a node belongs to one or the other community. An entry $c_i$ is
$$
c_i=\begin{cases}
1\text{ if }v_i\in C_1\\
-1\text{ if }v_i\in C_2
\end{cases}
$$
We can rewrite equation for $\mathcal Q$ as
$$
\mathcal Q=\frac{1}{2m}\sum_{i,j}(a_{ij}-\frac{d_id_j}{2m})\delta(c_i,c_j)
$$
where $\delta(c_i,c_j)=1$ if $c_i=c_j$ and $0$ otherwise.

Notice that $\delta(c_i,c_j)=\frac{1}{2}(c_ic_j+1)$. Thus
$$
\mathcal Q=\frac{1}{4m}\sum_{i,j}(a_{ij}-\frac{d_id_j}{2m})c_ic_j=\frac{1}{4m}\mathbf c^\top\mathbf{Bc}
$$
where $\mathbf B$ with entries $B_{ij}=a_{ij}-\frac{d_id_j}{2m}$ is referred to as modularity matrix.

The objective is very close to spectral clustering. However, in this case we try to maximize the quantity. Since $\mathbf B$ is symmetric, the variational characterization of the eigenvalues tells that the solution of the optimization $\max\frac{\mathbf x^\top\mathbf{Mx}}{\mathbf x^\top\mathbf x}$ is the eigenvector that corresponds to the largest of the eigenvalues of the modularity matrix.



In order to compute the partition $\mathbf c$, we relax the vector $\mathbf c$ to take any real value. Our object (omitting $1\over4m$) becomes
$$
\max_\mathbf c \mathbf{c^\top Bc}\\
\text{s.t. }\mathbf{c^\top c}=n\\
\Rightarrow\frac{\partial}{\partial\mathbf c}\mathbf{c^\top Bc}=0\\
\Rightarrow\frac{\partial}{\partial\mathbf c}[\mathbf{c^\top Bc}-\beta(n-\mathbf{c^\top c})]\Rightarrow2\mathbf{Bc}=2\beta c
$$
The last equality is an eigenvalue - eigenvalue equation, where $\beta$ is an eigenvalue for the eigenvector $\mathbf c$. If we substitute $\mathbf{Bc}=\beta\mathbf c$, we will obtain
$$
\mathbf{c^\top Bc}=\mathbf{c^\top\beta c}=\beta\mathbf{c^\top c}=\beta n
$$
In order to maximize the objective we need to select the largest eigenvalue and the corresponding eigenvector.

Finally, the partition of the graph is obtained by
$$
c_i=\begin{cases}
1\text{ if }v_i\gt0\\
-1\text{ otherwise}
\end{cases}
$$
**Detecting multiple communities**. Similar to spectral clustering, we would be tempted to generalize the spectral modularity to the case of multiple communities by recursive bi-partitioning. This method, however, assumes that the graph is cut into two parts and that the edges among the two partitions are removed. Unfortunately, removing edges changes also the degree of the nodes, and in turns the null model that forms the matrix $\mathbf B$, ending up maximizing the wrong quantity. As a remedy, we consider the change $\Delta\mathcal Q$ in modularity after splitting a community $C$ into two parts
$$
\Delta\mathcal Q=\frac{1}{2m}[\frac{1}{2}\sum_{i,j\in C}B_{ij}(c_ic_j+1)-\sum_{i,j\in C}B_{ij}]\\
=\frac{1}{4m}[\sum_{i,j\in C}B_{ij}c_ic_j-\sum_{i,j\in C}B_{ij}]\\
=\frac{1}{4m}\sum_{i,j\in C}[B_{ij}-\delta_{ij}\sum_{k\in C}B_{ik}]c_ic_j\\
=\frac{1}{4m}\mathbf c^\top \mathbf B^{(C)}\mathbf c
$$
where $\delta_{ij}=1$ if $i=j$ and $0$ otherwise, is called the Kronecker delta and $B_{ij}^{(C)}=B_{ij}-\delta_{ij}\sum_{k\in C}B_{ik}$.

The above is generalized spectral method, in which we can repeatedly find eigenvectors in the modified matrix $\mathbf B^{(C)}$. It also provides a convenient stopping criteria: Once the increment in modularity is negative, there is no other community that will increase the modularity further and the method can stop.

## Pros and Cons

:thumbsup:

- Clusters can have arbitrary shape and size, i.e., clusters are not restricted to have convex shapes.
- Efficient in common real graphs.
- Robust to noise.

:thumbsdown:

- Inefficient on dense graphs with $\mathcal O(n^3)$ in the worse case.
- Resolution limit: When merging communities if the number of edges is larges.

# Overlapping Community Detection

Nodes can be shared among communities. Harder than Non-overlapping.

## Clique Percolation

Clique percolation detects overlapping communities by exploiting the definition of clique.

A **clique** is a fully connected subgraph. In other words, a clique is a set of nodes that have an edge with any other node in the clique.

A $k$-clique is a complete subgraph on $k$ nodes.

A $k$-clique is adjacent to another clique if it shares $k-1$ vertices. In clique percolation we define a community the union of adjacent $k$-cliques. Given such a definition it's easy to formulate our first algorithm **CFinder**.

The CFinder algorithm starts with a $k$-clique defining a community, where $k$ is a user parameter, and expands the community until there is no more adjacent cliques to add.

### Pseudo Code

Input: Graph $G$, Size of cliques $k$

1. Start with a $k$-clique
2. Roll the clique over adjacent cliques
3. A $k$-cliques community is the largest subgraph obtained by the union of all adjacent $k$-cliques
4. Other $k$-cliques that can not be reached correspond to other clique-communities

### Pros and Cons

:thumbsup:

- Easy to implement and understand
- Finding $k$-cliques is polynomial
- Communities are easily explainable

:thumbsdown:

- Requires size of cliques: $k$
- Rigid communities: the results depend on the existence of the cliques.

## AGM/BigCLAM

This method instead take a probabilistic approach.

AGM and BigCLAM are two different models but with very similar premises.

Intuition: graph is a sample of an unknown distribution in which we flip a coin on the existence of an edge.

The existence probability is determined by the community structure: the more communities two nodes share, the higher the probability.

Given a graph $G$ with communities $\mathcal C$ each of them existing with probability $p_C$, a membership set $M_i=\{C\in\mathcal C|v_i\in C\}$. The probability of existence of an edge in this model is
$$
P(i,j)=1-\prod_{C\in M_i\cap M_j}(1-p_C)
$$
The above probability ensures that an edge exists if the nodes share at least one community. The above is an instance of a noisy-OR. If the nodes share no community, we assign a fixed small probability $\epsilon$.

Our model assumes that each edge is generated independently by any other edge. As such, given the parameters $\Theta=(\mathcal C,M,\{p_C\})$, the likelihood that our graph $G$ is sampled by the AGM model is
$$
P(G|\mathcal C,M,\{p_C\})=\prod_{(i,j)\in E}P(i,j)\prod_{(i,j)\notin E}(1-P(i,j))
$$
AGM is a rather flexible model and can express a variety of community structures, such as non-overlapping and hierarchical communities.

The model assumes that we know already the parameters in order to compute the likelihood of a network.

By maximizing this equation, we can find the parameters of the model that maximize the likelihood. Bit how to find this model? Finding $\Theta$ means finding a community membership for each node. The task is hard.

**From AGM to BigCLAM**. AGM model is hard to optimize. BigCLAM drops some of the requirements of AGM to attain efficiency.

Under BigCLAM each node belongs to some community $C_j$ with some strength $F_{ij}$. If $F_{ij}=0$, node $i$ is not a member of community $j$. The probability $p_C$ that an edge between $i$ and $j$ exists if they belong to community $C$ is
$$
p_C(i,j)=1-\exp(-F_{iC}F_{jC}).
$$
This means that the probability that an edge exists is proportional to the product of the strengths. If one node does not belong to the community $C$ ($F_{iC}=0$) then its probability is $0$. The probability that at least one common community $C$ links two nodes is
$$
P(i,j)=1-\prod_C(1-p_C(i,j))=1-\exp(-\mathbf F_{i\cdot}\mathbf F_{j\cdot}^\top)
$$
where $\mathbf F_{i\cdot}$ is the row vector of the matrix $\mathbf F\in\mathbb R^{n\times|C|}$.

To compute $\mathbf F$ to fit the model to the data, we use a maximum likelihood approach
$$
\arg\max_\mathbf F\prod_{i,j\in E}p(i,j)\prod_{(i,j)\notin E}(1-p(i,j)).
$$
Instead of maximizing the likelihood, we maximize the log-likelihood
$$
P(G|\mathbf F)=\sum_{(i,j)\in E}\log(1-\exp(-\mathbf F_{i\cdot}\mathbf F_{j\cdot}^\top))-\sum_{(i,j)\notin E}(\mathbf F_{i\cdot}\mathbf F_{j\cdot}^\top).
$$
Since matrix $\mathbf F$ is real, the model can be optimized with coordinate ascent over the rows.

**Efficiency optimization**. The gradient can be optimized by caching and compute an iteration in linear time.
$$
\nabla P(G|\mathbf F)=\sum_{v\in N(i)}\mathbf F_v\cdot\frac{\exp(-\mathbf F_{i\cdot}\mathbf F_{v\cdot}^\top))}{1-\exp(-\mathbf F_{i\cdot}\mathbf F_{v\cdot}^\top))}-\sum_{v\notin N(i)}\mathbf F_v.
$$
The factor $\sum_{v\notin N(i)}\mathbf F_v$ can be written as
$$
\sum_v\mathbf F_{v\cdot}-\mathbf F_{i\cdot}-\sum_{v\in N(i)}\mathbf F_{v\cdot}
$$
which runs in linear time as both $\sum_v\mathbf F_{v\cdot}-\mathbf F_{i\cdot}$ can be cashed in advance.

### Pros and Cons

:thumbsup:

- Detect overlapping and non-overlapping communities
- Cluster can have arbitrary shapes
- Nearly linear Complexity
- Flexible model.

:thumbsdown:

- Requires the number of communities upfront
- Hard to find the threshold for determining the membership of each nodes.