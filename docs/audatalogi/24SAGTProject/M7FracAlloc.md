---
title: Fair Division 7 Ex-ante and Ex-post Fairness
author: tinsir888
date: 2024/4/19
cover: /img/AUfd.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
categories:
  - AUDatalogi
  - 算法博弈论
  - 公平分配博弈
abbrlink: 1fd41289
---

Objective: Explore randomized indivisible allocations (equivalent to fractional allocation) and their implications on fairness: the compatibility of Ex-ante and Ex-post fairness.

Activities: Study the paper [*Best of Both Worlds: Ex-Ante and Ex-Post Fairness in Resource Allocation*](https://arxiv.org/abs/2005.14122).

> What does *Both worlds* here means?
>
> The indivisible allocation is randomized.
>
> - Before the allocation, each agent has a expected valuation for the bundle. Ex-ante WORLD I
>
> - After the allocation, each agent has its actual valuation for the bundle. Ex-post WORLD II

~~Iannis told me to ignore anything related to GROUP FAIRNESS.~~

Indivisible goods, additive valuations.

When **RANDOMIZATION** is allowed, it is possible to achieve envy-freeness.

When allocation is **deterministic**, achieving exact fairness is impossible but approximate notions such as EF1 can be guaranteed.

Goal: Achieve both simultaneously by randomized allocation that is fair ex-ante and approximately fair ex-post.

Algorithm that achieves **ex-ante EF** with **ex-post EF1**.

ex-ante EF + ex-post EF1 + economic efficiency? impossible. :x:

ex-ante EF + relaxed ex-post fairness + economic efficiency? feasible. :heavy_check_mark:

# Introduction

If we allowed to randomize, then we can choose an agent uniformly at random and allocate the entire set of goods. This is trivially **ex-ante envy-free** since all agents receive the same distribution.

> ex-ante 可以理解为预期的，先验的
>
> ex-post 可以理解为实施确定分配后的，后验的

However, this allocation induces a large amount of envy **ex-post** because there are times when one agent receives everything while others receive nothing.

Some ex-post envy is unavoidable: 2 agents, 1 item.

EF1 always exists, which limits the ex-post envy, but still be unfair if some agents are systematically favored over others.

Question: Can we randomize over EF1 allocations s.t. the resulting randomized allocation is EF? aka ex-ante EF + ex-post EF1.

> 本论文提出的期望无嫉妒和后验嫉妒特一物分配，即
>
> - 分配前：所有智能体获得的价值的期望是 EF 的
>
> - 分配后：所有智能体真实获得价值是 EF1 的

This paper: Yes.

## Basic Results

| en-ante\ex-post |      Prop1+EF~1~^1^       |            EF1            |   EF1+PO   |  EF1+fPO   |
| :-------------: | :-----------------------: | :-----------------------: | :--------: | :--------: |
|      Prop       |    :heavy_check_mark:     |    :heavy_check_mark:     | :question: | :x: Thm. 3 |
|       EF        |    :heavy_check_mark:     | :heavy_check_mark: Thm. 2 | :question: |    :x:     |
|       GF        | :heavy_check_mark: Cor. 1 |        :x: Thm. 3         |    :x:     |    :x:     |

Algorithm: **Recursive Probabilistic Serial**, which produces an ex-ante EF distribution over deterministic allocation that each satisfy EF1.

Impossibility of ex-ante EF + ex-post EF1 + ex-ante PO.

But with relaxation of ex-post fairness, awe are able to achieve **ex-ante group fairness** (GF) (aka EF + PO), in conjunction with Prop1 or EF~1~^1^ (**envy-freeness up to one good more-and-less**)

> $EF1\Rarr EF_1^1,EF1\Rarr PROP1$.

## Related Work

~~Omitted~~

# Preliminaries

Agent $N=[n]$, Indivisible item $M=[m]$.

**Fractional and Randomized Allocations**

Fractional allocation: for every item $j$, we have $\sum_{i\in N}A_{i,j}\le1$.

Complete allocation: a *fractional allocation* $A$ satisfying $\forall j\in M,\sum_{i\in N}A_{i,j}=1$. Otherwise call it *partial allocation*.

Integral allocation: a fractional allocation if $A_{i,j}\in\{0,1\}\forall i\in N,j\in M$.

Let $\mathcal X$ be all fractional allocations w.r.t. $N,M$.

**Randomized allocation**

$\mathbf X$, a lottery over integral allocations.

$\mathbf X$ is specified by a set of $\ell\in\mathbb N$ ordered pairs $\{(p^k,A^k)\}_{k\in[\ell]}$.

$A^k$ is an integral allocation implemented with probability $p^k\in[0,1]$.

The **support** of $\mathbf X$ is the set of integral allocations.

**Preferences**

$g_j\succcurlyeq_i g_k\Lrarr v_{i,j}\ge v_{i,k}$

$g_j\succ_i g_k\Lrarr v_{i,j}\gt v_{i,k}$

The utility of agent $i$ under $X\in\mathcal X$ is $v_i(X_i)=\sum_{j\in M}X_{i,j}\cdot v_{i,j}$.

**Allocation rule**

$f:\mathcal I\rarr2^\mathcal X$

**Fractional Maximum Nash Welfare Rule**. Given an instance $I\in\mathcal I$, the fractional MNW returns all fractional allocations that maximize the product of agents' utilities, i.e., $MNW(I)=\arg\max_{X\in\mathcal X}\prod_{i\in N}v_i(X_i)$. We refer to an allocation $A\in MNW(I)$ as a fractional MNW allocation.

Integral MNW allocation maximizes the product of agents' utilities across all integral allocations.

## Fairness and Efficiency Properties

PROP: ~~Omitted~~

EF: ~~Omitted~~

SD-EF: An allocation is SD-EF if for every pair of agents $i,h\in N$, we have $X_i\succcurlyeq_i^{SD}X_h$

:heavy_exclamation_mark:$X_i\succcurlyeq_i^{SD}Y_i$ means agent $i$ is **SD-prefer**s $X_i$ to $Y_i$, if for every good $g\in M$, we have
$$
\sum_{g_j\in\{g'\in M:g'\succcurlyeq_ig\}}X_{i,j}\ge\sum_{g_j\in\{g'\in M:g'\succcurlyeq_ig\}}Y_{i,j}
$$
> For example: $3$ agents, $4$ goods.
> $$
> agent_1:g_1\succcurlyeq g_2\succcurlyeq g_3\succcurlyeq g_4\\
> agent_2:g_2\succcurlyeq g_3\succcurlyeq g_4\succcurlyeq g_1\\
> agent_3:g_3\succcurlyeq g_4\succcurlyeq g_1\succcurlyeq g_2\\
> \text{allocation }X:
> \begin{array}{cc}
> X&g_1&g_2&g_3&g_4\\
> 1&20\%&30\%&20\%&10\%\\
> 2&30\%&50\%&30\%&40\%\\
> 3&40\%&20\%&50\%&50\%\\
> \end{array}
> \\
> \text{allocation }Y:\begin{array}{cc}
> Y&g_1&g_2&g_3&g_4\\
> 1&100\%&0&0&0\\
> 2&0&100\%&0&0\\
> 3&0&0&100\%&100\%\\
> \end{array}
> $$
> Let's take agent $1$ as example:
>
> - for good $g_1$: agent $1$ gets $100\%$ in $Y$, $20\%$ in $X$. $i$ SD-prefers $Y_i$ to $X_i$ property holds w.r.t. $g_1$ ($100\%\ge0$)
>
> - for good $g_2$: agent $1$ gets $100\%$ in $Y$, $20\%+30\%=50\%$ in $X$. $i$ SD-prefers $Y_i$ to $X_i$ property holds w.r.t. $g_2$ ($100\%\ge50\%$)
>
> - for good $g_3$: agent $1$ gets $100\%$ in $Y$, $20\%+30\%+20\%=70\%$ in $X$. $i$ SD-prefers $Y_i$ to $X_i$ property holds w.r.t. $g_3$ ($100\%\ge70\%$)
>
> - for good $g_4$: agent $1$ gets $100%$ in $Y$, $(20+30+20+10)\%=80\%$ in $X$. $i$ SD-prefers $Y_i$ to $X_i$ property holds w.r.t. $g_4$.
>
>   THUS, IN THIS CASE, AGENT $i$ SD-PREFERS $Y_i$ TO $X_i$.

SD abbr. for first-order *Stochastic Dominance*.

$EF\Rarr SD-EF$

fPO and PO: ~~Omitted~~

:thinking:**Proposition 1**. If a randomized allocation is ex-ante PO, then it is also ex-post fPO.

>If a randomized allocation $\mathbf X=\{(p^k,A^k)\}_{k\in[\ell]}$ implementing a fractional allocation $X$ is not ex-post fPO, then for some $k\in[\ell]$, integral allocation $A^k$ must be Pareto dominated by a fractional allocation, say $Y$. Then the fractional allocation $X'=p^k\cdot Y+\sum_{r\neq k}p^r\cdot A^r$ Pareto-dominates $X$, which implies that $\mathbf X$ is not ex-ante PO.

**Group Fairness (GF)**. An allocation $X$ is **group fair** if for all non-empty subsets of agents $S,T\subseteq N$, there is no fractional allocation $Y$ of $\cup_{i\in T}X_i$ to the agents in $S$ s.t. $\frac{|S|}{|T|}\cdot v_i(Y_i)\ge v_i(X_i)$ for all $i\in S$ and at least one inequality is strict.

:heavy_exclamation_mark:By imposing constraints on $(S,T)$ pair can recover properties such as PROP $(|S|=1,T=N)$, EF $(|S|=|T|=1)$ and fPO $(S=T=N)$.

PROP1, EF1: ~~Omitted~~

**SD-Envy-Freeness up to One Good (SD-EF1)**. An integral allocation is SD-EF1 if for every pair of agents $i,h\in N$ s.t. $A_h\neq\empty$, we have $A_i\succcurlyeq_i^{SD}A_h\diagdown\{j\}$ for some good $j\in A_h$.

(This is equivalent to agent $i$ not envying agent $h$ up to one good under every additive valuation consistent with the ordinal preference relation $\succcurlyeq_i$)

**Envy-Freeness up to One Good More-and-Less (EF~1~^1^)**. An integral allocation $A$ is EF~1~^1^ if for every pair of agents $i,h\in N$ s.t. $A_h\neq\empty$, we have $v_i(A_i\cup\{j_i\})\ge v_i(A_h\diagdown\{j_h\})$ for some goods $j_i\not\in A_i$ and $j_h\in A_h$.

Relations:
$$
SD-EF1\Rarr EF1\Rarr\begin{cases}
EF_1^1\\
PROP1
\end{cases}
$$

# Possibility: Ex-ante EF + Ex-post EF1

Ex-ante EF + Ex-post EF1 can be achieved simultaneously.

Based on round-robin, with manipulation of agent order. Uniformly randomizing  the agent ordering also achieves ex-ante proportionality.

:thinking:**Proposition 2**. With additive valuations, randomized round-robin is ex-ante PROP and ex-post EF1.

> Obviously, randomized round-robin is ex-post EF1. For ex-ante PROP, fix an agent $i$, and suppose, without loss of generality, that $v_{i,1}\ge v_{i,2}\ge\cdots\ge v_{i,m}$. Suppose $m=cn$ for some positive integer $c$.
>
> When agent $i$ is $k$-th in the ordering, her value for her allocation is at least $v_{i,k}+v_{i,k+n}+\cdots+v_{i,k+(c-1)n}=\sum_{c'=1}^cv_{i,(c'-1)n+k}$ because the $c'$-th good she chooses must be no less preferred than her $(c'-1)n+k$-th most preferred good.
>
> Since she appears in every position in the ordering with probability $1/n$, her expected value is at least $\frac{1}{n}\sum_{k=1}^m\sum_{c'=1}^cv_{i,(c'-1)n+k}=\frac{1}{n}\sum_{j=1}^mg_{i,j}$, which matches her proportionality guarantee.

However, just by randomizing agent order, sometimes we fail to achieve ex-ante EF. ~~Example omitted.~~



Noted that there are $n!$ possible agent orders. For $i$, some orders may better than the others.

Instead of starting from a method that guarantees ex-post EF1 and using it to achieve ex-ante EF, we do the opposite: Start from a fractional EF allocation and implement it using integral EF1 allocations.

**Probabilistic serial** is an algorithm that produces a fractional EF allocations.

- *serial eating* protocol wherein all agents simultaneously start eating their respective favorite goods at the same constant speed.
- Once a good is completely consumed by a subset of agents, each of those agents proceeds to eating her favorite available good at the same speed.
- The algorithm terminates when all goods have been eaten, and fraction of each good consumed by an agent is allocated to her.

~~A very vivid algorithm~~

A useful property of this algorithm: it only uses the ordinal preferences of agents over goods, and computes an allocation that is ex-ante envy-free for any additive utilities consistent with the ordinal preferences. Thus, it's SD-EF.

Use a variant of probabilistic serial, *recursive probabilistic serial*, which provides the desired fairness guarantees, i.e., ex-ante EF and ex-post EF1.

## Recursive Probabilistic Serial

Given as input a subset of goods $M'\subseteq M$, the algorithm allows each agent to consume its favorite available good in $M'$ out of those that have not yet been fully consumed at a rate of one good per unit time.

Let $X^t=eating(M',t)$ denote the partial allocation obtained when the algorithm is run for $t$ unit of time. Note that $eating(M,\lceil\frac{m}{n}\rceil)$ is equivalent to the outcome of the probabilistic serial algorithm.

:dart::thinking:**Theorem 1**. (Brikhoff-von Neumann) Let $X$ be a real-valued $n\times m$ matrix s.t.

1. $X_{ij}\in[0,1]\forall i\in[n]\and j\in[m]$
2. $\sum_{j=1}^mX_{ij}\le1\forall i\in[n]$
3. $\sum_{i=1}^nX_{ij}\le1\forall j\in[m]$

Then, in strongly polynomial time, one can compute integral matrices $A^1,A^2,\cdots,A^q$ and weights $w^1,w^2,\cdots,w^q\in[0,1]$ s.t.

1. $\sum_{k=1}^qw^k=1$ and $X=\sum_{k=1}^qw^kA^k$
2. For each integral matrix $A^k$, we have $A^k_{ij}\in\{0,1\}\forall i\in[n]\and j\in[m]$
3. For every $k\in[q]$, we have
   - $\lfloor\sum_{j=1}^mX_{ij}\rfloor\le\sum_{j=1}^mA^k_{ij}\le\lceil\sum_{j=1}^mX_{ij}\rceil$
   - $\lfloor\sum_{i=1}^nX_{ij}\rfloor\le\sum_{i=1}^nA^k_{ij}\le\lceil\sum_{i=1}^nX_{ij}\rceil$

The algorithm proceeds in stages. In each stages, the `EATING` procedure is run for **one unit of time**, after which every agent has consumed a total mass of one item.

Then, by theorem 1, this fractional partial allocation can be decomposed into integral allocations. By 3. of theorem 1, each agent receives exactly one item in each integral allocation in the support.

We interpret the weights $w^1,\cdots,w^q$ as probabilities, and sample an integral allocation from this distribution before proceeding to the next stage.

The algorithm then fixes the assignments of the goods to the agents in accordance with the aforementioned integral partial allocation, and repeats the eating procedure with the reduced set of goods.

In the final stage of the algorithm, fewer than $n$ goods might remain, in which case some agents do not receive any good under the integral allocation. The full procedure is describe in pseudo code.

### Pseudo-code of Algorithm 1

Ex-ante EF + ex-post EF1

1. $B\gets(\empty,\cdots,\empty)$
2. $M^0\gets M$
3. for $t=1$ to $\lceil\frac{m}{n}\rceil$ do
   - $X^t\gets$ `eating`$(M^t,1)$ // eating protocol for one unit of time
   - BvN: $X^t=\sum_{k=1}^\ell w^{t,k}A^{t,k}$ // decomposition from theorem 1
   - $B^t\gets A^{t,k}$ with probability $w^{t,k}$ // sampling step
   - $M^{t+1}\gets M^t\diagdown\bigcup_{i\in N}B_i^t$ // fix the assignments according to the sampled allocation
   - $B_i\gets B_i\bigcup_{t=1}^{\lceil\frac{m}{n}\rceil}B_i^t$ for all $i\in N$ // update integral allocation
4. return $B$

**Every integral allocation returned by Algorithm 1 satisfies EF1.**

:thinking:**Lemma 1**. Let $t\lt\lceil\frac{m}{n}\rceil$. Let $g_{i,t}$ denote the good allocated to agent $i$ in iteration $t$ of Algorithm 1. Then agent $i$ weakly prefers $g_{i,t}$ to all goods in $M^{t+1}$ (goods unallocated after $t$ iterations of the algorithm).

> Proof by contradiction.
>
> If there exists a good $g^*\in M^{t+1}$ s.t. $v_i(g^*)\gt v_i(g_{i,t})$, then by theorem 1, agent $i$ must have eaten a non-zero share of good $g_{i,t}$ in iteration $t$. On the other hand, $g^*\in M^{t+1}$ implies that $g^*\in M^t$, and thus $g^*$ must not be fully consumed in iteration $t$. However, since $g_i(g^*)\gt v_i(g)$, agent $i$ should have then consumed $g^*$ before consuming $g_{i,t}$ in iteration $t$, which is a contradiction.

:thinking:**Lemma 2**. Every integral allocation returned by Algorithm 1 satisfies EF1.

> $\forall i\in[n]$, let $g_{i,t}$ denote the good received by agent $i$ in iteration $t$. Fix a pair of agents $i,k\in N$. By lemma 1, for every $t\lt\lceil\frac{m}{n}\rceil$, we have $v_i(g_{i,t})\ge v_i(g_{k,t+1})$ because $g_{k,t+1}\in M^{t+1}$. Hence,
> $$
> v_i(A_i)\ge\sum_{i=1}^{\lceil\frac{m}{n}\rceil-1}v_i(g_{i,t})\ge\sum_{i=2}^{\lceil\frac{m}{n}\rceil}v_i(g_{i,t})=v_i(A_k\diagdown\{g_{k,1}\})
> $$
> which establishes EF1.

:dart::thinking:**Theorem 2**. The randomized allocation implemented by algorithm 1 is ex-ante EF + ex-post EF1.

> By lemma, it is indeed ex-post EF1.
>
> Notice that each agent eats her preferred available good at each point in time during the `eating` algorithm. Therefore, for any iteration $t$, the partial fractional allocation $X^t$ computed by the `eating` procedure in iteration $t$ is EF.
>
> The rest is induction.

Since Recursive Probabilistic Serial takes only the ordinal references of agents over goods as input, thus it is in fact **ex-ante SD-EF + ex-post SD-EF1**.

## A Polynomial-Time Algorithm for Ex-ante EF + Ex-post EF1

Full randomized allocation constructed by Algorithm 1 require exponential time.

Approach to reducing the support size is to use the Carathéodory’s theorem from convex analysis: If a point $x\in\mathbb R^d$ lies in the convex hull of a set $P$, then $x$ can be expressed as a convex combination of at most $d+1$ vertices of $P$.

Any fractional allocation $X$ is a point in an $n\times m$-dimensional space. By Carathéodory’s theorem, if it is a convex combination of integral allocations in the support $\{A^1,\cdots,A^\ell\}$, then it can also be written as a convex combination of at most $nm+1$ allocations from this set.

This establishes the existence of a convex combination with small support, it does not automatically provide an efficient algorithm for converting a convex combination with large support to one with small support.

In the modification of recursive probabilistic serial, we ensure that the support size remains $mn+1$ after every step of "branching out".

Every step of branching out can increase the support size of $mn+1$ to $(nm+1)\cdot p(m,n)$. Thus we need a way to reduce the support size from at most $(mn+1)\cdot p(m,n)$ to at most $nm+1$.

:thinking:**Proposition 3**. There is a polynomial-time algorithm, that given a polytope $\mathcal P$ with a strong separation oracle and a point $y\in\mathcal P$, returns a representation of $y$ as a convex combination of $\dim(\mathcal P)+1$ vertices of $\mathcal P$.

 Notice that at the end of the first iteration $t=1$, the fractional partial allocation $X^1$ consists of a polynomial number of integral allocations in its support, i.e., $X^1=\sum_{k=1}^\ell w^{1,k}A^{1,k}$, where $\ell=p(m,n)$ for some polynomial $p$. Consider the polytope $\mathcal P^1$ formed by the convex hull of the integral allocations $A^{1,1},\cdots,A^{1,\ell}$ as follows
$$
\mathcal P^1=\{z:z=\sum_k\alpha_kA^{1,k}\text{ where }(\alpha_k\ge0\forall k\in[\ell])\and(\sum_k\alpha_k=1)\}
$$

> polytope 多胞体
>
> 1. **Convexity:** A polytope is a convex set, meaning that for any two points inside the polytope, the line segment connecting them lies entirely within the polytope.
> 2. **Finite Set of Vertices:** A polytope is defined by a finite set of vertices (or corners) in Euclidean space. These vertices are the extreme points of the polytope and determine its shape.
> 3. **Higher-Dimensional Analogues:** Just as a polygon is a two-dimensional polytope and a polyhedron is a three-dimensional polytope, polytopes can exist in any finite number of dimensions. For example, a four-dimensional polytope is called a polytope, and a five-dimensional polytope is called a polychoron.
> 4. **Faces:** The faces of a polytope are the lower-dimensional subsets of the polytope that are themselves polytopes. For example, in a three-dimensional polyhedron, the faces are the polygons that form its surface.
> 5. **Simplexes and Polygons:** Simplexes are special types of polytopes that are defined as the convex hull of a set of affinely independent points. A simplex in two dimensions is a triangle, and in three dimensions, it is a tetrahedron. Polygons are also considered special cases of polytopes.

Observe that $\mathcal P^1$ admits a polynomial-time strong separation oracle. One can decide in polynomial time whether a given point $y\in\mathbb R^{n\times m}$ belongs to $\mathcal P^1$, or return a separating hyper-plane if it doesn't. This implies that an $(nm+1)$-sized decomposition of $X^1$, as guaranteed by Carathéodory’s theorem can be computed in polynomial time.

Having computed an $(nm+1)$-sized decomposition of $X^1$, we obtain at most $nm+1$ sub-instances in each of which we re-run algorithm. Notice that this result in a  probability distribution over $(nm+1)\cdot p(n,m)$ deterministic allocations at the end of the second iteration. Re-invoke algorithmic version of Carathéodory’s theorem to obtain a decomposition over at most $mn+1$ of these integral allocations in polynomial time.

Produced randomized allocation has support that is a subset of the support of the randomized allocation produced by recursive probabilistic serial. Hence, ex-post EF1 of this modified procedure follows immediately from lemma 1.

Like algorithm 1, this algorithm produces the same **ex-ante SD-EF + ex-post SD-EF1**.

# Impossibility: ex-ante PROP + ex-post EF1 + ex-post fPO

The previous lacks efficiency.

ex-ante PO $\Rarr$ ex-post fPO $\Rarr$ ex-post PO

ex-ante EF + ex-post PO + ex-post EF1? Not solve...

How about stronger one, ex-post fPO? The answer is negative for ex-ante PROP + ex-post EF1 + ex-post fPO.

:dart::thinking:**Theorem 3**. There exists an instance with additive valuations in which no randomized allocation is simultaneously ex-ante PROP + ex-post EF1 + ex-post fPO.

> It suffices to give an instance with unique EF1 + fPO allocation which violates PROP.
>
> 2 goods $g_1,g_2$, 2 agent $a_1,a_2$ with valuations
>
> |       | $g_1$ | $g_2$ |
> | :---: | :---: | :---: |
> | $a_1$ |   1   |   2   |
> | $a_2$ |   1   |   3   |
>
> This instance has exactlly 2 integral EF1 allocations: $A=(\{g_1\},\{g_2\})$ and $B=(\{g_2\},\{g_1\})$. It's easy to check that $B$ is not fPO, since it is Pareto dominated by a fractional allocation $X$ that assign $g_1$ to $a_1$ and splits $g_2$ equally between 2 agents.
>
> $A$ is fPO, maximizing the utilitarian social welfare.
>
> Observe that $A$ violates PROP since $v_1(A_1)=v_1(g_1)\lt\frac{1}{2}v_1(g_1\cup g_2)$.

Additionally, ex-ante EF and ex-ante GF imply ex-ante PROP, thus these are also incompatible with ex-post EF1 + PO.

Since ex-ante PO implies ex-post fPO, thus ex-ante (PROP + PO) and ex-ante (EF + PO) are incompatible with ex-post EF1.

# Possibility: ex-ante GF + ex-post PROP1 + ex-post EF~1~^1^

~~Omitted~~

# Characterizing MNW

~~Seems related to GF, can be omitted.~~

# The Case of Bads

"Bads" here means chores. aka all items are negative valued.

Let $M$ be set of $m$ bads. Fractional, integral and randomized allocations are defined analogously as in the case of goods.

However the agents have **non-positive** valuation functions. There are differences when defining fairness notions.

**PROP1**, aka proportionality up to one bad. An **integral** allocation $A$ is PROP1 if for every agent $i$, either $v_i(A_i)\ge v_i(M)/n$ or there exists a bad $j\in A_i$ s.t. $v_i(A_i\diagdown\{j\})\ge v_i(M)/n$, where $v_i(M)$ is $i$'s valuation for all bads.

**EFk**, aka envy-freeness up to $k$ bads. An **integral** allocation $A$ is EFk if for every pair of agent $i,h$, there $\exist S_i\subseteq A_i$ with $|S_i|\le k$ s.t. $v_i(A_i\diagdown S_i)\ge v_i(A_h)$.

**EF~1~^1^**, aka envy-freeness up to one bad more-and-less. An integral allocation $A$ is EF~1~^1^ if for every pair of agent $i,h$ s.t. $A_i\neq\empty$, we have $v_i(A_i\diagdown\{j_i\})\ge v_i(A_h\cup\{j_h\})$ for some bads $j_i\in A_i$ and $j_h\not\in A_h$.

## Ex-ante EF + Ex-post EF1 for Bads

Recursive Probabilistic Serial and its polynomial-time variant only use ordinal preferences of agents over goods.

Problem here: Can we construct an ordinal preference over bads in which the bads are sorted in a non-increasing order of the agent's valuation for them.

Both RPS and its polynomial-time variant are always ex-post EF2 for bads.

:thinking:**Proposition 6**. For additive valuation functions over bads, recursive probabilistic serial and its polynomial variant produce randomized allocation that are ex-ante EF and ex-post EF2.

> `eating` procedure is EF because agents are forced to eat bads at the same rate and an agent is always eating a bad that she prefers the most among all bads not fully consumed.
>
> Since each $X^t$ is EF, a probability distribution over $X^t$ in different branches is also envy-free. Hence, the expected fractional allocation induced in step $t$ is EF for each $t$. Hence, ex-ante EF.
>
> For ex-post EF2, let $A$ be an integral allocation produced by RPS. By lemma 1 still holds for bads.
>
> For $t\lt\lceil m/n\rceil$, if $b_{i,t}$ denotes the bad allocated to agent $i$ in iteration $t$, then agent $i$ prefers $b_{i,t}$ to all bads in $M^{t+1}$.
>
> When $t\lt\lfloor m/n \rfloor$, we know that another agent $h$ must receive a bad in round $t+1\le\lfloor m/n \rfloor$. Hence, for all agents $i,h\in N$ and all $t\lt\lfloor m/n\rfloor$, we have $v_i(b_{i,t})\ge v_i(b_{h,t+1})$.
>
> Summing over $t\lt\lfloor m/n\rfloor$, we get
> $$
> v_i(\lbrace b_{i,t}:1\le t\lt\lfloor m/n\rfloor\rbrace)\ge v_i(\lbrace b_{h,t}:2\le t\le\lfloor m/n\rfloor\rbrace)\ge v_i(A_h)
> $$
> In the LHS, there are at most 2 bads missing from $A_i$:
>
> - The bad allocated to agent $i$ in round $\lfloor m/n\rfloor$
> - Tge bad which may be allocated tot agent $i$ in the final round $\lceil m/n\rceil$
>
> Q.E.D.

Naturally, the algorithm also produces ex-ante SD-EF + ex-post SD-EF2.

When $m$ is a multiple of $n$, it turns out that the allocation is in fact ex-post EF1.

:thinking:**Lemma 5**. For additive valuation functions over bads, recursive probabilistic serial and its polynomial-time variant from Section 3.2 produce randomized allocations that are ex-ante envy-free (EF) and ex-post envy-free up to one bad (EF1) whenever the number of bads is divisible by the number of agents.

> Proof by proposition 6.
>
> If $n$ divides $m$, we guarantee that there is only one bad missing from the LHS, which is that allocated to agent $i$ in round $m/n$.

Simple modification of RPS, simply add a number of "dummy bads" which all agent value at $0$ s.t. the total number of bads becomes divisible by the number of $n$, then run RPS to compute ex-ante EF + ex-post EF1.

:dart::thinking:**Theorem 6**. For additive valuation over bads, there always exists a randomized allocation that is ex-ante EF + ex-post EF1 which can be computed in polynomial time.

## What about fair division of mixed manna?

Each item can be either a good or a bad for each agent.

EF1 can be generalized to the mixed manna setting. It requires that if $i$ envies $h$, then the envy can be eliminated by either removing one item from agent $h$'s bundle (which must be a good for $i$), or one item from agent $i$'s bundle (which must be a bad for $i$).

**EF1** aka envy-freeness up to one item. An integral allocation $A$ is EF1 if for every pair of agent $i,h$, either $i$ does not envy $h$, or there exists an item $o\in A_i\cup A_h$ s.t. $v_i(A_i\diagdown\{o\})\ge v_i(A_h\diagdown\{o\})$.

The algorithm of this paper achieve the relaxation of EF1 in mixed manna setting, w-EF1.

**w-EF1** aka weak envy-freeness up to one item. An integral allocation $A$ is w-EF1 if for every pair $i,h$, either $i$ does not envy $h$, or there exists a good $o_h\in A_h$ and/or a bad $o_i\in A_i$ s.t. $v_i(A_i\diagdown\{o_i\})\ge v_i(A_h\diagdown\{o_h\})$.

:dart::thinking:**Theorem 7**. For additive valuation in the mixed manna setting, there always exists a randomized algorithm producing ex-ante EF + ex-post w-EF1, which can be computed in polynomial time.

> Add dummy items to guarantee that $m$ is a integer multiple of $n$, and remove them at the end.
>
> When we run RPS on such instance, we can again apply the same argument as in the proof in proposition 6. That is equivalent to ex-post w-EF1 guarantee.

Of course, this results is also ex-ante SD-EF and ex-post SD-w-EF1 for mixed manna.

## Ex-ante GF + Ex-post PROP1 + Ex-post EF~1~^1^ for Bads

~~Omitted~~

# Discussion

:question:Open question:

- ex-ante PROP + ex-post (EF1 + PO)
- ex-ante EF + ex-post (EF1 + PO)

Difficulty: hard to analyze the methods of finding EF1 + PO allocations.

:question:Open question:

- Does there always exist an integral EF1 + PO allocation in which agent $i$ doesn't envy agent $j$?
- What about an integral EF1 + PO allocation in which agent $i$ does not envy any other agent?
- Given a priority ordering over the agents, does there always exist an EF1 + PO allocation in which no agent envies an agent with lower priority?

Other possible work direction: EFX/MMS for ex-post fairness?

How about ex-ante and ex-post fairness guarantees in voting, matching and public decision-making?