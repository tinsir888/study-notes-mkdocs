---
title: APS 重点复习课程之人工智能导论
author: tinsir888
date: 2022/10/21
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 人工智能导论
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 9805f71d
---

# Overview

Brief Introduction

Logic and Reasoning

Searching

Supervised Learning

Unsupervised Learning

Statistics and Machine Learning

Deep Learning

Reinforcement Learning

Game Theory

# Brief Introduction

Symbolism, connectionism(Deep Learning), and behaviorism(Reinforcement Learning)

# Logic and Reasoning

## Propositional calculus

命题逻辑

### Basic

Every proposition is True or False.

connectives 逻辑连接词

and, or, not, conditional, bi-conditional

$\land,\lor,\neg,\rightarrow,\leftrightarrow$

compound proposition 复合命题

### Proposition Logic

$(a\Rightarrow b)\equiv(\neg b\Rightarrow\neg a)$, Contraposition

$(a\Rightarrow b)\equiv(\neg a\lor b)$

$\neg(a\land b)\equiv(\neg a\lor\neg b)$, Law of De Morgan 1

$\neg(a\lor b)\equiv(\neg a\land\neg b)$, Law of De Morgan 2

$(a\Rightarrow b,a)\Rightarrow b$, Modus Ponens 假言推理

$(a_1\land a_2\land\cdots\land a_n)\Rightarrow a_i$, And-Elimination

$(a_1,a_2,\cdots,a_n)\Rightarrow(a_1\land a_2\land\cdots\land a_n)$, And-Introduction

$\neg\neg a\Rightarrow a$, Double-Negation Elimination

$(a\lor b,\neg b)\Rightarrow a$, Unit Resolution

### Normal Form

CNF, Conjunctive Normal Form: $(\lor)\land(\lor)\land(\lor)$

DNF, Disjunctive Normal Form: $(\land)\lor(\land)\lor(\land)$

## Predicate logic

谓词逻辑 aka First-order logic

individual, predicate, quantifier

universal quantifier $\forall$

existential quantifier $\exists$

### Laws

Universal Instantiation, UI: $(\forall x)A(x)\rightarrow A(y)$

Universal Generalization, UG: $A(y)\rightarrow(\forall x)A(x)$

Exsitential Instantiation, EI: $(\exists x)A(x)\rightarrow A(c)$

Exsitential Generalization, EG: $A(c)\rightarrow(\exists x)A(x)$

## Knowledge Graph

A graph contains multi-relation.

Each edge is a triplet (left node, relation, right node)

## Causal Inference

因果推理

Simpson's Paradox: a relationship between two variables appears to be present in different  groups of data, but disappears or reverses when the groups are combined.

# Searching

## Basic

State, Action, State transfer, Path/Cost, Target Judge

Search Tree

## Heuristic Search

启发式搜索

evaluation fucntion $f(n)$, heuristic function $h(n)$.

Greedy best-first search: $f(n)=h(n)$

A* search: $f(n)=g(n)+h(n)$, g(n) refers to cost from source to current node.

## Adversarial Searh

aka Game search.

In zero-sum Game.

Each agent maximize self interest, minimize opponents'.

### Minimax Search

Traverse every possible situation before current state, compute every win possibility of every situation, choose the action with highest possibility.

If size of search tree is large, minimax search can't finish in time.

### Alpha-Beta Pruning

decrease the number of nodes.

Alpha: Max pay of MAX node currently, init. -infty

Beta: Min pay to opponent of MIN node currently, init. +infty

Pruning

- for alpha: if n is MIN node, if one of the successor of n provides pay less than alpha, then discard n and its successors.
- for beta: if n is MAX node, if one of the successor of n provides pay more than beta, then discard n and its successors.

### Monto-Carlo Tree Search MCTS

#### Monte-Carlo programming in single state

Multi-armed slot machine

k controllers, $R(s,a_k)$.

Minimize variance of regret function.

#### Upper Confidence Bound Strategies, UCB

$X_{i,T_i(t-1)}$ refers to in past t-1 times, average reward of i-th controller.

$I_t=\max_{i}\{\overline{X_{i,T_i(t-1)}},c_{t-1,T_i(t-1)}\}$

$c_{t,s}=\sqrt{\frac{2\ln n}{s}}$

$T_i(t)$ refers to in past t choices, times of choosing i-th controller.

$UCB=\overline{X_j}+C\times\sqrt{\frac{2\ln n}{n_j}}$

C is balance factor, to balance exploitation and exploration.

#### 4 steps of MCTS

1. selection. Choosing the node L with largest value of UCB.
2. expansion. If L is not terminate, choose its unvisited successor C stochastically.
3. simulation. From C, simulate game to terminal. 
4. back-propagation. Updating win times and visit times of each node in order.

MCTS is based on sampling, not traverse every state.

# Supervised Learning

## Machine Learning Basis

Learning from data.

mapping function $f$.

supervised, unsupervised, reinforcement.

data-annotation, model training, loss function.

## Regression Analysis

### Linear regression

$\hat y=a\hat x+b$

$a=\frac{\sum_ix_iy_i-n\bar x\bar y}{\sum_ix_i^2-n\bar x^2}$, $b=\bar y-a\bar x$

### Logistic regression

Sigmoid function $y=\frac{1}{1+e^{-z}}$.

$\hat y=\frac{1}{1+e^{-(w^Tx+b)}}$

## Decision Tree

Leaf nodes: decision rules.

Info entropy: $E(D)=-\sum_kp_k\log_2p_k$,$\sum p_k=1$ the lower the more confident.

## Linear Discriminant Analysis

Classification.

Multi-class: dimensionality reduction.

## Ada Boosting

自适应提升

Divide and conquer: Decompose a difficult classification into several easy sub-task.

Combine serval weak classifiers into strong classifier.

- for each weak classifier, change training factor.
- weight of weak classifier $G_m(x)$: $\alpha_m=\frac{1}{2}\ln\frac{1-err_m}{err_m}$
- Linear combination into string classifier: $G(x)=G(f(x))=sign(\sum_i\alpha_iG_i(x))$

- if error rate of a weak classifier is $\frac{1}{2}$, it can be considered as random classifier.

## Support Vector Machine SVM

structural rick minimization.

## Generative Learning Models

GAN

# Unsupervised Learning

## K-means Clustering

Both data feature and similarity function are important.

$dist(x_i,x_j)=\sqrt{\sum_k(x_ik-x_jk)^2}$, the less, the more similar.

### Process of k-means

1. Initialize the centroids of the k clusters randomly.
2. Iterate over the data points and assign each data point to the cluster with the closest centroid, based on some measure of distance (such as Euclidean distance). $\arg\min d(x_i,c_j)$
3. Recalculate the centroids of each cluster as the mean of the points assigned to that cluster. $c_j=\frac{1}{|G_j|}\sum_{x_i\in G_j}x_i$
4. Repeat steps 2 and 3 until the centroids converge, or until a maximum number of iterations is reached.
5. Return the final clusters and their centroids.

### Another View

Minimize variance of each claster.

## Principal Components Analysis PCA

Occam's Razor: the simplest explanation is usually the correct one.

Correlation coefficient, Covariance

## Eigenface

One of PCA method.

Feature can represent face, not pixels.

- 32\*32 trans to 1024\*1
- Compute the mean face
- Subtract the mean face from each training image to create a set of  "difference" images. These difference images represent the deviation of  each face from the mean face.
- Compute the covariance matrix of the difference images.
- Compute the eigenvectors and eigenvalues of the covariance matrix.
- Select the "top" eigenvectors, which are the eigenvectors with the largest eigenvalues.
- Use the selected eigenvectors to represent the faces in the dataset.

## Expectation Maximization EM

EM is used for finding the maximum likelihood estimates (MLE) of the parameters in a statistical model.

1. Initialize the model parameters with some arbitrary values.
2. In the E step, compute the expected value of the complete data log likelihood, given the current model parameters. This expected value is called the "Q function" and is a measure of how well the current model fits the data.
3. In the M step, maximize the Q function with respect to the model parameters. This step updates the model parameters to better fit the data.
4. Repeat steps 2 and 3 until the model parameters converge, or until a maximum number of iterations is reached.
5. Return the final model parameters as the maximum likelihood estimates.

# Statistics and Machine Learning

## Classification based on Logistic Regression Model

regression: from linear to non-linear

Logistc regression: output probability.

multi-class: softmax

## Latent Sematic Analysis based on Matrix Factorization

Latent Semantic Analysis LSA aka Latent Semantic Indexing LSI.

term-document matrix, low rank approximation

## Linear Discrimination Analysis and Classfication

aka LDA

- a feature dimentionality reduction method
- liner projection into a low dimensional space, which samples of same class - close, different - far from

# Deep Learning

Details in [Deep Learning and Application]().

# Reinforcement Learning

## Basic Concepts

RL: Learn from interaction with environment.

Agent, policy, state, action, reward.

### Feature of RL

- based on assessment
- interaction
- sequential decision-making

### Discrete Markov Process

$\{X_t\}_{t=0,1,\cdots}$.

Markov chain: $\Pr(X_{t+1}=x_{t+1}|X_0=x_0,X_1=x_1,\cdots)$

$=\Pr(X_{t+1}=x_{t+1}|X_t=x_t)$

Reward: $G_t=R_{t+1}+\gamma R_{t+2}+\gamma^2 R_{t+3}+\cdots$, gamma is the discount factor.

$MDP=\{S,A,\Pr,R,\gamma\}$

value function, action-value function

Def of RL problem: Given a MDP, learning a best policy $\pi^*$, which maximize $V_{\pi^*}(s)$.

### Bellman Equation

aka Dynamic Programming Equation

$V_\pi(s)=\mathbb E_\pi[\sum_i\gamma^iR_{t+i}|S_t=s]$

$q_\pi(s,a)=\mathbb E_\pi[\sum_i\gamma^iR_{t+i}|S_t=s,A_t=a]$

Bellman function for value function: $V_\pi(s)=\mathbb E_{a\sim\pi(s,\cdot)}\mathbb E_{s'\sim P(\cdot|S,a)}[R(s,a,s')+\gamma V_\pi(s')]$

Bellman function for action-value function: $q_\pi(s,a)=\mathbb E_{s'\sim P(\cdot|S,a)}[R(s,a,s')+\gamma\mathbb E_{a'\sim\pi(s',\cdot)}[q_\pi(s',a')]]$

## RL based on Value

$q_\pi(s,\pi'(s))\geq q_\pi(s,\pi(s))$, then $V_{\pi'}(s)\geq V_\pi(s)$

$\pi'=\arg\max_aq_\pi(s,a)$

### Policy Assessment methods in RL

Dynamic Programming

Monto-Carlo Sampling

Temporal Difference, aka monto-carlo + DP

- Q-learning

Trade-off between exploration & exploitation: $\epsilon$-greedy

- Deep Q-learning. parametrize q function.

Experience Replay

Target Network

## RL based on Strategy

parameterize strategy function: $\pi_\theta(s,a)$

### Policy Gradient Theorem

target: maximize $J(\theta)=V_{\pi_\theta}(s_0)$

$\frac{dJ}{d\theta}=\sum_s(\mu_{\pi_\theta}(s) * \frac{d\pi(s, a)}{d\theta} * q_{\pi_\theta}(s, a))$

## DRL Application

AlphaGo=DL+RL+MCTS

DQN: Atari Game, DOTA2, 

# Game Theory

## Basic Concepts

player, strategy, strategy set, outcome, mixed strategy, pure strategy, payoff, expected payoff, rule

### Prisoner's Dilemma

best response: confess, nash equilibrium

## Regret Minimization

$Regret_i^T(\sigma_i)=\sum_t(\mu_i(\sigma_i,\sigma_{-i}^t)-\mu_i(\sigma^t))$

$P(a)=\frac{Regret_i^T(a)}{\sum_bRegret_i^T(b)}$

Counterfactual Regret Minimization

## AI Security

- Encryption Protocol
- Digital watermark
- Data Security (availability, completeness, privacy) and Model security (robustness, correctness, generality)