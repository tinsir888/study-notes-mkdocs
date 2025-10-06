---
title: 数据挖掘 10 Graph Neural Networks
author: tinsir888
date: 2024/4/8
cover: /img/AUdm.jpg
katex: true
tags:
  - 笔记
  - 数据挖掘
categories:
  - AUDatalogi
  - 数据挖掘
abbrlink: '484130e5'
---

GNN - graph neural networks

Intuition: how to propagate information over the neighbors in a non-linear manner.

# Graph Embeddings as Matrix Factorization

Linear embeddings correspond to SVD of the similarity matrices chosen in the objective.

For adjacency-based similarity, the best linear $d$-dimensional embedding corresponds to the projection of the dataset into the $d$ directions associated to the top-$d$ singular values:
$$
\text{if }SVD(\mathbf A)=\mathbf U\Sigma\mathbf V^\top\\
\text{then the embedding matrix }\mathbf Z=\mathbf U_d\Sigma_d
$$
Question: Does there exist a matrix that, once factorized, would be approximately equivalent to run gradient descent over the non-linear objective?

## NetMF

DeepWalk, one of the most popular random-walk embedding methods.

A random-walk approach samples random walks using a certain strategy $R$.

A random-walk embedding method optimizes the **likelihood** that a node $j$ is reachable from a random-walk starting from node $i$.

DeepWalk's strategy is to generate random-walks of length $L$ and then generate pairs of co-occuring nodes in a window of size $T$. The co-occurences  are stored in a multiset $\mathcal D$.

Pseudo-code:

- for each $n=1,2,\cdots,N$ do
  - pick $w_1^n$ according to probability distribution $p(w_1)$
  - generate vertex sequence $(w_1^n,\cdots,w_L^n)$ of length $L$ by random walk on network $G$
  - for each $j=1,2,\cdots,L-T$ do
    - for each $r=1,\cdots,T$ do
      - add vertex-context pair $(w_j^n,w_{j+r}^n)$ to multi-set $\mathcal D$
      - add vertex-context pair $(w_{j+r}^n,w_{j}^n)$ to multi-set $\mathcal D$

The first vertex of the random-walk $w_1^n$ is sampled from a prior distribution $p(w_1)$ that in undirected, connected bipartite graphs correspond to $p(i)=d_i/2|E|$.

This process and the relative transformation through softmax corresponds to a similarity matrix. We can represent the similarity among two nodes $i,j$ as
$$
\log\left(\frac{\text{(# of RW between i and j)}\cdot\text{(# of node/context pairs)}}{k\text{(# of occurrences of i)}\cdot\text{(# of occurrences of j)}}\right)\\
=\log\left(\frac{\#(i,j)|\mathcal D|}{k\#(i)\#(j)}\right)
$$
where $k$ is the number of negative samples.

Understand this formula:

- First partition the set of multi-set $\mathcal D$ of pairs of nodes co-occurring in walks into two set $\mathcal D_{r\rarr}$ and $\mathcal D_{r\larr}$
  $$
  \mathcal D_{r\rarr}=\{(w,c):(w,c)\in\mathcal D,w=w_j^n,c=w_{j+r}^n\}\\
  \mathcal D_{r\larr}=\{(w,c):(w,c)\in\mathcal D,c=w_{j+r}^n,w=w_j^n\}
  $$

- Then observe that
  $$
  \log\left(\frac{\#(i,j)|\mathcal D|}{k\#(i)\#(j)}\right)=\log\left(\frac{\#(i,j)\div|\mathcal D|}{k(\#(i)\div|\mathcal D|)(\#(j)\div|\mathcal D|)}\right)\\
  \#(i,j)\div|\mathcal D|=\frac{1}{2T}\sum_{r=1}^T\left(\frac{\#(i,j)_{r\rarr}}{|\mathcal D|_{r\rarr}}+\frac{\#(i,j)_{r\larr}}{|\mathcal D|_{r\larr}}\right)
  $$
  where the latter observation comes from $|\mathcal D_{r\rarr}|/|\mathcal D|=|\mathcal D_{r\larr}|/|\mathcal D|=\frac{1}{2T}$.

- The observe what happens as the length of random walk $L\rarr\infty%$. We first define the random walk matrix $\mathbf P=\mathbf\Delta^{-1}\mathbf A$. The volume of a graph $G$ is the sum of the entries of adjacency matrix $\text{vol}(G)=\sum_i\sum_ja_{i,j}$. There are 3 important Lemmas:
  $$
  \frac{\#(i,j)_{r\rarr}}{|\mathcal D|_{r\rarr}}\stackrel{p}\rarr\frac{d_i}{\text{vol(G)}}(\mathbf P^r)_{i,j}\and \frac{\#(i,j)_{r\larr}}{|\mathcal D|_{r\larr}}\stackrel{p}\rarr\frac{d_i}{\text{vol(G)}}(\mathbf P^r)_{j,i}\\
  \frac{\#(i,j)}{|\mathcal D|}\stackrel{p}\rarr\frac{1}{2T}\sum_{r=1}^T\left(\frac{d_i}{\text{vol(G)}}(\mathbf P^r)_{i,j}+\frac{d_i}{\text{vol(G)}}(\mathbf P^r)_{j,i}\right)\\
  \frac{\#(i,j)|D|}{\#(w),\#(c)}\stackrel{p}\rarr\frac{\text{vol}(G)}{2T}\left(\frac{1}{d_j}\sum_{r=1}^T(\mathbf P^r)_{i,j}+\frac{1}{d_i}\sum_{r=1}^T(\mathbf P^r)_{j,i}\right)
  $$

- Putting all together we obtain DeepWalk with infinite length random walks converges to
  $$
  \log\left(\frac{\text{vol}(G)}{k}(\frac{1}{T}\sum_{r=1}^T(\mathbf\Delta^{-1}\mathbf A)^r)\mathbf\Delta^{-1}\right)
  $$
  Notice that the internal sum reminds close to a random walk iteration as the power of matrix $\mathbf \Delta^{-1}\mathbf A$ encodes the probability of reaching nodes in $r$ steps through random walking the graph. By factorizing such a similarity with SVD we can obtain the desired result that approximates the embeddings of a random-walk approach.

# Graph Neural Network

abbr GNN

An architecture that allows nodes to propagate information to the neighbors in a non-linear manner. We have an attributed graph $G=(V,E,\mathbf X)$ with matrix of node features $\mathbf X\in\mathbb R^{m\times n}$. (such a matrix encodes nodes attributes such as labels, profiles or characteristics)

The main ingredient of a GNN is the aggregation function that takes in input the features from the neighbors of a node $i$ and finds an embedding $\mathbf h_i$.

## Neighborhood Aggregation

Main idea: Generate node embeddings based on local neighborhoods of each node, using neural networks to represent the complex relationships of a graph.

Intuitively, think of the neighbors as a computational graph of a Neural Network.

The embeddings in the layer $l$ is an aggregation of the embeddings in layer $l-1$.

How to aggregate information across the layers? A simple approach: average neighbor information and apply a neural network.

The embeddings of the initial layer $0$ are the attributes $\mathbf z_i^0=\mathbf z_i$. The $l$-th layer embedding of $i$ is
$$
\mathbf z_i^l=\sigma(\mathbf W_l\sum_{j\in N(i)}\frac{\mathbf z_j^{l-1}}{|N(i)|}+\mathbf B_l\mathbf z_i^{l-1}),\forall t\gt0
$$
$\sigma$ is non-linear activation function. The sum is the average of each neighbor's embeddings compute in layer $l-1$.

**Loss function**. $\mathbf {W,B}$ are the trainable weight matrices. After $k$-layers of neighborhood aggregation, we get output embeddings for each node. Loss function needs non-linear and differentiable.

We optimize the parameters of model through stochastic gradient descent SGD.

Rewrite model in matrix notation
$$
\mathbf Z^{l+1}=\sigma(\mathbf Z^l\mathbf W^l+\tilde{\mathbf A}\mathbf Z^l\mathbf W^l_1)
$$
where $\tilde{\mathbf A}=\mathbf\Delta^{-1/2}\mathbf {A\Delta}^{-1/2}$. To optimize the parameters, a **cross-entropy** loss function would work in a supervised setting w.r.t. node's labels.

An important properties of models: ability to share the parameters $\mathbf {W,B}$ and allow for generalization to unseen nodes, edges or graphs.

Depend on loss function and embedding matrix, the GNN can learn node embeddings, graph embeddings or clusters.

## Graph Convolutional Networks

abbr. GCN

A different neighbor aggragation
$$
\mathbf z_i^l=\sigma\left(\mathbf W_l\sum_{j\in N(i)\cup i}\frac{\mathbf z_j^{l-1}}{\sqrt{|N(i)||N(j)|}}\right).
$$
The main idea is node with a large number of connections should be less important. There is no need for the bias term $\mathbf B$ and the per-neighbor normalization.

Normalization empirically attains better results and better parameter sharing. We can efficiently compute the embeddings using sparse batch operations
$$
\mathbf Z^{l+1}=\sigma(\mathbf\Delta^{-1/2}\tilde{\mathbf A}\mathbf\Delta^{-1/2}\mathbf Z^l\mathbf W_l)
$$
where $\tilde{\mathbf A}=\mathbf A+\mathbf I$ and $\delta$ is the diagonal degree matrix. The time complexity to propagate the information is $\mathcal O(|E|)$.

## GraphSAGE

Instead of fixed aggregation, GraphSAGE use any differentiable function that maps set of vectors to a single vector. So, if we use a generalized aggregation, we get
$$
\mathbf z_i^l=\sigma([\mathbf W_l\cdot AGG(\{\mathbf z_j^{l-1},\forall j\in N(i)\}),\mathbf B_l\mathbf z_i^{l-1}])
$$
Concentrate self embedding of neighbor embedding. Also we use a generalized aggregation.

Some variants:

- Mean. Take weighted average of neighbors:
  $$
  AGG=\sum_{j\in N(i)}\frac{\mathbf z_j^{l-1}}{N(i)}
  $$

- Pool. Transform neighbor vectors and apply symmetric vector function:
  $$
  AGG=\gamma(\{\mathbf{Qz}_j^{l-1},\forall j\in N(i)\})
  $$
  where we take element-wise mean/max

- LSTM. Apply LSTM to reshuffled neighbors $\pi(N(i))$
  $$
  AGG=LSTM([\mathbf z_j^{l-1},\forall j\in\pi(N(i))])
  $$
  