---
title: Fair AI/ML 1 Core-Stability Federated Learning
author: tinsir888
date: 2024/9/20
cover: /img/AUFairAI.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
  - 人工智能
categories:
  - AUDatalogi
  - 算法博弈论
  - AI/ML 公平性
abbrlink: 7e9f3293
---

I will begin a new project work 24 Fall: *FAIRNESS IN AI/ML THROUGH THE LENS OF SOCIAL CHOICE*

The basic outline is following [*Tutorial: Fairness in AI/ML via Social Choice by Evi Micha & Nisarg Shah*](https://www.cs.toronto.edu/~nisarg/tutorials/fair-AI-SC.html) in EC 2024.

The first paper I have read in last semester, [*Fairness in Federated Learning via Core-Stability*](https://arxiv.org/abs/2211.02091), which was one of the possible topics.

Brief notes is [here](https://tinsir888.github.io/posts/248b382e.html).

~~I will try to understand some mathematical proof in this semester.~~

# Core-Stability

Revisit the definition of **Core-Stability**: A predictor $\theta\in P$ is called core stable if there exist no other $\theta'\in P$ and no subset $S$ of agents, s.t. $\frac{|S|}{n}u_i(\theta')\ge u_i(\theta)$ for all $i\in S$, with at least one strict inequality.



!!! warning

    :warning::warning::warning:**What is WRONG with the claim**

    > Setting $S=[n]$ in definition, there is no predictor $\theta'\in P$ where $\sum_{i\in[n]}\frac{u_i(\theta')}{u_i(\theta)}\gt n$.

    According to the definition, when we plug in $S=[n]$, there exist no $\theta'\in P$ s.t.

    $$
    u_i(\theta')\ge u_i(\theta)\forall i\in S\\
    \Leftrightarrow\frac{u_i(\theta')}{u_i(\theta)}\ge 1\forall i\in S
    $$

    with at least one strict inequality.

**The problem is, can we directly sum it up, then we have** $\sum_i\frac{u_i(\theta')}{u_i(\theta)}\gt n$???

There is a counterexample:

- $n=2$, $\theta$ is core-stable, we only have two feasible predictors $\theta,\theta':\theta'\neq\theta$, $\epsilon\to0^+$.

  |           |  agent $1$   |  agent $2$   |
  | :-------: | :----------: | :----------: |
  | $\theta$  |     $1$      |     $1$      |
  | $\theta'$ | $2-\epsilon$ | $1-\epsilon$ |

- Verifying the core-stability of $\theta$:

  - For $S=\{1\}$, we have $\frac{|S|}{n}u_i(\theta')=\frac{1}{2}u_1(\theta')=1-\frac{\epsilon}{2}\lt1=u_1(\theta)$. Which satisfy the condition.
  - For $S=\{2\}$, we have $\frac{1}{2}u_2(\theta')=\frac{1-\epsilon}{2}\lt1=u_2(\theta)$. Which satisfy the condition.
  - For $S=\{1,2\}$, we have $u_1(\theta')=2-\epsilon\gt1=u_1(\theta)$ and $u_2(\theta')=1-\epsilon\lt1=u_2(\theta)$. Which also satisfy the condition that some scaled utility is less than the original utility.

  But $\sum_i\frac{u_i(\theta')}{u_i(\theta)}=\frac{u_1(\theta')}{u_1(\theta)}+\frac{u_2(\theta')}{u_2(\theta)}=3-2\epsilon\gt2$!



!!! info

    Scaled factor $|S|/n$ suggests some kind of proportionality here. Basically core-stability means there is **no big benefit** for agent to form a coalition.



Core means **"no deviating subgroup"**

Core stability is very strong, only exists in special cases.

## Advantage

More fair than FedAvg. $M$ is a very large integer.

|            | Agent 1 | Agent 2 |    Agent 3    |
| :--------: | :-----: | :-----: | :-----------: |
| $\theta_1$ |   $a$   |   $a$   |  $M\cdot a$   |
| $\theta_2$ |  $10a$  |  $10a$  | $0.9M\cdot a$ |

In FedAvg, $\theta_2$ is preferred, but not fair to Agent 1 and 2. (They have incentive to collide.)

Robustness to poor local data quality of agents.

:exclamation:Scaling the utility of any single agents does not change the core-stable allocation.

# :book:Some Ideas around Sum-of-ratio Property

## Is there scenario s.t. none of predictors is core-stable?

Yes. The example

|           |  agent $1$   |  agent $2$   |
| :-------: | :----------: | :----------: |
| $\theta$  |     $1$      | $2+\epsilon$ |
| $\theta'$ | $2+\epsilon$ |     $1$      |

shows that none of $\theta,\theta'$ is core-stable.

## Some New Definitions

Ideas from Iannis. I was amazed by his ability to invent some new notions :open_mouth:

:book:Definition of pessimistic price of core-stability:
$$
PoCS_{pessimistic}=\max_{\theta\text{ is core-stable}}\max_{\theta'\in P}\sum_{i\in[n]}\frac{u_i(\theta')}{u_i(\theta)}
$$
:book:Definition of optimistic price of core-stability:
$$
PoCS_{optimistic}=\min_{\theta\text{ is core-stable}}\max_{\theta'\in P}\sum_{i\in[n]}\frac{u_i(\theta')}{u_i(\theta)}
$$

## Conjecture about Price of Core-Stability (Has been disproved)

**If the set of core-stable predictors is non-empty, does one predictor in the set be the optimistic price of core-stability?**

We want to proof/disproof this conjectures.

Let's consider the scenario with two agents.

| agents\predictors | $\theta$ | ...  | $\theta''$ | ...  |
| :---------------: | :------: | :--: | :--------: | :--: |
|         1         |    1     | ...  |     ?      | ...  |
|         2         |    1     | ...  |     ?      | ...  |

Assume that $\theta$ is core-stable. The question is, does there exist predictor $\theta''$ that minimizing $\max_{\theta'\in P}\sum_{i\in[n]}\frac{u_i(\theta')}{u_i(\theta'')}$ and not core-stable? If the answer is true, then we disproof the conjecture.

## Disprove it

Consider the scenario with 2 agents and 3 feasible predictors:

| agents\predictors | $\theta_1$ |  $\theta_2$  |  $\theta_3$  |
| :---------------: | :--------: | :----------: | :----------: |
|         1         |    $1$     | $1-\epsilon$ |     $2$      |
|         2         |    $1$     |     $2$      | $1-\epsilon$ |

By symmetry, $\theta_2$ and $\theta_3$ are equivalent.

### Check whether $\theta_1$ is core-stable

- Consider $S=\{1\}$:
  $$
  \frac{1}{2}u_1(\theta_2)=\frac{1-\epsilon}{2}\lt 1=u_1(\theta_1)=1\\
  \frac{1}{2}u_1(\theta_3)=\frac{2}{2}=1\le 1=u_1(\theta_1)=1
  $$

- Consider $S=\{2\}$:
  $$
  \frac{1}{2}u_2(\theta_3)=\frac{1-\epsilon}{2}\lt 1=u_2(\theta_1)=1\\
  \frac{1}{2}u_2(\theta_2)=\frac{2}{2}=1\le 1=u_2(\theta_1)=1
  $$

- Consider $S=\{1,2\}$:
  $$
  \frac{2}{2}u_1(\theta_2)=1-\epsilon\lt u_1(\theta_1)=1,\frac{2}{2}u_2(\theta_2)=2\gt u_2(\theta_1)=1\\
  \frac{2}{2}u_1(\theta_3)=2\gt u_1(\theta_1)=1,\frac{2}{2}u_1(\theta_3)=1-\epsilon\lt u_1(\theta_1)=1
  $$

There is no subset satisfying all scaled value is greater or equal to original value. Thus, $\theta_1$ is core-stable.

### Check whether $\theta_2,\theta_3$ are core-stable

It suffices to check only $\theta_2$.

- Consider $S=\{1\}$:
  $$
  \frac{1}{2}u_1(\theta_1)=\frac{1}{2}\lt u_1(\theta_2)=1-\epsilon\\
  \frac{1}{2}u_1(\theta_3)=1\gt u_1(\theta_2)=1-\epsilon
  $$
  Here scaled utility of $\theta_3$ is strictly better than that of $\theta_2$.

Thus $\theta_2,\theta_3$ are not core-stable.

### Check the Sum of Ratio

For $\theta_1$:
$$
\sum_{i\in\{1,2\}}\frac{u_i(\theta_2)}{u_i(\theta_1)}=3-\epsilon\\
\sum_{i\in\{1,2\}}\frac{u_i(\theta_3)}{u_i(\theta_1)}=3-\epsilon\\
\therefore\max_{\theta'\neq\theta_1}\sum_{i\in\{1,2\}}\frac{u_i(\theta')}{u_i(\theta_1)}\approx3
$$
For $\theta_2$:
$$
\sum_{i\in\{1,2\}}\frac{u_i(\theta_1)}{u_i(\theta_2)}=\frac{1}{2}+\frac{1}{1-\epsilon}\approx\frac{3}{2}\\
\sum_{i\in\{1,2\}}\frac{u_i(\theta_3)}{u_i(\theta_2)}=\frac{2}{1-\epsilon}+\frac{1-\epsilon}{2}\approx\frac{5}{2}\\
\therefore\max_{\theta'\neq\theta_2}\sum_{i\in\{1,2\}}\frac{u_i(\theta')}{u_i(\theta_2)}\approx\frac{5}{2}
$$
By symmetry, $\max_{\theta'\neq\theta_3}\sum_{i\in\{1,2\}}\frac{u_i(\theta')}{u_i(\theta_3)}\approx\frac{5}{2}$.

The max of sum is not minimized by the only core-stable predictor.

# Core-Stability in FL

CoreFed exists in Linear Regression and Logistic Regression (Classification)

Approximate core stable predictor exists in Deep Neural Network.

## Existence

:thinking:Core-Stability exist in FL if:

1. Utility function is **continuous**.

2. The **conical combination** of utility functions is convex.

   $\forall\langle\alpha_1,\cdots,\alpha_n\rangle\in\mathbb R_{\ge0}^n$, the set $C=\{\theta|\sum_{i\in[n]}\alpha_iu_i(\theta)\}$ is convex.

:thinking:Important theorem: Kakutani's Fixed Point Theorem: A *set valued function* $\phi:D\to2^D$ admits a fixed point, i.e. $\exists d\in D$, s.t. $d\in\phi(d)$, if

1. $D$ is non-empty, compact, and convex

!!! info

    Compact: The set is closed (It must contains all its boundary points), and bounded (It can be covered by a "sphere" with certain radius)

    Convex: A set is **convex** if, for any two points $x$ and $y$ in the set, the line segment connecting $x$ and $y$ also lies entirely within the set. Mathematically, for any $x,y\in S$ and any $t \in [0, 1]$, the point: $tx+(1−t)y\in S$.



2. $\forall d\in D,\phi(d)$ is non-empty, compact, and convex.
3. $\phi(\cdot)$ has a **closed graph**. i.e., $\forall$ sequence $(d_i)_{i\in\mathbb N}$ converging to $d^*$ and $(e_i)_{i\in\mathbb N}$ converging to $e^*$, s.t. $d_i\in D$ and $e_i\in\phi(d_i)$, we have $e^*\in\phi(d^*)$.

### Define Core-Stable Feasible Predictors

$\forall\theta\in P$, $\phi(\theta)=\{\arg\max_{d\in P}\sum_{i\in[n]}\frac{u_i(d)}{u_i(\theta)}\}$.

Lemma 4.1: Let $\theta\in P$ be s.t. $\theta\in\phi(\theta)$ (It is a fixed point). Then $\theta$ is a core-stable predictor.

> Proof by contradiction.

By Takutani's Theorem, it suffices to show that $\phi(\cdot)$ has a closed graph, to ensure that it admits a fixed point.

Lemma 4.2: $\phi(\cdot)$ has a closed graph.

> Hard proof.

:thinking:Theorem 1. In FL, where utilities are continuous and any conical combination of utilities is convex, a core-stable predictor exists.

:warning:If we change $d\in P$ to $d\in\mathcal B(\theta, r)$, if the two condition still holds within a radius of $r$ of every point. The conditions tend to be true in DNN of small $r$. Then the predictor is **locally** core-stable.

# Computation of Core-Stable Predictor

There is efficient way to compute core-stable predictor.

It is optima of convex program:
$$
\text{maximize }\mathcal L(\theta)=\sum_{i\in[n]}\log(u_i(\theta))\text{ or }\prod_{i\in[n]}u_i(\theta)\\
\text{subject to }\theta\in P
$$
~~The object reminds me of Nash Social Welfare~~:sunglasses:

Utility of each agent is concave, thus above program is convex. Thus this program is concave maximization subject to convex constraint.

:thinking:Theorem 2. If $u_i$ is concave for all $i$, then any predictor $\theta^*$ maximizes the convex program is core stable.

> Proof by contradiction
> $$
> \sum_{i\in[n]}\frac{u_i(\theta')}{u_i(\theta^*)}\le n.
> $$
> If $\theta^*$ is not core-stable, there must exist $\theta'$ and subset $S$, s.t.
> $$
> u_i(\theta')\ge\frac{n}{|S|}u_i(\theta^*)
> $$
> for all $i$ with at least one strict inequality.
>
> Thus for any $\theta\neq\theta'$,
> $$
> \sum_{i\in[n]}\frac{u_i(\theta)}{u_i(\theta')}\ge\sum_{i\in S}\frac{u_i(\theta)}{u_i(\theta')}\gt n/|S|\cdot|S|=n,
> $$
> admitting contradiction.

$\theta^*$ must be Pareto Optimal.

## CoreFed

Stochastic Gradient Descent.

$n$ finite samples $\{(x_i,y_i)\}_{i\in[n]}$ drawn from data distribution $\mathcal P$, which constitute empirical distribution $\hat{\mathcal P}_n$. The gradient can be expressed as an **conical combination** of the gradients of each group:
$$
\nabla_\theta\mathcal(\theta)=\sum_{i\in[n]}\frac{\nabla_\theta u_i(\theta)}{u_i(\theta)}=\sum_{i\in[n]}\frac{-\nabla_\theta\mathbb E_{(x,y)\sim\hat{\mathcal P}_n^{(i)}}\ell(f_\theta(x),y)}{M_i-\mathbb E_{(x,y)\sim\hat{\mathcal P}_n^{(i)}}\ell(f_\theta(x),y)}
$$

For each group, reweight its gradients by $(M_i-\mathbb E_{(x,y)\sim\hat{\mathcal P}_n^{(i)}}\ell(f_\theta(x),y))^{-1}$ and then sum up to get the final weight update. This is base protocol.

In CoreFed the model weights are directly averaged and aggregated at each iteration.

### Pseudo Code of CoreFed

Input: Number of clients $K$, number of rounds $T$, epochs $E$, learning rate $\eta$.

Output: Model weights $\theta^\top$

1. For $t\gets0$ to $T-1$ do

   - Server selects a subset of $K$ devices $S_t$

   - Server sends weights $\theta^t$ to all selected devices

   - Each select device $s\in S_t$ updates $\theta^t$ for $E$ epochs of SGD with learning rate $\eta$ to obtain new weights $\bar\theta_s^t$

   - Each select devices $s\in S_t$ computes
     $$
     \Delta\theta_s^t=\bar\theta_s^t-\theta^t,\\
     \mathcal L_s^t=\frac{1}{|\mathcal D_s|}\sum_{i=1}^{|\mathcal D_s|}\ell(f_{\theta^t}(x_s^{(i)}),y_s^{(i)})
     $$
     where $\mathcal D_s=\{(x_s^{(i)},y_s^{(i)}):i\in[|\mathcal D_s|]\}$ is the training dataset on device $s$

   - Each selected device $s\in S_t$ sends $\Delta \theta_s$ and $\mathcal L_s$ back to the server

   - Server updates $\theta^{t+1}$ following
     $$
     \theta^{t+1}\gets\theta^t+\frac{1}{|S_t|}\sum_{s\in S_t}\frac{\Delta\theta_s^t}{M_s-\mathcal L_s^t}.
     $$

end

## Core-Stability in Linear Regression and Classification with Logistic Regression

Utility function is concave.

$u_i$ is concave $\Leftrightarrow \ell$ is convex

$n$ finite samples $\{(x_i,y_i)\}_{i\in[n]}$ drawn from data distribution $\mathcal P$, which constitute empirical distribution $\hat{\mathcal P}_n$.

### Linear Regression

$f_\theta(x)=\theta^\top x$.

Regression loss $\mathbb E_{(x,y)\sim\hat{\mathcal P}_n^{(i)}}\ell(f_\theta(x),y)=\mathbb E_{(x,y)\sim\hat{\mathcal P}_n^{(i)}}(\theta^\top x-y)^2$ would be convex in $\theta$

:thinking:Lemma: CoreFed determines a core-stable predictor in a federated learning setting training linear regression

### Classification with Logistic Regression

Given a classifier $\theta$ and a scalar $c\in R$, and agents $i$'s loss is $\ell_i(\theta,c)=\frac{||\theta||_2}{2}+\alpha\cdot\sum_{i\in[n]}\log(\exp(-y_i(\theta^\top x_i+c))+1)$. $\ell_i$ is convex.

Thus, $u_i(\theta,c)=M_i-\ell_i(\theta,c)$ where $M_i=\arg\max_{\theta\in P,c\in\mathbb R}(\ell_i(\theta,c))$ is concave.

:thinking:Lemma: CoreFed determines a core-stable predictor in a federated learning setting training classification with logistic regression.

## Approximate Core-Stability in DNN

Skip them!

