---
title: 数据挖掘 9 Graph Embedding
author: tinsir888
date: 2024/4/1
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: 9cddf924
---

Main problem with graphs: lack of predefined node ordering.

Graph is non-Euclidean: the distance among two nodes does not depend on the coordinates of the nodes.

Origin idea of graph embedding: graph could become a set of multi-dimensional points in which Euclidean distances represent similarities among the nodes.

2 methods of embedding:

1. Linear embedding: linear transformation of the edges.
2. Random-walk embedding: preserving the probability that a node is reachable from another node.

# Linear Embeddings

Simplest form of graph embeddings.

Let $\mathbf z_i\in\mathbb R^d$ the embedding vector of the node $i$.

The similarity between vector $\mathbf z_i,\mathbf z_j$ is $sim(i,j)\approx\mathbf z_i^\top\cdot\mathbf z_j$. If two nodes are "close" under some definition of similarity, they should be also similar in the embedding space.

To define embedding vectors, we need

1. An encoder from nodes to the embedding space.
2. A similarity among nodes in the graph.

Then we need to define an objective or loss function, s.t. the embeddings are the parameters of optimization. Depending on the loss function, we can then use the appropriate optimizer to find the embeddings $\mathbf z_i$

**Shallow encoding**. We define the embedding matrix $\mathbf Z^{n\times d}$ in which row $i$ is the embedding $\mathbf z_i$ of node $i$. Given $\mathbf Z$ we can encode each node in its corresponding embedding by a lookup on the embedding matrix. Let $\mathbf v\in\mathbb I^n$ the indicator vector that has a $1$ in position $i$ and $0$ otherwise. Then

$$
\mathbf z_i=\mathbf Z^\top\mathbf v
$$

**Node similarity**. Intuitively check whether two nodes connected, are far form each other in shortest-path, are reachable from random walk, or simply share any common information.

## Adjacency-based Similarity

The simplest: two nodes are similar if they share an edge between them.

$sim(i,j)=a_{ij}$ implies the dot-product among the embedding vectors should approximate the entries of the adjacency matrix $a_{ij}\approx\mathbf z_i^\top\mathbf z_j$.

We then find embedding that minimize the distance among the entries $a_{ij}$ and the dot-product among the embedding vectors $\mathbf z_i^\top\mathbf z_j$:
$$
\mathcal L=\sum_{(i,j)\in V\times V}|\mathbf z_i^\top\mathbf z_j-a_{ij}|^2
$$
which corresponds to the minimization of the norm between $\mathbf A$ and the matrix $\mathbf{ZZ^\top},||\mathbf{ZZ^\top-\mathbf A||}_F^2$.

In order to minimize the objective, we use gradient descent or matrix decomposition (such as SVD or QR-decomposition) on $\mathbf A$.

### Pros and Cons

:thumbsup:

1. The similarity function is straightforward
2. Many methods for solving the optimization problem.

:thumbsdown:

1. $\mathcal O(n^2)$ running time to consider all pairs, $\mathcal O(|E|)$ if we sum only over the non-zero entries.
2. $\mathcal O(dn)$ parameters, one vector for each node.
3. Only consider direct and local connections through the adjacency matrix.

## Multi-hop  Similarity

Connections at distance $k$ from a certain node to remedy to the strict requirements of the adjacency-based similarity.

The $k$-th power of the adjacency matrix captures $k$-hop connections. Indeed the position $i,j$ in $\mathbf A^k$ represents the number of paths of lengths $k$ from node $i$ to node $j$.

Similarly to the case above loss function minimizes the differences between the entry $i,j$ in the $k$-th power of the adjacency matrix and the dot-product among the embedding vectors of the nodes $i,j$.

$$
\mathcal L=\sum_{(i,j)\in V\times V}|\mathbf z_i^\top\mathbf z_j-a_{ij}^k|^2
$$

Similarly, this corresponds to minimizing $||\mathbf{Z^\top Z}-\mathbf A^k||_F^2$. In practice, to avoid numerical explosion, GraRep use a log-transformed probabilistic adjacency matrix

$$
\tilde a_{ij}^k=\max\{\log(\frac{a_{ij}/d_i}{\sum_{l\in V}(a_{lj}/d_l)})^k-\alpha,0\}
$$

where $d$ is node degree, and $\alpha$ is a constant shift. GraRep trains embeddings with different $k$ and concatenates the embedding vectors.

A more flexible option to capture multi-hop similarities relies on the overlap between node neighborhoods, using overlap functions such as *Jaccard Similarity* and *Adamic-Adar score* which is $s_{ij}=\sum_{l\in N(i)\cap N(j)}\frac{1}{\log N(l)}$. The loss function becomes

$$
\mathcal L=\sum_{(i,j)\in V\times V}|\mathbf z_i^\top\mathbf z_j-s_{ij}|^2
$$

One drawback with Linear embedding is, in their current form, they require to compute the pairwise similarities for all nodes, taking $\mathcal O(n^2)$ in the worst case. For large graphs, a quadratic complexity is not feasible. Moreover. we need to define the similarity beforehand.

# Neural Embeddings

Alternative to the pairwise computation by exploiting the efficiency of probability distributions.

Random-walk based embeddings, non-linear similarity embeddings

## Random Walk Embeddings

Let $\mathbf z_i^\top\mathbf z_j$ be the probability that node $i$ and node $j$ co-occur in a random walk over the graph. We can estimate the probability of visiting node $i$ from node $j$ using random-walk or methods like Personalized PageRank. If we sample a large enough number of random walks. the process converges to the PageRank.

Random walks are efficient as they do not require to traverse the entire graphs, they can be easily simulated and they converge to probability distributions.

Assume we have a strategy $R$ to sample random walks. We can define our embedding algorithm:

1. Run short random walk starting from each node using strategy $R$.

2. For each node $i$, collect $N_R(i)$, the multi-set of nodes visited on random walks starting from $i$

3. Optimize the embeddings according to

   $$
   \mathcal L=\sum_{i\in V}\sum_{j\in N_R(i)}-\log(p(j|\mathbf z_i))
   $$

The optimization is to find embeddings that maximize the likelihood of random walk co-occurrences. We can parametrize $p(j|\mathbf z_i)$ by using softmax

$$
p(j|\mathbf z_i)=\frac{\exp(\mathbf z_i^\top\mathbf z_j)}{\sum_{l\in V}\exp(\mathbf z_i^\top\mathbf z_l)}
$$

The above loss can be optimized using gradient descent. However, the nested sum takes $\mathcal O(n^2)$, although the real bottleneck is the denominator of the probability, that sums over all nodes. One strategy to reduce the complexity is to employ a *negative sampling* approach.

Negative sampling treats the problem as a classification problem and splits the fraction into two parts.

$$
\log\frac{\exp(\mathbf z_i^\top\mathbf z_j)}{\sum_{l\in V}\exp(\mathbf z_i^\top\mathbf z_l)}\sim\log(\sigma(\mathbf z_i^\top\mathbf z_j))-\sum_{l=1}^k\log(\mathbf z_l^\top\mathbf z_{n_l})
$$

where $n_l\sim p_V$ is a node sampled from a negative distribution $p_V$ over the nodes and $\sigma$ is the sigmoid function. A typical choice of the negative distribution is to sample negative nodes proportional to the degree of each node. The more we sample, the more robust is the approximation. A higher $k$ corresponds to higher prior on negative events.

### Pros and Cons

:thumbsup:

1. Expressive and non-linear
2. Scale up with sampling techniques

:thumbsdown:

1. Similarity is fixed to random walks

## General Similarities

Another approach to graph embedding is to generalize the similarity-based approaches to any *samplable* similarity. An appealing choice for similarity is PPR. For a given restart vector $\mathbf v$ for node $i$ the PPR $\mathbf r$ is

$$
\mathbf r_i=(\mathbf I-\alpha\mathbf{A\Delta}^{-1})^{-1}(1-\alpha)\mathbf v
$$

from which we obtain the PPR-matrix

$$
\mathbf S=((1-\alpha)\mathbf I)(\mathbf I-\alpha\mathbf{A\Delta}^{-1})^{-1}
$$

where each row $i$ represents the PPR vector $\mathbf r_i$.

One choice to embed $\mathbf S$ is to use SVD; however this embedding method is again linear. We instead consider each row $\mathbf S_i$ separately as probability distributions to reach any node from node $i$. This is the approach of VERSE. VERSE computes the embedding matrix $\mathbf Z$ to preserve the distribution of the similarities node by node.

VERSE optimize the KL-divergence between the distribution of node $i$ and node $j$, where the KL-divergence between distribution $p$ and $q$ is

$$
D(p||q)=\sum_xp(x)\log\frac{p(x)}{q(x)},
$$

Typically, with KL-divergence the distribution $p$ is fixed and given as input, while the distribution $q$ is the one to learn. If that is the case, in order to learn $p$, notice that

$$
D(p||q)=\sum_xp(x)\log p(x)-p(x)\log q(x)
$$

Entropy of $p$ minus Cross entropy

The first term, the entropy of $p$ is constant if we minimize for $q$. So it suffices to reduce objective to $-\sum_x p(x)\log q(x)$.

For each node $i$ we can parametrize $q(i)$ as the similarity one-to-all in embedding space given by softmax of the dot-product of the embeddings

$$
sim(i,\cdot)=\frac{\exp(\mathbf{Zz}_i)}{\sum_{j=1}^n\exp(\mathbf z_i^\top\mathbf z_j)}
$$

Notice the numerator multiplies for the entire embedding matrix $\mathbf Z$. The final loss becomes

$$
\mathcal L=-\sum_{i\in V}\mathbf S_i\log sim(v,\cdot)
$$

$sim(i,\cdot)$ can benefit from a strategy similar to negative sampling. Another alternative is to use Noise Contrastive estimation and change the loss function into.

$$
\mathcal L=\sum_{i\sim\mathcal P,j\sim\mathbf S_i}[\log p(D=1|sim(i,j))+s\mathbb E_{\tilde j\sim Q(i)}\log p(D=0|sim_E(i,\tilde j))]
$$