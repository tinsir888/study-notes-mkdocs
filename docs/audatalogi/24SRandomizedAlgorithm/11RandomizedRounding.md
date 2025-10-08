---
title: 随机算法 11 Randomized Rounding for MAX SAT
author: tinsir888
date: 2024/4/22
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: 4cc9c38c
---

# MAX SAT and MAX CUT

randomized ½ approximation for each problem

## MAX SAT

aka maximum satisfiability problem

- Input consists of $n$ Boolean variables $x_1,\cdots,x_n$, $m$ clauses $C_1,\cdots,C_m$.
  - Clauses $C_j$ consist of some number of variables and $\lor$ and a non-negative weight $w_j$.
- The objective of the problem is to find an assignment of $x_i$ that maximizes the weight of the satisfied clauses.
- A clause is said to be satisfied if one of the unnegated variables is set to `true`, or one of the negated variables is set to `false`.

Some terminology:

- **literal**. A variable $x_i$ or a negated variable $\bar x_i$ is a literal, so that each clause consists of some number of literals.
  - $x_i$ is called positive literal
  - $\bar x_i$ is called  negative literal
- **length**. The number of literals in a clause. Length of $C_i$ is $l_i$.
- Clause of length 1 is **unit clause**.

WLOG, assume that no literal is repeated in a clause, at most one of $x_i$ and $\bar x_i$ appears in a clause.

Assume the clauses are distinct.

A very straightforward use of randomization: set each $x_i$ to `true` independently with probability $1/2$.

:thinking:**Theorem 1**. Setting each $x_i$ to `true` with probability $1/2$ independently gives a randomized $1/2$-approximation algorithm for the MAX SAT problem.

> Define indicator variable $Y_j$: $1$ if clause $j$ is satisfied and $0$ otherwise.
>
> Let $W$ be a random variable that is equal to the total weight of the satisfied clauses, so that $W=\sum_jw_jY_j$.
>
> Let $OPT$ denote the optimum value of the MAX SAT instance. Then, by linearity of expectation, and the definition of the expectation of 0-1 random variable, we know
> $$
> \mathbb E[W]=\sum_{j=1}^mw_j\mathbb E[Y_j]=\sum_{j=1}^mw_j\Pr[C_j\text{ is satisfied}].
> $$
> For each $C_j$, the probability that it is not satisfied is the probability that each positive literal in $C_j$ is set to `false` and each negative literal is set to `true`, each of which happens with probability $1/2$. Hence,
> $$
> \Pr[C_j\text{ is satisfied}]=\left(1-(\frac{1}{2})^{l_j}\right)\ge\frac{1}{2},
> $$
> where the last inequality is a consequence of the fact that $l_j\ge1$. Hence,
> $$
> \mathbb E[W]\ge\frac{1}{2}\sum_{j=1}^mw_j\ge\frac{1}{2}OPT,
> $$
> where the last inequality follows from the fact that the total weight is an easy upper bound on the optimal value, since each weight is assumed to be non-negative.

If $l_j\ge k$ for each clause $j$, the algorithm is a $\left(1-(\frac{1}{2})^k\right)$-approximation algorithm for such instances. **Thus the longer the clauses, the better the approximation.**

Consider $l_j=3$ for all clauses, the problem turns into MAX E3SAT. By analysis above, this randomized algorithm gives guarantee of $7/8$ approximation. Nothing better is possible for these instances unless $P=NP$.

:thinking:**Theorem 2**. If there is an $(\frac{7}{8}+\epsilon)$-approximation algorithm for MAX E3SAT for any constant $\epsilon\gt 0$, then $P=NP$.

## MAX CUT

aka maximum cut problem

In this problem. the input is an undirected graph $G=(V,E)$, along with a non-negative weight $w_{ij}\ge0$ for each edge $(i,j)\in E$. The goal is to partition the vertex set into two parts $U$ and $W=V-U$, so as to maximize the weight of edges whose two endpoints are in different parts, one in $U$ and another in $W$.

An edge with endpoints in both $U$ and $W$ is *in the cut*. In the case $w_{ij}=1$ for each edge $\in E$, we have an **unweighted** MAX CUT problem.

We place each vertex $v\in V$ into $U$ independently with probability $1/2$. As with the MAX SAT algorithm, this can be viewed as sampling a solution uniformly form the space of all possible solutions.

:thinking:**Theorem 3**. If we place each vertex $v\in V$ into $U$ independently with probability $1/2$, then we obtain a randomized $1/2$-approximation algorithm for the maximum cut problem.

> Consider a random variable $X_{ij}$ that is $1$ if edge $(i,j)$ is in the cut, and $0$ otherwise.
>
> Let $Z$ be the random variable equal to the total weight of edges in the cut, so that $Z=\sum_{(i,j)\in E}w_{ij}X_{ij}$.
>
> Let $OPT$ denote the optimal value of the maximum cut instance. Then, as before, by linearity of expectation and the definition of expectation of a 0-1 random variable, we get that
> $$
> \mathbb E[Z]=\sum_{(i,j)\in E}w_{ij}\mathbb E[X_{ij}]=\sum_{(i,j)\in E}w_{ij}\Pr[(i,j)\text{ in cut}].
> $$
> The probability that a specific edge $(i,j)$ is in the cut: since the 2 endpoints are placed in the sets independently, they are in different sets with probability equal to $1/2$. Hence,
> $$
> \mathbb E[Z]=\frac{1}{2}\sum_{(i,j)\in E}w_{ij}\ge\frac{1}{2}OPT,
> $$
> where the inequality follows directly from the fact that the sum of the non-negative weights of all edges is obviously an upper bound on the weight of the edges in an optimal cut.

# Derandomization

Randomized algorithm for MAX SAT can be derandomized by replacing the randomized decision of whether to set $x_i$ to `true` with a deterministic one that will preserve the expected value of the solution. The decisions will be made sequentially.

Assume that $x_1$ is fixed, and other variables will be set `true` with probability $1/2$. Intuitively we should choose the value of $x_1$ which maximize the expected value of $W$. In this way, we should maintain the algorithm that the expected value is at least half the optimum, while having fewer random variables left.

If $\mathbb E[W|x_1\gets true]\ge\mathbb E[W|x_1\gets false]$, then we set $x_1$ to `true`, otherwise `false`. Then
$$
\mathbb E[W]=\mathbb E[W|x_1\gets ture]\Pr[x_1\gets true]+\mathbb E[W|x\gets false]\Pr[x_1\gets flase]\\
=\frac{1}{2}(\mathbb E[W|x_1\gets true]+\mathbb E[W|x_1\gets false])
$$
By induction, we can set the sequence of variables better than $1/2$.

# Flipping Biased Coins

Consider MAX SAT instance with no unit clauses $\bar x_i$. (This condition can be moved)

Suppose we set each $x_i$ to be `true` independently with probability $p\gt\frac{1}{2}$, then the probability that any given clause is satisfied is at least $\min(p,1-p^2)$ for MAX SAT instances with no negated unit clauses.

> 假设这些布尔表达式不含有单个非变量。
>
> 对于只含一个变量的布尔表达式，满足的概率为 $p$.
>
> 对于含有至少两个变量的布尔表达式，假设 $x_i$ 有 $a$ 个，$\bar x_j$ 有 $b$ 个，则该表达式不满足的概率为：
>
> $$
> \Pr[C_k\text{ not satisfied}]=(1-p)^ap^b\le p^{a+b}\le p^2\\
> $$
>
> $$
> \therefore\Pr[C_k\text{ satisfied}]\ge 1-p^2
> $$
>
> 现在就需要将 $\min(p,1-p^2)$ 最大化。

Best performance is $p=1-p^2\Rightarrow p\approx0.618$.

:thinking:**Theorem 4**. Setting each $x_i$ to `true` with probability $p$ independently gives a randomized $\min(p,1-p^2)$-approximation algorithm for MAX SAT instances with no negated unit clauses.

Let $v_i$ be the weight of the unit clause $\bar x_i$ if it exists in the instance, and let $v_i$ be zero otherwise.

$OPT$ has a tighter bound: $OPT\le\sum_{j=1}^mw_j-\sum_{i=1}^nv_i$.

Let $U$ be the set of indices of clauses of the instance excluding unit clauses of the form $\bar x_i$. WLOG the weight of each clause $\bar x_i$ is no greater than the weight of clause $x_i$. Thus $\sum_{j\in U}w_j=\sum_{j=1}^mw_j-\sum_{i=1}^nv_i$. Each $x_i$ is set to `true` with probability $0.618$. So the algorithm produces

$$
\mathbb E[W]=\sum_{j=1}^mw_j\Pr[C_j\text{ satisfied}]\\
$$

$$
\ge\sum_{j\in U}^mw_j\Pr[C_j\text{ satisfied}]\\
$$

$$
\ge p\cdot\sum_{j\in U}w_j\\
$$

$$
=p\cdot(\sum_{j=1}^mw_j-\sum_{i=1}^nv_i)\ge p\cdot OPT.
$$

This algorithm can be derandomized using the method of conditional expectations.

# Randomized Rounding

Biasing probability with which we set $x_i$ `true` yields an improved approximation algorithm. However, we gave each variable the same bias. ==We can do better by giving each variable its own bias!==

Idea of **randomized rounding**:

- First set up an integer programming formulation of the problem at hand in which there are 0-1 integer variables. 0-1 variable $y_i$ for each Boolean variable $x_i$ s.t. $y_i=1$ corresponds to $x_i$ set `true`.
- The integer program is relaxed to a linear program by replacing the constraints $y_i\in\{0,1\}$ with $0\le y_i\le 1$.
- The fractional value $y_i^*$ is interpreted as the probability that $y_i$ should be set to $1$. In this case we set each $x_i$ to `true` with probability $y_i^*$ independently.

For MAX SAT problem, in addition to the variables $y_i$, we introduce a variable $z_j$ for each $C_j$ that will be $1$ if the clause is satisfied and $0$ otherwise. For each clause $C_j$ let $P_j$ be the indices of the variables $x_i$ that are positive, $N_j$ be negated in the clause.

We denote the clause $C_j$ by

$$
\bigvee_{i\in P_j}x_i\lor\bigvee_{i\in N_j}\bar x_i.
$$

Then the inequality

$$
\sum_{i\in P_j}y_i+\sum_{i\in N_j}(1-y_i)\ge z_j
$$

must hold for $C_j$ since if each variable that occurs positively in the clause is set to `false` and each variable that occurs negatively is set to `true`, then the clause is not satisfied, and $z_j$ must be $0$.

This yields **integer programming** formulation of MAX SAT problem:

- maximize $\sum_{j=1}^mw_jz_j$
- subject to $\sum_{i\in P_j}y_i+\sum_{i\in N_j}(1-y_i)\ge z_j$, $\forall C_j=\bigvee_{i\in P_j}x_i\lor\bigvee_{i\in N_j}\bar x_i$,
- $y_i\in\{0,1\},i=1,\cdots,n$
- $0\le z_j\le 1,j=1,\cdots,m$

$Z^*_{LP}$ is the optimum value of this linear program $LP$, then clearly the optimum value of integer program (IP) $Z^*_{IP}=OPT$.

Let $(y^*,z^*)$ be the optimum value of solution to the linear programming, relaxation. Consider the result of using randomized rounding, and setting $x_i$ to `true` with probability $y_i^*$ independently. Before we can begin the analysis, we will need 2 facts. The first is commonly called the *arithmetic-geometric mean inequality*, because it compares the **arithmetic and geometric means** of a set of numbers.

$$
\sqrt[k]{\prod_{i=1}^ka_i}\le\frac{1}{k}\sum_{i=1}^ka_i
$$

If $f''(x)\le0\forall x\in[0,1],f(0)=a,f(1)=b+a$, then $f(x)\ge bx+a\forall x\in[0,1]$.

:thinking:**Theorem 5**. Randomized rounding gives a randomized $(1-\frac{1}{e})$-approximation algorithm for MAX SAT.

>Applying arithmetic-geometric mean inequality:
>
>$$
>\Pr[C_j\text{ not satisfied}]=\prod_{i\in P_j}(1-y_i^*)\prod_{i\in N_j}y_i^*\\
>$$
>
>$$
>\le\left[\frac{1}{l_j}(\sum_{i\in P_j}(1-y_i^*)+\sum_{i\in N_j}y_i^*)\right]^{l_j}
>$$

>By rearranging terms,
>
>$$
>\left[\frac{1}{l_j}(\sum_{i\in P_j}(1-y_i^*)+\sum_{i\in N_j}y_i^*)\right]^{l_j}=\left[1-\frac{1}{l_j}(\sum_{i\in N_j}(1-y_i^*)+\sum_{i\in P_j}y_i^*)\right]^{l_j}
>$$
>
>By the inequality Optimum of LP $\ge$ Optimum of IP, we see
>
>$$
>\Pr[C_j\text{ not satisfied}]\le\left(1-\frac{z_j^*}{l_j}\right)^{l_j}.
>$$
>
>Function $f(z_j^*)=1-\left(1-\frac{z_j^*}{l_j}\right)^{l_j}$ is concave for $l_j\ge1$, then
>
>$$
>\Pr[C_j\text{ satisfied}]\ge1-\left(1-\frac{z_j^*}{l_j}\right)^{l_j}\\
>$$
>
>$$
>\ge\left[\left(1-\frac{1}{l_j}\right)^{l_j}\right]z_j^*.
>$$
>
>Therefore the expected value of the randomized rounding algorithm is
>
>$$
>\mathbb E[W]=\sum_{j=1}^mw_j\Pr[C_j\text{ satisfied}]\\
>$$
>
>$$
>\ge\sum_{j=1}^mw_jz_j^*\left[\left(1-\frac{1}{l_j}\right)^{l_j}\right]\\
>$$
>
>$$
>\ge\min_{k\ge1}\left[\left(1-\frac{1}{k}\right)^{k}\right]\sum_{j=1}^mw_jz_j^*.
>$$
>
>$\left[\left(1-\frac{1}{k}\right)^{k}\right]$ is non-increasing. It approaches $1-\frac{1}{e}$ from above as $k$ tends to infinity. Thus we have
>
>$$
>\mathbb E[W]\ge\min_{k\ge1}\left[\left(1-\frac{1}{k}\right)^{k}\right]\sum_{j=1}^mw_jz_j^*\ge(1-\frac{1}{e})OPT.
>$$
>
>This randomized rounding algorithm can be derandomized in the standard way using the method of conditional expectations.

!!! info

    这个证明不太优雅，其实可以用 $e^x\ge x+1$ 进行放缩，将乘积化为 $e$ 的指数和的形式。

    然后再用线性规划的结果至少优于整数线性规划的结果，得到 $\Pr[C_i(x)=1]\ge1-\exp(-z_j^*)$。



# Choosing the Better of 2 Solutions

Consider a given clause $C_j$ of length $l_j$. The randomized rounding algorithm satisfies the clause with probability at least $\left[\left(1-\frac{1}{l_j}\right)^{l_j}\right]z_j^*$, while the unbiased randomized algorithm satisfies the clause with probability $1-2^{-l_j}\ge(1-2^{-l_j})z_j^*$.

When the clause is short, it is very likely to be satisfied by the randomized rounding algorithm, tough not by the unbiased randomized algorithm. When the clause is long, the opposite is true.

:thinking:**Theorem 6**. Choosing the better of the two solutions given by the randomized rounding algorithm and the unbiased randomized algorithm yields a randomized $3/4$-approximation algorithm for MAX SAT.

> Let $W_1$ be a random variable denoting the value of the solution by randomized rounding algorithm.
>
> Let $W_2$ be the results of unbiased randomized algorithm. To show:
>
> $$
> \mathbb E[\max(W_1,W_2)]\ge\frac{3}{4}OPT\\
> $$
>
> Observe that
>
> $$
> \mathbb E[\max(W_1,W_2)]\ge\mathbb E[\frac{1}{2}(W_1+W_2)]\\
> $$
>
> $$
> =\frac{1}{2}\mathbb E[W_1]+\frac{1}{2}\mathbb E[W_2]\\
> $$
>
> $$
> \ge\frac{1}{2}\sum_{j=1}^mw_jz_j^*\left[\left(1-\frac{1}{l_j}\right)^{l_j}\right]+\frac{1}{2}\sum_{j=1}^mw_j(1-2^{-l_j})\\
> $$
>
> $$
> \ge\sum_{j=1}^mw_jz_j^*\left[\frac{1}{2}\left(1-(1-\frac{1}{l_j})^{l_j}\right)+\frac{1}{2}(1-2^{-l_j})\right].
> $$
>
> Claim that
>
> $$
> \left[\frac{1}{2}\left(1-(1-\frac{1}{l_j})^{l_j}\right)+\frac{1}{2}(1-2^{-l_j})\right]\ge\frac{3}{4}
> $$
>
> for all positive integers $l_j$.
>
> ~~通过求导等方法可以求出这个式子的最小值，不太优雅啊……~~

Notice that taking the best solution of the two derandomized algorithms gives at least half of sum of two results.

Thus taking the best solution of the two derandomized algorithms is a deterministic $3/4$-approximation algorithm.

# Non-linear Randomized Rounding

$3/4$-approximation algorithm for MAX SAT can be obtained by using randomized rounding with a non-linear function $f$.

$f$ is a function from $[0,1]$ to $[1,0]$

Select $f$ s.t. $1-4^{-y}\le f(y)\le 4^{y-1}$ for every $y\in[0,1]$. Such an $f$ does exist.

Use the similar analysis of linear randomized rounding.