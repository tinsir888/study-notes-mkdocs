---
title: 算法博弈论 三篇不同方向的论文阅读
author: tinsir888
date: 2023/12/13
cover: /img/AUagt.jpg
katex: true
tags:
  - 笔记
  - 论文阅读
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
abbrlink: 248b382e
---

Iannis 给了三篇论文，三个方向。分别通读一下这三篇论文，看看什么主题比较感兴趣吧。

# Possible Project Topics of Algorithmic Game Theory

## Algorithmic Game Theory

### Mechanism Design :white_check_mark:

- Auction Theory
- Market Design
- ......

The first paper is about **Mechanism Design** for Machine Learning/Statistical Tasks.

> 简单来讲就是说，将提供训练集的个体视为智能体 Agent，在训练人工智能模型的时候，这些智能体有动机操纵提供的训练集数据来使得人工智能模型最终的输出有利于该智能体。而研究者的任务是研究如何设计一个机制来鼓励智能体提供真实的数据（no incentive to misreport）

- Mean Estimation (2016 ICML)
- Classification
- Regression (Current)

### Games and Price of Anarchy

- Price of Anarchy
- Price of Stability

### Equilibrium Computation

Didn't cover in the course.

## Computational Social Choice

### Voting​ :white_check_mark:

Didn't cover in the course.

The second paper is about **Computational Social Choice** for Federated learning

### Matching

- Stable Matching (Gale–Shapley algorithm)
- Kidney-exchange

### Fair Division :white_check_mark:

Didn't cover in the course.

The third paper is about **Fair Division**. (Ιωάννης 在该方向上做了相当多的工作，2009-2023 current)

# Mechanism Design for Machine Learning

[Strategyproof Linear Regression in High Dimensions](https://arxiv.org/abs/1805.10693) by Chen et al, ACM Economics and Computation 2018

[Strategyproof](https://en.wikipedia.org/wiki/Strategyproofness)

> 说人话：就是占优策略激励相容 (Dominant Strategy Incentive Compatible abbr. DSIC)，向机制诚实报告就是最优策略 dominant strategy

## Abstract

Strategyproof mechanisms on Linear regression

Agents: single-peaked preferences

Ham Sandwich Theorem

This paper proposed: *Generalized Resistant Hyperplane* mechanisms.

## Introduction

Motivation: Designing a Machine Learning algorithm that is **robust to noise in training data**

Adversarial noise: sabotaging the algorithm.

> 这一点有印象，本科期间了解过，在图像上有策略的增加一些肉眼很难分辨的对抗噪点就能欺骗视觉模型使得图像分类的结果出错。

Training data: provided by *agents*

Aligning the agents' incentives correctly → possible to obtain uncontaminated data

### Scenario

Linear Regression i.e., fitting a hyperplane through given data.

Agents' incentive: to minimize their vertical distance from the output hyperplane.

Design: Strategyproof regression mechanisms without payments.

### Proposed Model

Fitting a hyperplane through data points $(\mathbb x_i,y_i)$ for $\mathbb x_i\in\mathbb R^d;y_i\in\mathbb R;i=1,\cdots,n$.

$y_i$ is held only by agent $i$.

$\mathbb x_i$ is independent variables, public information.

Mechanism: elicits the private information of the agents, returns a hyperplane represented by vector $\beta=(\beta_1,\beta_0)\in\mathbb R^{d+1}$.

Residual for agent $i$: $r_i=y_i-\beta_1^T\mathbb x_i-\beta_0$

Agents wish to minimize $|r_i|$



Dekel et al: **empirical risk minimization with $L_1$ loss** ($L_1$-ERM) coupled with **specific tie-breaking rule** is group strategyproof.

> No coalition of agents can be weakly better off by misreporting.

This paper: replacing the $L_1$ loss by a **weighted $L_1$ loss** and adding **convex regularization** to the **risk function** preserves group strategyproof.



Perote et al: 2-dimensional case, propose a wide family of strategyproof mechanisms, *clockwise repeated median* (CRM).

But this paper prove that it's wrong, giving 3 stricter conditions. CRM mechanism coincide with *resistant line mechanisms*

### Main Result

Generalizing CRM family to higher dimensions.

Introduce the family of *generalized resistant hyperplane* (GRH).

> In $d+1$ dimensions, GRH parametrized by $d+1$ subset of agents. 

Impartiality: group strategyproof.

> 比 DSIC 更严苛：如果智能体相互之间串通，谎报都不能使得收益增加。



Another desideratum except strategyproof: Good statistical efficiency.

Important: this paper establish a lower bound of $2$ on the approximation ratio of any strategyproof mechanism, which means that any mechanism that is even close to ordinary least squares regression must be manipulable.

## Related Work

Research: 3 categories:

1. Manipulable information

   Most papers assume: independent variables (feature vectors) - public; Dependent variables (labels) - private.

   - Strategyproof classifiers / agents interest in a shared set of input points.

   - Constructing classifiers robust to agents strategically misreporting *feature vector*.

     One-shot [Stackelberg game](https://en.wikipedia.org/wiki/Stackelberg_competition)

2. Goal of the agents :white_check_mark: (Exact one this paper discusses)

   - Motivated by privacy concerns: Tradeoff between accuracy and privacy

   - Agent want model make accurate assessment on their own sample, even if this reduce overall accuracy.

3. Use of payments and incentive guarantees

   - Monetary payment?

   - How strongly to guarantee truthful reporting

     stronger strategyproofness VS. Bayes-Nash incentive compatibility.

## Model

(Omit Notation here)

Strategyproofness: Every single agent has no incentive to misreport its true value.

**Group Strategyproofness**: Stronger than strategyproofness. No coalition of agents can simultaneously misreport in a way that: no agent in the coalition is strictly worse off and some agent in the coalition is strictly better off (with no regards to other agents' report value)

This paper does not consider weaker group strategyproofness, as mechanism proposed is able to satisfy stronger one.

## Families of Strategyproof Mechanisms

### Empirical Risk Minimization with the $L_1$ Loss

When dimension $d=0$ i.e., $\mathbb x_i=\mathbb x_j(\forall i,j\in N)$. Choosing the median of the reported values achieves group strategyproofness.

Median minimize $L_1$ loss: $\arg\min_{y\in\mathbb R}\sum_{i=1}^n|y-y_i|$.

This can be generalized into higher dimension while maintaining group strategy proof (Dekel et al)

This paper proposes **Generalized $L_1$-ERM**, which is also group strategyproof.

> Weighted $L_1$ loss, the loss of each agent $i$ is multiplied by a weight $w_i^{\mathbb x}$.
>
> adding a convex regularizer $h:\mathcal F\rarr R$

Advantage of Generalized $L_1$-ERM:

1. Eliminating tie-breaking step if risk function guaranteed to have a unique minimizer (To achieve this by adding strictly convex regularizer).
2. It covers all generalized medians while $L_1$-ERM reduces to a specific mechanism.



*quantile regression* mechanisms
$$
\hat R_q(f,\mathcal D)=\sum_{y_i\ge f(\mathbb x_i)}q\cdot|y_i-f(\mathbb x_i)|+\sum_{y_i\lt f(\mathbb x_i)}(1-q)\cdot|y_i-f(\mathbb x_i)|
$$
**Not remain strategyproof in higher dimension**.

### Generalized Resistant Hyperplane Mechanisms

Extending family of *resistant line* mechanisms.

#### Clockwise Repeated Median Mechanisms

Perote et al.

Only defined for the special case of simple linear regression.

Need to assume that set of points is **admissible**

> Admissible set is a collection of data points $\mathcal D=(x_i,y_i)_{i\in N}$ that $x_i\neq x_j\forall i,j\in N$

Step:

1. Divide agent into two subsets $S,S'$.
2. Compute the median *clockwise angle* (CWA)
3. For each point in $S$, choose $i^*\in S$ whose median CWA is the median of median CWAs from all points in $S$.
4. If median CWA from $i^*$ is towards point $j^*\in S'$, the mechanism returns the straight line passing through points $i^*$ and $j^*$.

CWA

> From $(x_i,y_i)$ to $(x_j,y_j)$, CWA is
> $$
> \text{CWA}((x_i,y_i),(x_j,y_j))=\pi+\text{sign}(x_j-x_i)\cdot\frac{\pi}{2}+\text{sign}(\frac{y_j-y_i}{x_j-x_i})|\arctan(\frac{y_j-y_i}{x_j-x_i})|\\
> \text{sign}(x)=\begin{cases}
> -1,x\lt0\\
> 0,x=0\\
> 1,x\gt0
> \end{cases}
> $$

Directing angle

> $$
> \text{DA}(S,S')=\text{med}_{i\in S}\text{med}_{j\in S':j\neq i}\text{CWA}((x_i,y_i),(x_j,y_j))
> $$

Mechanism returns the line $\beta=(\beta_1,\beta_0)$ s.t.
$$
\beta_1=\tan[\text{DA}(S,S')-\pi-\frac{\pi}{2}\cdot\text{sign}(\text{DA}(S,S')-\pi)]\\
\beta_0=\text{med}_{i\in S}(y_i-\beta\cdot x_i)
$$
But this paper provides two counterexamples showing that $(S,S')$-CRM mechanisms (under Perote's loose restrict) violate strategyproofness.



This paper looks for a subset of the CRM mechanisms to establish group strategyproofness. (impose more restrictive conditions)

Separable Sets of Point in a Plane

> In this paper definition is two sets can be separated by a vertical line.

$(S,S')$-CRM is group strategyproof if one of the following conditions holds

1. $S=S'$.
2. $S$ and $S'$ are separable.
3. $S\cap S'=\empty$ and $\min(|S|,|S'|)=1$. (dictatorship)

#### Generalized Resistant Line Mechanisms on a Plane

This paper introduces a novel family of group strategyproof mechanisms.

Start from: *Resistant Line* mechanisms

> 3 separable subset $L,M,R$. A line $\beta$ given by
> $$
> \text{med}_{i\in L}y_i-\beta_1x_i-\beta_0=\text{med}_{i\in R}y_i-\beta_1x_i-\beta_0=0
> $$
> Making median of residuals in $L$ and $R$ zero.

Yielding $(L,R)$-CRM mechanism
$$
\beta_1=\text{med}_{i\in L}\text{med}_{j\in R}\frac{y_j-y_i}{x_j-x_i}\\
\beta_0=\text{med}_{i\in L}y_i-\beta_1x_i=\text{med}_{j\in R}y_j-\beta_1x_j
$$
Brown-Mood mechanism: $L$ and $R$ are two half, $M$ is empty.

Tukey mechanism: $L,M,R$ each contains $1\over3$ points.

This paper: instead of making the *median* residuals from $S$ and $S'$ zero, making the k-th smallest residual in $S$ and $k'$-th smallest residual in $S'$ zero, for fixed $k\in[|S|]$ and $k'\in[|S'|]$.

**Generalized Resistant Line Mechanisms** (GRL)
$$
\min_{i\in S}^ky_i-\beta_1x_i-\beta_0=\min_{j\in S'}^{k'} y_j-\beta_1x_j-\beta_0=0
$$
Theorem: *$(S,S',k,k')$-generalized resistant line mechanism* is **group strategyproof**.

Noted: GRL is a generalized resistant mechanism version of $(S,S')$-CRM mechanism.

#### Generalized Resistant Hyperplane Mechanisms in High Dimensions

In $d+1$ dimension, take $d+1$ separable subsets of data points. Return regression hyperplane with makes prescribed percentiles of residuals in each set zero.

How to generalize to higher dimension? Using **Ham Sandwich Theorem**.

> A $d$-dimension hyperplane $H$ can "bisect" a $d+1$ hyperspace.

Well separable aka *affinely independent sets*

> Given $t\in[k+1]$, finite sets $S_1,\cdots,S_t$ of points in $\mathbb R^k$ are called *well separable* if for all disjoint $I,J\subseteq[t]$, there exists a hyperplane $H$ s.t. $S_i\sub H^+\diagdown H$ for each $i\in I$ and $S_j\sub H^-\diagdown H$ for each $j\in J$, i.e. $H$ separates $\cup_{i\in I}S_i$ from $\cup_{j\in J}S_j$ by putting them in different *open* half-spaces.

:warning: Due to my shortage of (discrete) mathematics knowledge, hereinafter only put key words.

Weak General Position

Publicly Separable Sets of Agents

Generalized Resistant Hyperplane (GRH) Mechanism

Hyperplane Comparison Lemma

:sob: Too many lemmas, propositions and theorem.

**Main contribution** of this paper:

**Every generalized resistant hyperplane mechanism is group strategyproof.**

### Strategyproofness Versus Group Strategyproofness

For $d=0$ all strategyproof mechanisms are group strategyproof. 

Is it still true that all strategyproof mechanisms for linear regression are also group strategyproof? No!

Impartial Mechanism

> A mechanism $M$ is called *impartial* if the outcome for each agent is independent of the agent's report.

Propositions and theorems...

## Characterizing Strategyproof Mechanisms

Locally Constant Function

> $$
> \exist\epsilon\gt0,\forall x'\in[x-\epsilon,x+\epsilon],f(x')=f(x)
> $$

In one-dimensional setting, a mechanism elicits agents report $\pi(y)$ ($y$ is private true valuation) is strategyproof iff:

1. There exist constant $\alpha^1,\alpha^2\in\overline{\mathbb{R}}\triangleq\mathbb R\cup\{-\infty,+\infty\}$
2. $\pi$ is continuous, for every $y\in\mathbb R$, either $\pi(y)=y$ or $\pi$ is locally constant at $y$.

For higher dimension, the conditions are similar. (median and locally constant)

## Efficiency of Strategyproof Mechanisms

OLS (ordinary least square) aka RSS (residual sum of squares)

Gauss-Markov Theorem: justification for OLS

OLS is not strategyproof.

Efficiency

> Given $\mathbb x$m the mechanism $M^{\mathbb x}$ for linear regression is $c$-efficient if for every $\mathcal D=(\mathbb x_i,y_i)_{i\in N}$, we have $\text{RSS}(\mathcal D,M^{\mathbb x}(\mathbb y))\le c\cdot\inf_\beta\text{RSS}(\mathcal D,\beta)$.

No strategyproof mechanism that is close to OLS can be strategyproof. (Proof omitted)

## Discussion

Open question leaves:

How to find a constructive characterization of all strategyproof or group strategyproof mechanisms for linear regression.

$L_1$-ERM is $n$-efficient, does there exist a more efficient strategyproof mechanism?

Analysis efficiency in a stochastic setting: What if data points are drawn from a underlying distribution?

Further research on "phantom"?

Different game setting?

# Computational Social Choice for Federated Learning

[Fairness in Federated Learning via Core-Stability](https://arxiv.org/abs/2211.02091) by Bhaskar et al. NeurIPS 2022

[Federated learning](https://en.wikipedia.org/wiki/Federated_learning)

> 简单来说，联邦学习就是在多个拥有本地数据样本的分布式边缘设备或服务器上训练人工智能模型，仅上传模型参数这种不敏感的数据到中心服务器中。会假定所有分布式的数据都是同分布的。训练过程是去中心化的。好处是保护数据隐私、数据安全等。
>
> MIT Press 的 NeurIPS 人工智能顶会

## Abstract

We care about fairness. It is unfair for agents with high quality data to sacrifice their performance due to other agents with low quality data.

Currently: *egalitarian and weighted equity-based* fairness measures suffer from the above pitfall.

This paper: model the task of learning a shared predictor in the federated setting as a *fair public decision making* problem.

*core-stable fairness*:

> Given $N$ agents, there is no subset of agents $S$ that can benefit significantly by forming a coalition among themselves based on their utilities.

Core-stable predictors are robust to low quality local data

*Proportionality*

> each agent gets at least $\frac{1}{n}$ fraction of the best utility that she can get from any predictor.

*[Pareto-optimality](https://en.wikipedia.org/wiki/Pareto_efficiency)*

>There exists no model that can increase the utility of an agent without decreasing the utility of another.

This paper: propose an efficient federated learning protocol **CoreFed** to optimize a core stable predictor.

Kakutani's fixed point theorem

## Introduction

### Background

Federated Learning (abbr. FL)

- utilize valuable data scatter in different organizations.

- Challenge: Heterogenity in local data distributions. To ensure a fair performance of classifier across all local data distributions.

Questions this paper asked: Jointly optimizing a certralized model with fairness guarantees regarding the heterogeneity of local agents. Defining such fairness s.t. no agents intend to form an coalition with a subset of agents. Finding a federated learning protocol to ensure such fairness.

Federated learning can be cast into *public decision making*

> All agents derive respective utilities from a common global decision.
>
> New goal: make this global decision fairly.

Fundamental fairness meaures: [core-stability](https://en.wikipedia.org/wiki/Core_(game_theory)).

> An outcome is considered "core-stable" if, once a coalition is formed and an outcome is achieved, no subgroup of that coalition has an incentive to break away and form a new coalition to achieve a more favorable outcome for themselves.
>
> A globally trained model is core stable if there are no blocking coalitions.

2 commonly used fairness notions

1. Egalitarian fairness

   Aims to maximize the utility of the least happy agent

2. Proportional fairness aka weighted equity based fairness

   Ratios of the losses of any pair of the agents to be proportional to the size of their respective datasets.

This paper points out that both notions are vulnerable.

> Unfair to the other agents as both may reduce loss incurred by the noisy-agent, thereby biasing the learning.

### Contribution of This Paper

Defining the core-stable fairness in FL.

- Modeling agents' utility functions to caputre their learning loss error.

- $N$ local agents, protocol $P$, aggregated model $f$. $f$ achieves core-stability if there are no coalition $S$ of agents that could benefit significantly by training a model with only their data.

  i.e., no agent has the incentive to deviate from curret group and thereby obtain proportionally better aggregate utility from the final trained model.

  就是说，在这一大群智能体中，没有任何一个真子集的智能体有动机去只用自己的数据训练模型。用所有人的数据训练比用这个真子集的数据训练效果更好。

  Such model $f$ ensures:

  - Proportionality: each agent gets $\frac{1}{n}$ times their best possible utility.
  - Pareto-optimality

- Core-stability is popular but rare-to-exist. Learning errorrs are innately non-linear and highly complex.

Contribution of this paper:

1. Extending core-stability from AGT to fairness in FL.

   fixed point formulation

2. **CoreFed**: An Effective federated learning protocol which optimizes the final model by **maximizing the protocol of agents' utilities**.

   Whenever underlying utility functions are **concave**.

3. Proving that CoreFed works in linear regression and logistic regression (utility functions are convex).

   For non-convex utility function (Smooth DNN), approximately core-stable.

4. Extending core-stability into weighted core-stability. Showing that weighted core-stable model is weighted proportional and Pareto-optimal.

   CoreFed → weighted CoreFed

5. Experiments on 3 datasets, CoreFed achieves the core-stable fairness, while maintaining similar utility with the standard FedAvg protocol.

## Related Work

### Fairness in Social Choice

Proportionality

> Every agent receives their propotional share of the best outcome. $\frac{1}{n}\times$ best utility themselves.

Envy-freeness

> Divide a set of items among agents fairly.
>
> No agent prefers the bundle of the other agent to her own. In public decision making this is impossible. Replaced by core-stability.
>
> Equitability: every agent should be equally happy.
>
> Relaxations of equitability: envy-freeness, proportionality.

### Fairness in Federated Learning

- Establishing fairness at the agent selection phase.

- Fairness while training the global model s.t. no discrimination against protected groups.

  e.g. Egalitarian fairness

- Considering fairness by evaluating contribution of the agents towards training the joint model.

  e.g. weighted equity fairness

- Significance to the agents based on [Shapley values](https://en.wikipedia.org/wiki/Shapley_value).

## Core-Stability in Federated Learning

### Problem Setup

Model: A function mapping from $\mathcal X\in\mathbb R^d$ to $\mathcal Y\in\mathbb R$.

Regression or binary classification.

Such mapping:
$$
\mathcal F=\{f_\theta|\theta\in P\subseteq\mathbb R^d\}\\
f_\theta:\mathbb R^d\rarr\mathbb R
$$
s.t. for data sample $(x,y)$ drawn from the distribution $\mathcal P$, $f_\theta(x)\approx y$.

$\theta$ is called predictor for the model.

#### Utility Functions of Agents

Quality of $\theta$ is measured by the expected loss over the data distribution $\mathcal P$, i.e., $\mathbb E_{(x,y)\sim\mathcal P}l(f_\theta(x),y)$.

$l(\cdot,\cdot)$ is a predefined loss function. Training process minimize expected loss, attaining $\theta^*=\arg\min_{\theta\in P}\mathbb E_{(x,y)\sim\mathcal P}l(f_\theta(x),y)$.

Utility function of a group for a predictor $\theta$:
$$
u(\theta)=M-\mathbb E_{(x,y)\sim\mathcal P}l(f_\theta(x),y)
$$
where $M$ is a constant more than $1+\epsilon$ times the loss incurred from the worst predictor for agent $i$, i.e.,
$$
M\ge(1+\epsilon)\sup_{\theta\in P,(x,y)\in\mathcal X\times\mathcal Y}l(f_\theta(x),y)\\
0<\epsilon\lt\lt10^{-5}
$$
Observation: $u(\theta)\in[M\epsilon,M(1+\epsilon)]$.

#### Federated Leaerning and Fairness

$n$ groups. Each group $i$ has their loss function $l_i(\cdot)$ and correspondingly a utility function $u_i(\cdot)$ for each $\theta\in P$.

Definition of **core-stability**:

> A prediction $\theta\in P$ is called core-stability if there exists no other $\theta'\in P$, and no subset $S$ of agents, s.t. $\frac{|S|}{n}u_i(\theta')\ge u_i(\theta)\forall i\in S$, with at least one strict inequality.
>
> No subset coalition can give agent better utility.
>
> This ensures that all agent benefit at least $\frac{1}{n}$ of its best possible utility. (Proportionality)

Definition of **proportionality**:

> A predictor $\theta\in P$ is called proportional iff $\forall\theta'\in P$, we have $u_i(\theta)\ge\frac{u_i(\theta')}{n}$  $(\forall i\in[n])$.
>
> No predictor that can increase theutility of some agent without decreasing the utility of another. (Pareto-optimality)

Definition of **Pareto-optimality**

> A prediction $\theta\in P$ is Pareto-optimal iff there exists no other $\theta'\in P$ s.t. $u_i(\theta')\ge u_i(\theta)$ for all $i\in[n]$ with at least one strict inequality.

#### Advantages of Core-Stability

Robust to low local data qualities of some agents.

Scale-invarance: Scaling the utility of any single agent does not alter the core-stable allocation.

### Existence of Core-Stability in Federated Learning

Conditions:

1. The utility function is continuous.

2. The set of maximizers of any conical combination of utility functions is convex.

   > Conical combination:  a linear combination where the coefficients are non-negative.

2 conditions together with Kakutani's Fixed Point Theorem (角谷不动点定理), we can prove the existence of core-stabiltiy in FL.

### Computation of a Core-Stable Predictor When Utility Functions are Concave

Any predictor that maximizes the product of utilities of the agents, i.e., $\arg\max_{\theta\in P}\prod_{i\in[n]}u_i(\theta)$ is core-stable.

Convex program:
$$
\text{maximize }\mathcal L(\theta)=\sum_{i\in[n]}\log(u_i(\theta))\\
\text{subject to }\theta\in P
$$
Theorem: if $u_i(\cdot)$ is concave for all $i\in[n]$, then any predictor $\theta^*$ that maximizes the convex program is core-stable. (Proof omitted)

Tightness of Guarantees: core-stability is tight.

#### CoreFed

For non-convex losses such as DNNs, applying gradient descent to maximize the objective.

$n$ finite samples $\{x_i,y_i\}_{i\in[n]}$ drawn from data distribution $\mathcal P$. Empirical distribution $\hat{\mathcal P}_n$.

Gradient descent:
$$
\nabla_\theta\mathcal L(\theta)=\sum_{i\in[n]}\frac{\nabla_\theta u_i(\theta)}{u_i(\theta)}=\sum_{i\in[n]}\frac{-\nabla_\theta\mathbb E_{(x,y)\sim\hat{\mathcal P}_n^{(i)}}l(f_\theta(x),y)}{M_i-\mathbb E_{(x,y)\sim\hat{\mathcal P}_n^{(i)}}l(f_\theta(x),y)}
$$
CoreFed: the model updates are weighted then aggregated at each iteration. (formular omitted here)

### Core-Stability in Linear Regression and Classification with Logistic Regression

This paper take linear regression and classification with logistic regression as example.

Since concavety of utility function in these scenarios are satisfied.

CoreFed can determin core-stable predictor in a federated learning setting training **linear regression** and **logistic regression (classification)**.

### Approximate Core-Stability in Deep Nerual Networks

Relaxed local core-stability we can attain for approximately first-order converged predictor.

:sob: very complex theorem for pseudo core stable predictor. (mere a mention here)

### Weighted Core-Stability

For each agent $i$, if we assign weight $w_i$, indicating the desired bias of the final trained model towards agent $i$. With subtle modifications, we can show the existence of a weighted core-stable predictor.

Weight Core-Stability

> Given a weight vector $w=(w_1,\cdots,w_n)$, a predictor $\theta\in P$, is weighted core-stable iff there exists no other predictor $\theta'\in P$ and a subset of agent $S\subseteq[n]$ s.t. 
> $$
> \frac{\sum_{j\in S}w_j}{\sum_{j\in[n]}w_j}\cdot u_i(\theta')\ge u_i(\theta)\text{ for all }i\in S
> $$
> with at least one strict inequality.

## Experiment

This paper evaluates CoreFed VS. FedAvg on Adult, MNIST, CIFAR-10 on linear model and deep neural network.

### Experiment Results

CoreFed achieves the core-stable fairness through comparison with FedAvg on different datasets and settings.

## Conclusion

Introducing a new notion of fairness in federated learning,

More resilient to noisy data from certain clients.

# Fair Division

[Fair Division of Indivisible Goods: Recent Progress and Open Questions](https://arxiv.org/abs/2208.08782) by Georgios et al, journal of Artificial Intelligence 2023

> 这是一篇文献综述 literature review。爱思唯尔 AIJ，CCF A，顶刊属于是。
>
> 这篇综述看完了，太爽了。感觉自己还是对公平分配感兴趣。Dive into 进去可能会有一些收获。

After reading , I drew a [mindmap](https://tinsir888.github.io/pdffile/FairDivisionMindmap.pdf) about this paper.

## Abstract

Fair division: Allocating resources to individuals in a *fair* manner.

Focus on **indivisible** case. Fairness notion:

- Envy-freeness
- Proportional

Relaxation of these notion:

- Maximin share fairness (MMS)
- Envy-freeness up to any good (EFX)

This paper: General review of recent progress for

- Different ways to relax fairness notions
- Common algorithm design techniques
- Interesting question for further research

## Introduction

Fair division: Fairly partitioning or allocating a set of resources to people with diverse and heterogeneous preferences over these resources.

Most classic work: Fair division of **infinitely divisible** resources.

Fairness means:

1. Proportionality

2. Envy-freeness

   Envy-free allocation of divisible resources can always be found in a finite number of steps.

For **indivisible** resources, aka *discrete fair division*. More challenging.

- No reasonable fair solution can be guaranteed in some cases: a single valuable item.
- Sometimes we need relaxations of envy-freeness and proportionality.

Relaxations

1. envy-freeness up to one good (EF1)
2. envy-freeness up to any good (EFX)
3. maximin share fairness (MMS)

Road map of this paper:

1. Introduction to the problem of discrete fair division
2. Results on EF1
3. Results on EFX
4. Results on MMS
5. Other notable fairness notions for the main setting
6. Efficiency and truthfulness
7. Other setting e.g. limited information, general valuations, randomness ...

## The Setting

Set $N$ of $n$ agents

Set $M$ of $m$ goods (can't be divided or shared)

Agent $i\in N$ is equipped with a valuation function $v_i:2^M\rightarrow\mathbb R_{\ge0}$, which assigns a non-negative real number to each possible subset of items and is *normalized* and *monotone*, i.e., $v_i(\emptyset)=0$ and $v_i(S)\le v_i(T)\forall S\subseteq T\subseteq M$.

Focus on valuation function of each agent is assumed to be *additive*. $v_i(S)=\sum_{g\in S}v_i(g)\forall S\subseteq M$.

A fair allocation instance is denoted by $I=(N,M,v)$ where $v=(v_1,\cdots,v_n)$ is the vector of valuation functions and can be represented by a table with a row per agent and a column per good, s.t. cell $(i,g)$ contains the value $v_i(g)$.

An allocation is a tuple of subsets of $M$, $A=(A_1,\cdots,A_n)$ s.t. each agent $i\in N$ receives the bundle $A_i\subseteq M,A_i\cap A_j=\emptyset$ for every pair of agents $i,j\in N$ and $\cup_{i\in N}A_i=M$. If $\cup_{i\in N}A_i\subsetneq M$, the allocation is called partial.

### Solution Concepts

Objective: To compute a fair allocation i.e., an allocation that satisfies a desired fairness criterion.

Two fairness notions: envy-freeness and proportionality.

Envy-freeness:

> An allocation $A$ is envy-free (EF) if $v_i(A_i)\ge v_i(A_j)$ for every pair of agents $i,j\in N$.
>
> 所有人认为自己分到的东西比别人分到的东西价值都高的情况，称为*无嫉妒*的。

Proportionality:

> An allocation $A$ is proportional (PROP) if $v_i(A_i)\ge\frac{v_i(M)}{n}$ for every agent $i\in N$.
>
> 所有人分到的东西都至少能达到他认为物品总价值的 $1\over n$ 的分配，成为*成比例*的。

Obviously, $EF\rightarrow PROP,PROP\not\rightarrow EF$.

ER and PROP allocations do not always exist.

Even deciding whether an instance admits an EF or PROP allocation is NP-complete.

### Important Relaxations

#### EF1

**Envy-freeness up to one good**. (Lipton et al. 2004; Budish. 2011)

> 对于 $i$ 来说，如果另一个 $j$ 得到的物品集中去掉一个**特定**的物品，$i$ 就不再嫉妒 $j$ 了。

- It is acceptable for an agent $i$ to envy another agent $j$, as long as there exists a good in $j$'s bundle the hypothetical removal of which would eliminate $i$'s envy towards $j$,
- Definition: An allocation $A$ is EF1 if for every pair of agents $i,j\in N$, it holds that $v_i(A_i)\ge v_i(A_j\diagdown\{g\})$ for **some** $g\in A_j$.
- A week notion of EF1: there are times when a very highly-valued good is hypothetically removed from  another agent's bundle.

#### EFX

To refine this week notion of EF1, we create stricter relaxation, **envy-freeness up to any good** aka EFX (Gourvès et al. 2014; Caragiannis et al, 2016)

> An allocation is said to be EFX if the envy of the agent $i$ towards agent $j$ can be eliminated by the hypothetical removal of *any* good in $j$'s bundle.
>
> 对于 $i$ 来说，如果另一个 $j$ 得到的物品集中去掉**任意**一个的物品，$i$ 就不再嫉妒 $j$ 了。

- Definition: An allocation $A$ is EFX if for every pair of agents $i,j\in N$, it holds that $v_i(A_i)\ge v_i(A_j\diagdown\{g\})$ for **any** $g\in A_j$.

The existence of EFX allocation is challenging open problem.

#### MMS

maximin share fairness (Budish. 2011)

- Generalization of the rationale of the well-known cut-and-choose protocol.

- Goal: Give each agent $i$ goods of value at least as much as her *maximin share* $\mu_i^n(M)$, which is the maximum value this agent could guarantee for herself by partitioning the set of goods $M$ into $n$ disjoint bundles and keeping the worst of them.

- It is a relaxation of proportionality.

- Definition: Let $\mathcal A_n(M)$ be the collection of possible allocations of the goods in $M$ to $n$ agents. An allocation $A$ is said to be MMS if for each agent $i\in N$,
  $$
  v_i(A_i)\ge\mu_i^n(M)=\max_{B\in\mathcal A_n(M)}\min_{S\in B}v_i(S)
  $$

Computing MMS allocation or even computing the maximin share of an agent is an NP-hard problem.

MMS allocations do not always exists when there are more than 2 agents. (Kurokawa et al. 2018, 2016)

It's possible to compute *approximate MMS allocations*.

## Envy-Freeness up to One Good (EF1)

 ### Computing EF1 Allocations

#### Round-Robin Algorithm

Simplest polynomial time for EF1 allocation computing algorithm is **Round-Robin**.

- Allocates the goods to the agents in multiple rounds.
- In each round, according to some arbitrarily fixed given ordering, each agent chooses her most valuable good among the available ones.

**Theorem 1**: Round-Robin computes an EF1 allocation.

#### Envy-Cycle Elimination Algorithm

Lipton et al. 2004

- Do not use a prefixed sequence for agents to select goods. Instead, it repeatedly chooses an agent that is in a disadvantage compared to other agents and gives an unallocated good.

- The algorithm maintains a *envy graph*, where the nodes correspond to agents and there is an edge from agent $i$ to agent $j$ if $i$ is envious of $j$'s bundle.

- At each step of algorithm, an unassigned good is allocated to some agent who is not envied by any other agent i.e., an agent corresponds to a node with in-degree 0 in the envy graph. If no such agent exists, the envy graph must contain a directed cycle, which can be eliminated by redistributing the current bundles among the agents that participate in the cycle.

- The cycle can be resolved by exchanging the bundles of items along the cycle.

- Repeating the procedure, eventually leads to a modified ency graph with at least one agent who is not envied by any other agent.

==Personal comments: It is kind of like Top Trading Cycle Algorithm???==

Envy-Cycle Elimination Algorithm runs in polynomial time and outputs an EF1 allocation.

### EF1 and Pareto Optimal (PO) Allocations

Besides fair, another desideratum is efficiency.

(Caragiannis et al. 2019) found inherent connection between EF1, Pareto optimality and *maximum Nash welfare (MNW)*.

Pareto optimal Allocations: An allocation $A$ is Pareto optimal if there is no allocation $B$ s.t. $v_i(B_i)\ge v_i(A_i)\forall i\in N$ and $v_j(B_j)\ge v_j(A_j)$ for some $j\in N$. Equivalently, we say such an allocation $B$ is not *Pareto dominated* by any other allocation.

MNW allocation: An allocation $A$ is MNW allocation if:

1. It maximizes the number of agents with positive value
2. It maximizes the Nash welfare ($\prod_iv_i(A_i)$) for the agents with positive value.

**Theorem 2**: Every MNW allocation is EF1 and PO.

There exist allocations that combine fairness with Pareto efficiency. (Yuan and Suksompong 2023) showed that Nash Welfare is the only welfarist function of agent valuations whose maximization leads to allocations that are EF1 and PO.

Computing MNW allocation is hard. In pseudo-polynomial time. **Still remains unknown whether this can be done in polynomial time**.

:question:**Open Problem 1: Can an EF1 and PO allocation be computed in polynomial time?**

## Envy-Freeness up to Any Good (EFX)

The existence of EFX is challenging open problem.

### Existence and Computation of EFX Allocations for Special Cases

#### Identical Valuations

Plaut and Roughgarden 2020

Allocations that satisfy particular properties about the minimum value are EFX.

A *leximin* allocation is an allocation that first maximizes the minimum value among all bundles, then maximizes the second minimum value and so on.

leximin is not EFX, but add some slight refinement of it is in fact EFX:

- After maximizing the minimum value, the allocation must maximize the size of the bundle with minimum value before maximizing the second minimum value and so on.
- Called *leximin++*

Any leximin++ allocation $A$ must be EFX.

#### Ordered Valuations

Plaut and Roughgarden 2020

Envy-Cycle elimination algorithm returns an EFX allocation for the slightly more general class of ordered instances, where the agents have identical orderings, but possibly distinct cardinal valuations.

- The ordering of the goods $g_1,\cdots,g_m$ s.t. $v_i(g_1)\ge \cdots\ge v_i(g_m)\forall i\in N$.

**Theorem 3**: The Envy-Cycle Elimination Algorithm computes an EFX allocation for every ordered instance.

#### EFX for 2 and 3 Agents

We can compute EFX allocation using a *divide-and-choose* approach that exploits the existence of EFX allocations for agents with identical valuations.

**Theorem 4**: EFX allocations always exist for instances with 2 agents.

(Goldberg et al. 2023) showed that it's possible to efficiently compute EFX allocations when there are only two agents for some more general valuation functions such as gross substitute or budget-additive, but problem becomes PLS-complete for sub-modular functions.

Computing EFX allocations for 3 agents in polynomial time is still an open problem.

[Sub-modular function](https://en.wikipedia.org/wiki/Submodular_set_function) $f$:
$$
f(A\cup\{i\})-f(A)\ge f(B\cup\{i\})-f(B)\forall A\subseteq B\subseteq N\text{ and }i\in N\diagdown B
$$

> “边际效用递减”

[PLS-complete](https://en.wikipedia.org/wiki/PLS_(complexity)): Polynomial Local Search complete. A problem is PLS-complete if it is PLS and any problem in PLS can be reduced to it in polynomial time.

#### Bi-valued Valuation

Only 2 distinct possible values that an agent may have for the goods.

(Amandatidis et al. 2021) showed that EFX allocations exist and can be efficiently computed for any number of agents.

(Garg and Murhekar 2021) showed that this is possible even in conjunction with PO.

(Amandatidis et al. 2021) also demonstrated an interesting connection between EFX and MNW allocation for bi-valued instances. MNW allocations always satisfy EFX.

#### Discussion

Original definition of EFX is a bit weaker because it requires the removed good to be positively-valued.

:question:**Open Problem 2: Do EFX allocations exist for instance with $n\ge4$ agents and unrestricted additive valuations?**

### Relaxations of EFX

Family of *Approximately EFX*

$\alpha$-EFX: Let $\alpha\in[0,1]$, An allocation is $\alpha$-EFX if for every pair of agents $i,j\in N$, $v_i(A_i)\ge\alpha \cdot v_i(A_j\diagdown\{g\})$ for any $g\in A_j$.

(Plaut and Roughgarden 2020) showed $\frac{1}{2}$-EFX allocations always exist even for sub-additive valuation functions.

Sub-additive function: $v(A\cup B)\le v(A)+v(B)$

(Chan et al 2019) showed that computing such allocations can be done in polynomial time.

**Theorem 5**: The Envy-Cycle Elimination algorithm computes a $1\over2$-EFX allocation.

The approximation ratio for the additive case was improved by (Amandatidis et al. 2020) to 0.618 by combining Round-Robin and Envy-Cycle Elimination with some appropriate pre-processing.

:question:**Open Problem 3: What is the best possible $\alpha$ for which $\alpha$-EFX allocations exist?**

==如果对于开放问题 3，$\alpha=1$ 成立，那么开放问题 2 也成立。如果开放问题 2 的答案是否定的，那么开放问题 3 就有重大意义。==

Additionally, as is the case for all of these notions, the next natural question is whether existence can be paired with polynomial-time algorithms for finding such allocations.



Another recent approach is that of relaxing the requirement to allocate all available goods. Clearly, if done without any constraints, this makes the problem trivial: simply leaving all good unallocated. Here objective is to only leave few goods unallocated or remove some goods without affecting the maximum possible Nash social welfare by too much.

(Caragiannis et al. 2019) showed that it is possible to compute an EFX allocation of a subset of the good the Nash welfare of which is at least half of the maximum Nash welfare on the original set.

(Chaudhury et al. 2021) presented an algorithm that computes a partial EFX allocation, but the number of unallocated goods is at most $n-1$.

(Berger et al. 2022) showed the unallocated goods can be decreased to $n-2$.

(Chaudhury et al. 2021) showed that a $(1-\epsilon)$-EFX allocation with a sub-linear number of unallocated goods and high Nash welfare can be computed in polynomial time for every constant $\epsilon\in(0,0.5]$.

:question:**Open Problem 4: Is it possible to achieve an exact EFX allocation by donating a sub-linear number of goods?**

## Maximin Share Fairness (MMS)

MMS significantly weakens the requirement of PROP. But MMS are not guaranteed to exist.

The trend is focusing on *approximate MMS allocations*.

$\alpha$-MMS: Let $\alpha\in[0,1]$. An allocation $A$ is $\alpha$-MMS if $v_i(A_i)\ge\alpha\cdot \mu_i^n(M)\forall i\in N$.

Monotonicity of the MMS values: remove one agent and one good from the instance, the MMS value of each of the remaining agents in the remaining instance does not increase.

**Lemma 1**: For any agent $i$ and any good $g$, $\mu_i^{n-1}(M\diagdown\{g\})\ge\mu_i^n(M)$.

Lemma 1 suggests that if there is a large good $g$ that satisfies the desired approximation of MMS value for some agent $i$, we can simply assign $g$ to $i$ and finalize the bundle $A_i=\{g\}$ without hurting the remaining agents in the reduced instance.

### Computing $1\over2$-MMS Allocations

Consider simple algorithms for computing $1\over2$-MMS Allocations in polynomial time. (Better approximation can be achieved by variants and extensions of the techniques employed by these algorithms)

The monotonicity of the MMS value allows us to focus only on the case when the agents have sufficiently small value for the goods, in particular, $v_i(g)\le\frac{\mu_i^n}{2}$ for every $i\in N,g\in M$. As long as there is an agent $i$ and a good $g$ s.t. $v_i(g)\gt\frac{\mu_i^n}{2}$, we can allocate $g$ to $i$ so that $i$ obtains half of her MMS value, and never consider $i$ to $g$ again. If we guarantee $1\over2$-MMS for the remaining agents in the reduced instance, the same guarantee must also hold in the original instance by Lemma 1.

One way to achieve this is by using Round-Robin.

**Lemma 2**: If $v_i(g)\lt\frac{\mu_i^n}{2}$ for eveery $i\in N,g\in M$, the Round-Robin algorithm returns an allocation that is $1\over2$-MMS.

**Theorem 6**: The Envy-Circle Elimination algorithm computes a $1\over2$-MMS allocation.

**Theorem 7**: The Bag-Filling Algorithm returns a $1\over2$-MMS allocation.

> Bag here means donation bag.
>
> 就是保留某些物品不分配给任何一个玩家（相当于直接捐给慈善机构）

### Better Approximations

(Kurokawa et al. 2018) showed how to find $2\over3$-MMS allocation (but not in polynomial time)

(Amanatidis et al. 2017) matched this guarantee in polynomial time.

Simpler algorithms rely on important property that the hardest case is when the agents have the same order of preference over the goods:

Given any instance $I=(N,M,v)$ which may not be ordered, we can construct corresponding ordered instance $I'=(N,M,v')$. Based on an ordering $\succ$ of the goods, for each agent $i\in N$, we find a permutation $\sigma_i:M\rightarrow N$ s.t. $v_i(\sigma_i(g))\ge v_i(\sigma_i(g'))$ for every $g\succ g'$. Then we define a new valuation function $v_i'$ with $v_i'(g)=v_i(\sigma_i(g))$ for every $g\in M$; hence, the new value of $i$ for good $g$ that ranked $l$-th in $\succ$ equals the $l$-th highest value of $i$ in original valuation. Hence, if $I'$ admits MMS then $I$ admits an MMS as well (same holds for approximate MMS allocations).

**Lemma 3**: Let $I=(N,M,v)$ be any instance, $I'$ be its corresponding instance, and $A'=(A_1',\cdots,A_n')$ be an allocation for $I'$ s.t. $v_i'(A_i')\ge\alpha_i$ for every $i\in N$. Then, we can construct in polynomial time an allocation $A=(A_1,\cdots,A_n)$ for $I$ s.t. $v_i(A_i)\ge\alpha_i$ for every $i\in N$.

(Barman and Krishnamurthy 2020) showed the Envy-Cycle Elimination algorithm returns a $2\over3$-MMS allocation for ordered instances, thus $2\over3$-MMS fair allocation can be found in polynomial time for every instance due to Lemma 3.

(Ghodsi et al. 2021) designed an $(\frac{3}{4}-\epsilon)$-approximation algorithm.

(Garg and Taki 2021) improved approximation guarantee of $\frac{3}{4}+\frac{1}{12n}$.

(Feige et al. 2021) showed that it's impossible to achieve an approximation bound better than $39\over40$.

:question:**Open Problem 5: Is it possible to improve upon the bound of $\frac{3}{4}+\frac{1}{12n}$ for additive valuations? Is there a stronger inapproximability bound than $39\over40$?**

### Restricted Instances

By restricting the number of agents or the space of the valuation functions, one can get stronger results.

MMS for four or fewer agents.

- Two agents: a simple cut-and-choose-type protocol always produce an MMS allocation.

  The first agent partitions the set of goods as equally as possible and the second agent decides who gets each of these sets.

  - The first step is computationally hard but producing a $(1-\epsilon)$-MMS allocation in polynomial time.

- (Kurokawa et al. 2018) guarantees a approximation of $2\over3$, for three or four agents it guarantees $3\over4$ approximation.

- (Amanatidis et al. 2017) improved approximation factor for three agents to $7\over8$. (Gourvès and Monnot 2019) improved to $8\over9$. (Feige and Norkin 2022) improved to $11\over12$.

- (Ghodsi et al. 2021) improved approximation factor for four agents to $4\over5$.

MMS for restricted valuations.

- All agents have the same valuation functions.
- (Bouveret and Lemaître 2016) showed that unlike EFX, the hardest instances for MMS are the ordered instances.
- MMS allocations always exist and can be computed efficiently for ternary valuation function（三元估值函数） (Amanatidis et al. 2017) and for bi-valued valuation functions (Feige 2022)
- (Ebadian et al. 2022) showed that this is also the case when there are at most two values per agent and for general instances where the value of each good is at least as much as the value of all less valuable goods combined.
- MMS allocations are known to exist is that of [matroid-rank](https://en.wikipedia.org/wiki/Matroid_rank) valuations

:question:**Open Problem 6: Are there other classes of structured valuations for which MMS is guaranteed to exist, e.g., when there are only a few possible values?**

## Further Notable Fairness Notions

Other notable fairness beyond EF, PROP, EF1, EFX, MMS.

### EFL, EFR, and Epistemic Notions

EFL aka envy-freeness up to one less-preferred good (Barman et al. 2018)

- EFL is between EF1 and EFX.

- An agent $i$ does not envy another agent $j$ if either $A_j$ contains at most one good that $i$ values positively, or thee envy of $i$ can be eliminated by the hypothetical removal of a good $g\in A_j$ s.t. $v_i(A_i)\ge v_i(g)$
- EFL always exists, and can be computed using a variant of the Envy-Circle Elimination algorithm.

EFR aka envy-freeness up to a random good (Farhadi et al. 2021)

- The envy of agent $i$ towards another agent $j$ can be eliminated in expectation after the hypothetical removal of a randomly chosen good from $A_j$.
- $0.73$-EFR allocation can be computed in polynomial time.

:question:**Open Question 7: Do EFR allocations always exist?**

Epistemic Fairness

- Agents do not have complete knowledge about the computed allocation.

- (Aziz et al. 2018) considered a setting where the agents are connected via a social network and do not know the bundles allocated to the agents who are not their neighbor. They introduced **epistemic EF** under which an agent is satisfied only if there are a redistribution of the goods she is not aware of s.t. the resulting allocation is EF to her.

  (Caragiannis et al 2022) proved the existence of epistemic EFX allocations for additive valuations when the underlying social network contains only isolated nodes.

### PMMS and GMMS

Variations of MMS

**PMMS aka pairwise maximin share fairness** (Caragiannis et al 2019)

- Every pair of agents $i$ and $j$, $i$'s value for $A_i$ must be at least as much as the maximum she could obtain by redistribution the set of goods in $A_i\cup A_j$ into two bundles and picking the worst of them.
- PMMS requires that it is achieved for any pair of agents.

$\alpha$-PMMS: Let $\alpha\in[0,1]$. An allocation $A$ is $\alpha$-PMMS if $v_i(A_i)\ge\alpha\cdot\mu_i^2(A_i\cup A_j)\forall i,j\in N$. When $\alpha=1$, the allocation is called PMMS.

:question:**Open Problem 8: Do PMMS allocations always exist?**

Any PMMS allocation must be EFX, thus showing the existence of PMMS allocations would also imply the existence of EFX allocations. For approximate PMMS, the best known result is $0.781$ by (Kurokawa 2017).

**GMMS aka groupwise maximin share fairness** (Barman et al. 2018)

- Stronger notion
- The maximin share guarantee is simultaneously achieved for any possible subset of agents.

$\alpha$-GMMS: Let $\alpha\in[0,1]$. An allocation $A$ is $\alpha$-GMMS if $v_i(A_i)\ge\alpha\cdot\text{GMMS}_i$ for all $i\in N$, where
$$
\text{GMMS}_i=\max_{S\subseteq N:i\in S}\mu_i^{|S|}(\bigcup_{j\in S}A_j)
$$
when $\alpha=1$, the allocation is called GMMS.

- (Barman et al. 2018) showed that GMMS allocations exist for some restricted settings, such as when the agents have binary or identical values. EFL allocation is $1\over2$-GMMS, such an allocation can be computed efficiently.
- Best known approximation of GMMS is $4\over7$ (Amanatidis et al. 2020; Chaudhury et al. 2021)

:question:**Open Problem 9: What is the best possible $\alpha$ for which $\alpha$-GMMS allocations exist?**

### Prop1, PropX and PropM

Relaxation of proportionality.

**Prop1 aka proportionality up to one good** (Conitzer et al. 2017)

- Each agent could obtain her proportional share if given one extra good.
- An allocation $A$ is Prop1 if for every agent $i\in N$, there **exists a good** $g\in M\diagdown A_i$ s.t. $v_i(A_i\cup\{g\})\ge\frac{v_i(M)}{n}$.

**PropX aka proportionality up to any good** (Aziz et al. 2020)

- Each agent could obtain her proportional share when given the least positively-valued good among those allocated to other agents.
- An allocation $A$ is PropX if for every agent $i\in N$ and **any good** $g\in M\diagdown A_i$, we have $v_i(A_i\cup\{g\})\ge\frac{v_i(M)}{n}$.

An allocation that is Prop1 and PO always exists (Conitzer et al. 2017) and can be computed in polynomial time. (Barman and Krishnamurthy 2019)

PropX is rather demanding and it can not always be guaranteed. (Aziz et al. 2020)

**PropM aka proportionality up to maximin good** (Baklanov et al 2021)

- Middle-ground between Prop1 and PropX.

- Given an allocation $A$, the value of a maximin good for agent $i$ is
  $$
  d_i(A)=\max_{j\neq i}\min_{g\in A_j}v_i(g),
  $$
  and $A$ is called PropM if $v_i(A_i)+d_i(A)\ge\frac{v_i(M)}{n}$.

- PropM always exists and can be computed in polynomial time.

### Equitability and its Relaxations

Besides envy-freeness and proportionality, another important fairness notion is that of **equitability** according to which all agents must derive the same value from the bundles they are allocated.

Definition of Equitability (EQ) (Brams and Taylor 1996): An allocation $A=(A_1,\cdots,A_n)$ is equitable if $v_i(A_i)=v_j(A_j)$ for every pair of agents $i,j\in N$.

- EQ allocation may not exist. similarly to EF1 and EFX, we can relax EQ1 and EQX.

**EQ1**: An allocation $A$ is equitable up to one good if, for every pair of agents $i,j\in N$, it holds that $v_i(A_i)\ge v_j(A_j\diagdown\{g\})$ for **some** $g\in A_j$.

**EQX**: An allocation $A$ is equitable up to any good if, for every pair of agents $i,j\in N$, it holds that $v_i(A_i)\ge v_j(A_j\diagdown\{g\})$ for **any** $g\in A_j$.

Obviously EQX is EQ1.

(Gourvès et al. 2014) showed that EQX allocation under the *nearly jealousy-free* always exist and can be computed in polynomial time.

## Beyond Fairness: Efficiency and Incentives

Relation between fairness and efficiency. Possibility of achieving fairness when the agents are strategic.

### Fair and Pareto Optimal Allocations

Whether it is possible to simultaneously achieve fairness and efficiency.

Common type of efficiency: Pareto Optimality, can be guaranteed in conjunction with some fairness notions like EF1 and Prop1.

(Caragiannis et al. 2019) showed that any allocation that maximizes the Nash welfare is EF1 and PO. But computation of Nash welfare maximizing allocations is NP-hard.

For computation of allocations that are fair and PO, a commonly used technique is to exploit the connection between fair division and *market equilibria in Fisher markets* (Budish 2011), which has been widely used in the design of approximation algorithms for maximizing the Nash welfare (Cole and Gkatzelis 2018; Cole et al. 2017).

**A Fisher market** consists of a set of agents $N$, a set of goods $M$, and each agent $i\in N$ has a value $v_i(g)$ for every good $g\in M$. In contrast to discrete fair division, each agent $i$ also has a *budget* $b_i\ge0$, and the goods can be fractionally allocated. A fractional allocation is a tuple of vectors $X=(x_i)_{i\in N}$, where $x_i=(x_{i1},\cdots,x_{im})$; in particular, $x_{ig}\in[0,1]$ denotes the fraction of $g$ that is allocated to $i$, s.t. $\sum_{i\in N}x_{ig}\le1$ for every $g\in M$.

Focusing on Fisher markets with *linear utilities*, $v_i(x_i)=\sum_{g\in M}x_{ig}\cdot v_i(g)$. A market outcome is a tuple $(X,p)$, where $X$ is a fractional allocation and $p=(p_1,\cdots,p_m)$ defines the price $p_j$ for item $j$.

An outcome $(X,p)$ is a market equilibrium if it satisfies the following conditions:

1. The market clears (all items are completely allocated): $\sum_{i\in N}x_{ig}=1$ for every $g\in M$ with $p_g\ge0$.
2. The budgets of all agents are exhausted: $\sum_{g\in M}p_g\cdot x_{ig}=b_i$ for every $i\in N$.
3. The agents only spend money on the goods with maximum value per unit of money spent (bang-per-pack): For any $g\in M$, $x_{ig}\gt0$ implies $\frac{v_i(g)}{p_g}\ge\frac{v_i(g')}{p_{g'}}$ for every $g'\in M$.

**Fractional Pareto Optimal (fPO)**: An allocation is fPO if it's not Pareto dominated by any fractional allocation.

An fPO allocation is also PO.

When all agents have the same budget (i.e., a *competitive equilibrium from equal income, CEEI*), the allocation is EF (Varian 1974).

(Barman et al. 2018) compute allocations by perturbing the budgets or the values in the market so that they admit market equilibria with integral allocations.

(Barman et al. 2018) designed a pseudo-polynomial time algorithm to compute an allocation that is EF1 and PO.

> Pseudo-polynomial aka 伪多项式时间复杂度，比如 01 背包的动态规划算法就是伪多项式时间复杂度的。

(Barman and Krishnamurthy 2019) designed a strongly polynomial time algorithm to compute an allocation that is PROP1 and PO.

(Garg and Murhekar 2021) showed that in bi-valued instances, an EFX and PO allocation can be computed in polynomial time, but in instances with 3 distinct values, EFX and PO are incompatible.

(Freeman et al. 2019) showed that if all values are strictly positive, there always exists a EQX and PO allocation. But there do not exist EQ1 and PO allocations when the values can be 0.

(Garg and Murhekar 2022) provided a pseudo-polynomial time algorithm to compute an EQ1 and PO allocation.

#### Approximating the Nash Welfare

MNW are both EF1 and PO.

Worthy probelm: Efficiently computing approximate maximum Nash welfare allocations

(Garg et al. 2017) showed that approximating the Nash welfare within a ratio better than $1.069$ is NP-hard, even for additive valuation functions with only four values; in fact it is APX-hard to approximate the Nash welfare even when there only two values (Akrami et al. 2022).

[APX](https://en.wikipedia.org/wiki/APX)

> aka Polynomial-time approximation algorithms with approximation ratio bounded by a constant.

For additive valuations, the best known approximation is $e^{\frac{1}{e}}+\epsilon\approx1.45$. (Barman et al. 2018)

For all agents have submodular valuation functions, the best known approximation of Nash welfare is $6+\epsilon$ (Garg et al. 2022) and the best known lower bound is $1.5819$ (Garg et al. 2020).

Under the value oracle model, asymptotically tight approximation ratio of $\Theta(n)$ for both XOS and subadditive functions. (Barman et al. 2020; Chaudhury et al. 2021)\

[Value oracle](https://en.wikipedia.org/wiki/Demand_oracle)

XOS (aka XOR of Sum)

> A set function $f:2^N\rightarrow\mathbb R$ is considered XOS if it can be represented as the XOR (exclusive OR) of linear threshold functions. Mathematically, a XOS function can be written as
> $$
> f(S)=\bigoplus_{i=1}^kg_i(S)
> $$
>
> - $S$ is a subset of elements from the ground set $N$.
>
> - $\bigoplus$ denotes the XOR operation.
>
> - $g_i(S)$ represents a linear threshold function on subset $N$.
>   $$
>   g_i(S)=\begin{cases}
>   1,|S|\ge\theta_i\\
>   0,\text{otherwise}
>   \end{cases}
>   $$

With stronger oracle assumptions, a sublinear approximation ratio is possible for XOS functions (Barman et al. 2021)

Whether sublinear approximation ratios are possible for subadditive functions with stronger oracle assumption remains unknown.

#### Egalitarian Welfare

Egalitarian welfare is the minimum utility among all agents.

To maximize the egalitarian welfare is a known APX-hard problems.

While egalitarian welfare maximizing allocations are clearly PO, they may not achieve any constant approximation of important relaxations of EF and PROP, such as EF1 and MMS.

### Price of Fairness (PoF)

Another natural goal is to approximately maximize some **objective function of the values** of the agents such as social welfare, i.e. total value of the agents for the goods they receive（上课讲过）.

Social welfare of an allocation $A$:
$$
SW(A)=\sum_{i\in N}v_i(A_i)
$$
(Bertsimas et al. 2011) and (Caragiannis et al. 2012) defined the *Price of Fairness*

- Similar to approximation ratio in worst-case analysis which measures the deterioration of the objective due to the fairness requirement.
- Given an instance $I$, we denote by $OPT(I)$ the maximum social welfare over all allocations of $I$. Let $F$ be a fairness criterion (such as EF1 and MMS) and $F(I)$ be the set of all allocations satisfying $F$.

Definition of PoF: 
$$
PoF=\sup_{I}\min_{A\in F(I)}\frac{OPT(I)}{SW(A)}\\
=\frac{\max_{D\in Divisions}SW(D)}{\max_{D\in Fair\ Divisions}SW(S)}
$$
If no fair allocation can achieve non-zero welfare, the PoF is infinite.

The price of fairness w.r.t. fairness criterion $F$ is also called the price of $F$.

(Bei et al. 2021) first consider the PoF w.r.t. relaxations of EF. For instances with 2 agents, the price of EF1 is at least $8\over7$.

An upper bound on the price of EF1 follows by a variant of the Adjusted-Winner algorithm (Brams and Taylor 1996). The idea is to sort the goods according to the ratios between the values that they yield to the 2 agents:
$$
\frac{v_1(g_1)}{v_2(g_1)}\ge\frac{v_1(g_2)}{v_2(g_2)}\ge\cdots\ge\frac{v_1(g_m)}{v_2(g_m)}
$$
Intuitively, the first good is more valuable to agent 1 while the last good is more valuable to agent 2. If a minimal set of consecutive goods from left is allocated to the first agent s.t. the allocation is EF1 for her and the remaining goods are given to agent 2, then the allocation is EF1 with social welfare that is within a $\frac{\sqrt 3}{2}$-fraction of the optimal social welfare.

For arbitrary number of agents

- (Bei et al. 2021) showed that a lower bound of $\Omega(\sqrt n)$ on the price of EF1.
- (Barman et al. 2020) managed to give a tight upper bound of $O(\sqrt n)$, also showed tight bounds for other fairness notions, for $1\over2$-MMS and Prop1.
- (Halpern and Shah 2021) showed tight bounds on the price of EF1 and of approximate MMS under the constraint of having only ordinal information about the agent values.
- A typical assumption made in the context of distortion in social choice (Anshelevich et al. 2021).

### Fair Division with Strategic Agents

In the strategic version of the problem, an agent may intentionally misreport the values of goods in order to end up with a better bundle.

This introduces an additional layer of difficulty as goal is to produce fair allocations *according to the true values of the agents* while declarations might be not true.

This version of problem is considered mostly from a mechanism design *without payment* perspective, in which the utility of an agent is defined as her true value for her bundle.

#### Direction 1: Both truthful and fair mechanism.

- (Caragiannis et al. 2009) showed that no truthful mechanism for 2 agents and 2 goods can always output allocations of minimum envy.
- (Amanatidis et al. 2016) revisited the problem for the case of 2 agents and any number of goods, and showed that no truthful mechanism can always output $\alpha$-MMS allocations for $\alpha\gt\frac{2}{m}$.
- **Truthfulness and fairness are incompatible** (Amanatidis et al. 2017) This conclusion does not apply to restricted cases.
- For binary valuations, maximizing the Nash welfare results in a polynomial-time truthful mechanism that outputs EF1 and PO allocations. Analogous result w.r.t. MMS.
- (Babaioff et al) also showed that for the submodular analog of binary valuations, there is a truthful mechanism that always outputs EFX allocations.

The impossibility results led to different directions.

#### Direction 2: Stable states of non-truthful mechanisms.

- (Amanatidis et al. 2021) studied mechanisms that always have pure Nash equilibria, and showed that every allocation that corresponds to an equilibrium of Round-Robin is EF1 w.r.t. the true value of the agents.
- Similar results for approximate pure Nash equilibria, even for agents with submodular valuation functions (Amanatidis et al. 2023)
- Strategic setting for general sequential allocation algorithms. (Bouveret and Lang 2014; Aziz et al. 2017)

:question:**Open Problem 10: Are there mechanisms that always have pure Nash equilibrium allocations with stronger guarantees than EF1?**

#### Direction 3: Relaxing the requirements of truthfulness

In order to escape from the impossibility.

*Not obviously manipulability* (Psomas and Verma 2022)

- A mechanism is not obviously manipulable (NOM) if no agent can increase her best- and worst-case value by lying.

Fortunately, Round-Robin is NOM thus EF1 and NOM are compatible.

EF1, PO and NOM can be achieved simultaneously.

## Different Setting

Further meaningful discrete fair division settings.

### Limited Information

Only ordinal information is available.

Given only the ordinal preferences of the agents: Envy-Cycle Elimination not works. Sequential-picking algorithm instead.

Round-Robin algorithm can obtain EF1 only needs to know which of the available goods every agent values the most in each round.

Harder to compute approximate MMS allocations.

Let $H_n=\sum_{i=1}^n\frac{1}{i}$, (Halpern and Shah 2021) showed that with only ordinal information, it is impossible to achieve better than $\frac{1}{H_n}$-MMS

(Amanatidis et al. 2016) showed that $\frac{1}{2H_n}$-MMS allocations can be computed.

(Hosseini et al. 2021) showed the existence of PO and MMS or EFX allocations when agents have lexicographic preferences.

Another question: Query complexity of unknown valuations.

- The algorithm can access the valuations by making queries to an oracle.
- (Oh et al. 2021) proved that $\Theta(\log m)$ queries suffice to define an algorithm that returns EF1 allocations.
- It's an important research direction to explore the relation between the amount of information and fairness guarantees.

### General Valuations

#### Variant of additive valuations

submodular, fractional subadditive, subadditive

Submodular: $v_i(S)+v_i(T)\ge v_i(S\cup T)+v_i(S\cap T)$

Subadditive: $v_i(S)+v_i(T)\ge v_i(S\cup T)$

XOS: finite number of additive functions $a_1,\cdots,a_k$ s.t. $v_i(S)=\max_{l\in[k]}a_l(S)$

Any additive function is submodular, any submodular function is XOS, any XOS function is subadditive.

The Envy-Cycle Elimination algorithm returns an EF1 allocation even when the valuation functions of the agents are as general as possible.

#### MMS

For MMS allocations, if the functions are not restricted, no approximation guarantee can be achieved.

Some literature focus on structured valuations:

- (Barman and Krishnamurthy 2020) proved that the Round-Robin algorithm returns a $0.21$-approximate MMS fair allocation for submodular valuations.

  The guarantee was improved to $1\over3$ by (Ghodsi et al. 2022), who complemented with an upper bound of $3\over4$.

- For XOS valuations, (Ghodsi et al. 2022) proved the existence of $1\over5$-MMS allocations, and designed a polynomial-time algorithm to compute $1\over8$-MMS allocations.

  The existence result was improved to $1\over4.6$ by (Seddighin and Seddighin 2022)

  (Ghodsi et al 2022) showed that no algorithm can do better than $1\over2$-approximation for all XOS valuations.

- For subadditive valuations, (Seddighin and Seddighin 2022) proved the existence of a $\Omega(\frac{1}{\log n\log\log n})$-MMS allocation, later improved the $\Omega(\frac{1}{\log m})$ guarantee by (Ghodsi et al. 2022).

Best known approximations of MMS for general valuations:

|             |             lower bound              | upper bound |
| :---------: | :----------------------------------: | :---------: |
|  Additive   |     $\frac{3}{4}+\frac{1}{12n}$      | $39\over40$ |
| Submodular  |              $1\over3$               |  $3\over4$  |
|     XOS     |             $1\over4.6$              |  $1\over2$  |
| Subadditive | $\Omega(\frac{1}{\log n\log\log n})$ |  $1\over2$  |

#### EFX

- (Plaut and Roughgarden 2020) proved that $1\over2$-EFX allocations always exist for general valuations.

  (Chan et al. 2019) showed that computing such allocations can be done in polynomial time.

- (Akrami et al. 2022) proved that, for instances with three agents, if one of them has an additive valuation and the others have general valuations, an exact EFX allocation always exists.

### Arbitrary Entitlements

Fairness of an allocation must be considered w.r.t. asymmetric entitlements. In many inheritance scenarios, closer relatives have higher entitlements than distant ones.

Formally, in *weighted* and *asymmetric* setting, each agent $i$ has an entitlement $0\lt s_i\lt1$ s.t. $\sum_{i\in N}s_i=1$

The traditional setting with symmetric agents is a special case when $s_i=\frac{1}{n}$ for every $i$.

Generalized fair notions like weighted EF1, weighted MMS

- An allocation is weight envy-free (WEF) if $\frac{v_i(A_i)}{s_i}\ge\frac{v_i(A_j)}{s_j}$ for every pair of agents $i,j\in N$.
- An allocation is weighted envy-free up to one good (WEF1) if $\frac{v_i(A_i)}{s_i}\ge\frac{v_i(A_j\diagdown\{g\})}{s_j}$ for every pair of agents $i,j\in N$ and some $g\in A_j$.

(Chakraborty et al. 2021) proved that WEF1 allocations always exist and can be computed by a weight-dependent sequential picking algorithm that generalizes the Round-Robin algorithm.

Adapting MMS to the weighted setting (Not straightforward as WEF1):

- Underlying idea is to define a fairness ratio for each agent. Ideally when the items are divisible, the fairest partition for agent $i$ is such that $v_i(A_j)=s_j\cdot v_i(M)$ for all $j$. However since the items are indivisible, it's inevitable to induce some some degree of unfairness and the best fairness ratio for agent $i$ is:
  $$
  F_i=\max_{A\in\mathcal A_n(M)}\min_{j\in N}\left\{\frac{v_i(A_j)}{s_j\cdot v_i(M)}\right\}
  $$

- An allocation $A$ is weighted maximin share fairness (WMMS) if $v_i(A_i)\ge\mu_i^n=F_i\cdot s_i\cdot v_i(M)$ for every agent $i\in N$. For any $\alpha\in[0,1]$, an allocation $A$ is $\alpha$-WMMS if $v_i(A_i)\ge\alpha\cdot\mu_i^n$ for every agent $i\in N$.

  $F_i\le1,\mu_i^n\le s_i\cdot v_i(N)$

- When agents are symmetric, WMMS is MMS.

- (Farhadi et al. 2019) proved that with arbitrary entitlements, the best approximation ratio of WMMS is $1\over n$, which is somewhat surprisingly guaranteed by the Round-Robin algorithm.

Some novel fairness notions highlight different perspectives of the weighted setting

- *$l$-out-of-$d$ share*

- *AnyPrice* (APS)

  Maximum value an agent can guarantee to herself if she has a budget equal to her entitlement and the goods are adversarially priced.

  In unweighted setting, the APS value is at least as much as that of MMS, sometimes strictly larger.

  (Babaioff et al. 2021) showed efficiently computing an allocation where every agents gets value no less than $3\over5$ of her APS.

  (Aziz et al. 2020) considered weighted PROP1 and presented an algorithm for computing a weighted PROP1 and PO allocation for indivisible items.

### Group Fairness

Focus on group-wisely fair.

For **divisible** items, (Berliant et al. 1992) defined the notion of *group envy-freeness* (GEF) by restricting envy-freeness to hold for groups of agents with equal size. (strictly stronger than requirement for EF)

(Conitzer et al. 2019) extended GEF for **indivisible** items, leading to the notion of group-fairness (GF), which take into account groups of different size.

An allocation is **group-fair** if for any non-empty subset of agent $S,T\subseteq N$ and every partition $(B_i)_{i\in S}$ of $\bigcup_{j\in T}A_j$, $((\frac{|S|}{|T|})\cdot v_i(B_i))_{i\in S}$ does not Pareto dominate $(v_i(A_i))_{i\in S}$.

When $|S|=|T|=1$ and $|S|=|T|=n$ imply the traditional requirements of EF and PO.

(Conitzer et al. 2019) and (Aziz and Rey 2020) introduced different variants of "up to one" relaxations of GEF/GF and proved existential and hardness results.

(Suksompong 2018) focused on a setting where each agent drives full value from all the goods allocated to the group she belongs to, and showed bounds on the best possible approximation of MMS.

(Kyropoulou et al. 2020) considered EF1 and EFX allocations in the same setting, as well as in settings with dynamic group formation.

### Randomness in Fair Division

Randomization can be used as a tool to circumvent impossibilities in the task of allocating indivisible resources.

#### Best of Both Worlds

Envy-freeness w.r.t. the expected values (*ex-ante* EF)

In Round-Robin, agents have an advantage over those who are ordered after them. To eliminate this unfair advantage, we consider a randomized version where the ordering of the agents is picked at random.

(Freeman et al. 2020) showed that this randomized Round-Robin guarantees ex-ante PROP but not ex-ante EF.

The Probabilistic-Serial (PS) algorithm (Bogomolnaia and Moulin 2001) is a randomized algorithm for allocating indivisible items in an ex-ante EF manner. Agents eat their most preferred good at a uniform rate and, once consumed, move on to the next available good. The algorithm terminates when all goods have been consumed.

(Freeman et al. 2020) presented a variant of this procedure, the recursive probabilistic serial algorithm that maintains the ex-ante EF property. Moreover every realized allocation is ex-post EF1.

(Aziz 2020) showed that the random allocation generated by the original probabilistic serial algorithm is equivalent to lottery over a set of EF1 allocations.

(Aziz and Brandl 2020) presented an eating algorithm that is suitable for any type of feasibility constraint and allocation problem with ordinal preferences.

(Caragiannis et al. 2021) studied *interim* envy-freeness, a notion which lies between ex-ante and ex-post envy-freeness.

#### Stochastic Settings

When the values are randomly drawn from some probability distributions, an envy-free allocation is likely to exist as long as the number of items is sufficiently large compared to the number of agents.

(Dickerson et al 2014) showed that when the values of the agents are independently drawn from an identical distribution, the social welfare maximizing allocation is EF with high probability when $m=\Omega(n\log n)$.

(Manurangsi and Suksompong 2020, 2021) improved the lower bound on the existence of envy-free allocations, and showed that the Round-Robin algorithm produces an envy-free allocation for a slightly lower $m$. (Bai and Gölz\ 2022) extended this result to the case of asymmetric distributions.

(Bai et al. 2022) showed that given a worst-case instance that does not admit any envy-free allocation, randomly perturbing the values of each agent leads to the existence of envy-free allocations with high probability.

### Online Fair Division

Online fair division consider settings where the agents or the goods arrive in an online manner.

$T$ items arrive online; when an item arrives, the values of the agents for it are realized and based on these we need to decide immediately and irrevocably how to allocate it, typically without knowing future events. (don't know how many items and value of these items)

Let $\text{Envy}_T$ be the cumulative envy until time $T$, i.e., the maximum difference between any agent's value for goods allocated to another agent and to herself in the first $T$ rounds. It is desired that the envy can vanish as time goes on $\lim_{T\rarr+\infty}\frac{\text{Envy}_T}{T}\rarr0$.

(Benade et al. 2018) proved that by allocating the new good to an agent chosen uniformly at random, $\text{Envy}_T=\tilde O(\sqrt\frac{T}{n})$, thus we can indeed eliminate the envy over time. They also showed that there exists a deterministic polynomial-time algorithm with the same envy bound as the random allocation algorithm, this bound is asymptotically optimal against an adaptive adversary meaning that the allocation is far from being EF1.

(He et al. 2019) relaxed the requirement of irrevocable decision by allowing the algorithm to reallocate previously allocated items. (If we reallocate all goods at each time i.e., $\Theta(T^2)$, we can surely achieve EF1. meaningless!) They showed that with 2 agents we actually only need $\Theta(T)$ allocations; for more than 2 agents, $O(T^\frac{3}{2})$ reallocation are sufficient and $\Omega(T)$ are necessary.

It remains to open question whether there exist competitive online algorithm for the computation of approximately MMS or Prop1/PropX allocations. For the case of identical valuation functions, approximate MMS allocation correspond to maximizing the minimum load on the job scheduling problem.

The alternative model that considers a fixed set of resources and agents who arrive or depart over time has not been considered for indivisible resources, partially because it is very challenging to achieve positive results.

### Subsidies

Consider the question of whether it is possible to pay the agents just a small amount of money on top of a given allocation in order to make it envy-free.

Allocations for which this can be done are called envy-freeable.

A pair $(A,p)$ consisting of an allocation $A$ and a payment profile $p=(p_1,\cdots,p_n)$ where $p_i\ge0$ for every $i$, is envy-free if $v_i(A_i)+p_i\ge v_i(A_j)+p_j$. An allocation $A$ is envy-freeable if there are payments $p$ s.t. $(A,p)$ is envy-free.

(Halpern and Shah 2019) noted that not all allocations are envy-freeable. $A$ is envy-freeable only if $A$ maximize the utilitarian welfare across all reassignments of its bundles to agents.

A natural question in this setting: Quantifying the minimum total amount of subsidy $\sum_ip_i$ required to find an envy-free allocation.

- (Halpern and Shah 2019) showed that if a fixed envy-freeable allocation is given, the minimum total subsidy can be computed in polynomial time. However, finding the envy-freeable allocation with overall minimum total subsidy is an NP-hard problem.
- Deciding the existence of an envy-free allocation without any subsidy is NP-hard (Boveret and Lang 2008)
- (Caragiannis and Ioannidis 2021) provided additive approximation guarantees and hardness results for computing an envy-freeable allocation that minimizes the total amount of subsidies.

To bound the minimum subsidy required in the worst case over allocations,

- (Halpern and Shah 2019) proved that any envy-freeable allocation requires no more than $(n-1)m\cdot v^*$ total subsidy, where $v^*$ is the maximum value of an agent for a good.
- If we are able to choose the allocations, the $n-1$ factor cannot be removed.

### Mixtures of Indivisible and Divisible Items

Scenarios involved both indivisible and divisible goods.

(Bei et al. 2021) proposed a new fairness notion called *envy-freeness for mixed goods* (EFM), which is a generalization of both EF and EF1 to the mixed goods setting.

Key idea behind EFM

1. An agent $i$'s envy towards another agent $j$ vanishes if an indivisible good is removed from consideration when $j$ only gets indivisible goods,

   or

2. Agent $i$ does not envy agent $j$ when $j$ gets some divisible goods.

They proved that an EFM allocation always exists for any number of agents with additive valuations.

They examined the same setting and explored approximations of MMS.

(Bhaskar et al. 2021) showed that for a mixed resources model consisting of indivisible items and a divisible, undesireble heterogeneous resource, an EFM allocation always exists.

### Chores and Mixed Manna

Consider similar questions when items can be seen as chores (can be negatively valued) or mixed manna (a mixture of both goods and chores)

The definition of EF, PROP and MMS remain the same. As in the case of goods, the existence of EF or PROP allocations is rarely guaranteeed.

- For additive valuations, (Aziz et al. 2017) proved that the Round-Robin algorithm returns a $2$-MMS allocation. Later improved to $4\over3$ and $11\over9$. On the negative side, (Feige et al. 2021) proved that no algorithm can ensure an approximation factor better than $44\over43$.

- If the algorithm has access only to the ordinal preferences of agents, the best approximation of MMS can be guaranteed between $7\over5$ and $5\over3$ (Aziz et al. 2022).

- When the valuations are submodular, (Li et al. 2022) proved that no algorithm guarantees a better than $n$-MMS allocation. (Aziz et al. 2019) considered the case when the agents have asymmetric entitlements and extended the notion of weighted MMS to chores, but the tight approximation ratio is still unknown.

- Relaxations of EF and PROP need to be changed a bit to capture the fact that the agents have negative values for chores. Essentially, instead of removing goods from bundles of other agents to eliminate the envy of an agent, we nned to remove chores from the bundle of the agent her self.

Definition for EF1 and EFX for chores: For chores, an allocation $A$ is $\alpha$-EF1 if $v_i(A_i\diagdown\{g\})\ge\alpha\cdot v_i(A_j)$ for any pair of agents $i,j$ and some $g\in A$; $A$ is $\alpha$-EFX if the inequality holds for any $g\in A$.

- For additive valuations, an EF1 allocation can be easily computed by Round-Robin algorithm.

- (Bhaskar et al. 2021) noted that the allocation returned by Ency-Cycle Elimination algorithm may not be EF1 if the cycles are resolved arbitrarily. Instead, the top-trading technique must be used to preserve EF1.

  In contrast to the case of goods where EF1 and PO can be satisfied simultaneously, the compatibility of EF1 and PO is still unknown for chores.

- (Ebadian et al. 2022; Garg et al. 2022; Chaudhury et al. 2022) proved that for bi-valued instances, EF1+PO allocations always exist and can be found efficiently; this is the only known result so far.

- The existence of EFX allocations for chores also remains open, with the only positive result being the computation of $O(n^2)$-EFX allocations due to (Zhou and Wu 2022).

### Other Settings

Some allocations may not be feasible due to various restrictions, such as connectivity, cardinality, separation or budet constriants.

- (Suksompong 2021) discuss this part in detail.
- (\Sheddighin et al. 2021) and (Aziz et al. 2021) studied externalities in fair division, where agents not only have value for what they get but also for what others get.
- (Conitzer et al. 2017; Fain et al. 2018; Garg et al. 2021) related to public resource allocaation whre the items can be shared by the agents.

