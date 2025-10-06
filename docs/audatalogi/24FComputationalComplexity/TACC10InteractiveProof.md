---
title: 算法和计算复杂度 10 Interactive Proofs
author: tinsir888
date: 2024/10/19
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 47c9111e
---

# Interactive Turing Machine

:book:Definition 44 (interactive Turing machine). An interactive TM $M$ is a probabilistic TM equipped also with a *write-only* query tape and a *read-only* answer tape, and has two new special states, a **query state** and a **continue state**. The symbols allowed on the query and answer tape are given by a *communication alphabet* $\Lambda$.

When $M$ enters the query state, the contents $m$ of the query tape as a **message** $m$ sent by $M$. We say that we send a reply $r$ to $m$ by performing the following steps: The query tape is reinitialized to a blank tape, the string $r$ is placed on the answer tape, and computation of $M$ is resumed in the continue state.

Given two interactive TM, consider running both TM on the same input $x$ and relaying messages back and forth. In this way we define **interactive proof systems** having one TM be the **verifier** $V$, assumed to be Polynomial-time bounded, and the other TM to be the **prover** $P$, just assume that $P$ will always produce a reply to any message received.

$P:\Lambda^*\to\Lambda^*$

Given also a polynomial time interactive TM $V$, denote by $(V,P)(x)$ the computation of $V$ on input $x$ where the replies to messages of $V$ are determined as follows.

Suppose that $V$ enters query states and $m_1,\cdots,m_i$ is the list of all messages sent by $V$. The reply given to the message $m_i$ is $P(\langle x,m_1,\cdots,m_i\rangle)$.

:book:Definition 45 (interactive proof system). Let $v$ be a polynomial time interactive Turing machine $V$, $P:\Lambda^*\to\Lambda^*$, and let $0\le s\lt c\le1$. We say that the pair $(V,P)$ is an interactive proof system for $L$ with completeness $c$ and soundness error $s$ if:
$$
x\in L\Rarr\Pr[(V,P)(x)=yes]\ge c\\
\and\\
x\not\in L\Rarr\Pr[(V,\hat P)(x)=yes]\le s\forall \hat P
$$
Define $IP$ as the class of all languages that has an interactive proof system with completeness $3/4$ and soundness error $1/4$. We say that $V$ is the verifier and $P$ the prover of the interactive proof system $(V,P)$.



We can view $NP$ as a proof system without randomness or interaction other than a single message.

:thinking:Proposition 14. $NP\subseteq IP$.

An example of a non-trivial interactive proof system for checking that two graph are not isomorphic ($GNI$).

~~GNI 问题：判断两图是否不是同构的。~~

Given a graph $G=(V,E)$ and a permutation $\pi:V\to V$. Denote by $\pi(G)$ the graph with vertex set $V$ and edge set $\{(\pi(u),\pi(v))\vert(u,v)\in E\}$.

We say that graphs $G_1(V,E_1),G_2(V,E_2)$ with the same set of vertices are isomorphic, denoted $G_1\cong G_2$, if there exist a permutation $\pi$ of $V$ s.t. $\pi(G_1)=G_2$.

Problem of $GNI$:

- Instance: Graphs $G_1(V,E_1),G_2(V,E_2)$ on the same set of vertices $V$
- Question: Is $\pi(G_1)\neq G_2$ for all permutations $\pi$ of $V$?

Clearly, $GNI\in coNP$. It is not known whether $GNI\in NP$.

A very interesting property of this proof system is that it is a so-called zero-knowledge proof.

~~零知识证明，扯到密码学上去~~

:dart:Theorem 48. $GNI\in IP$.

> GMW Protocol for $GNI$:
>
> - Input: $G_1=(V,E_1),G_2=(V,E_2)$.
> - Goal: Prove $G_1\not\cong G_2$.
>
> $V$: Pick $i\in\{1,2\}$ and permutation $\pi$ of $V$ uniformly at random. Send $H=\pi(G_i)$ to $P$.
>
> $P$: Pick $j\in\{1,2\}$ s.t. $H\cong G_j$. Send $j$ to $V$.
>
> $V$: Accept if $i=j$. Otherwise reject.
>
> 这个 GMW 协议是什么意思：验证器随机选一个图并将边的编号打乱（记为 $H$），发送给证明器。证明器从两个原图中随便选一个和 $H$ 同构的图，将其编号发回验证器。验证器验证同构图编号是否与 $H$ 对应原图的编号相等。如果不相等的话，那就认为给定的俩图不同构。
>
> Suppose first $G_1\not\cong G_2$. In this case $P$ has a unique choice of $j$ s.t. $H\not\cong G_j$, which means that $i=j$ making $V$ accept with probability $1$. Suppose next that $G_1\cong V_2$. Then the distribution of $\pi(H)$ is independent of $i$ since the composition of a fixed permutation with a uniformly random permutation is again a uniformly random permutation.
>
> Now since $\hat P$ chooses $j$ based only on $H$ we have $\Pr[i=j]=1/2$. It follows that the completeness of the protocol is $1$ and the soundness error of the protocol is $1/2$. The soundness error may be reduced to $2^{-k}$ by iterating protocol $k$ times.

It is not known whether $GNI\in NP$.

:thinking:Proposition 15. $IP\in PSPACE$.

> Hard proof.

# The Sumcheck Protocol

How to utilize the power of interactive proofs? **Converting Boolean formulas to arithmetic formulas** and exploit that polynomials can be evaluated on inputs outside the Boolean cube.

**Arithmetization**: The process of converting a Boolean formula into an arithmetic formula.

By arithmetization, we can prove that $P^{\#P}\subseteq IP$, where $\#P$ is a class capturing the complexity of **counting the number of satisfying assignments of a Boolean formula**.

A weaker result: $coNP\subseteq IP$.

The method can be generalized to show $PSPACE\subseteq IP$.

:book:Definition 46 (Arithmetization). Let $\varphi$ be a $3CNF$ formula with $m$ classes $C_1,\cdots,C_m$ and $n$ variables $x_1,\cdots,x_n$. We define the arithmetic formula $A_\varphi$, aka arithmetization of $\varphi$, recursively. For a variable $x_i$ we let $A(x_i)=x_i$, for a negated variable $\neg x_i$ we let $A(\neg x_i)=1-x_i$. For a clause $C_j=(\ell_{j,1}\or\ell_{j,2}\or\ell_{j,3})$ we let
$$
A(C_j)=1-A(\neg C_j)=1-A(\neg \ell_{j,1})A(\neg \ell_{j,2})A(\neg \ell_{j,3})\\
=1-(1-A(\ell_{j,1}))(1-A(\ell_{j,2}))(1-A(\ell_{j,3}))
$$
Finally, we define $A_\varphi=\prod_{j=1}^mA(C_j)$.

Expanding $A(\varphi)$ into a sum of monomials would take exponential time.

The number of satisfying assignments $\#\varphi$ of $\varphi$:
$$
\#\varphi=\sum_{x_1=0}^1\sum_{x_2=0}^1\cdots\sum_{x_n=0}^1A_\varphi(x_1,\cdots,x_n)
$$
Then we define an **interactive proof** for proving $\#\varphi=K$, for given $K$. For $K=0$ allow us prove that $coNP\subseteq IP$. The protocol is called **Sumcheck** protocol:

- Input: Arithmetic formula $F(x_1,\cdots,x_n)$ of total degree $d$, prime $p$, number $K$.
- Goal: Prove $\sum_{x_1=0}^1\sum_{x_2=0}^1\cdots\sum_{x_n=0}^1F(x_1,\cdots,x_n)\equiv K\mod p$.
- Verifier: If $n=0$, evaluate the arithmetic formula $F$, which only have constants as inputs. Accept it $F\equiv K\mod p$ and reject otherwise. If $N\ge1$, await message from $P$.
- Prover: Send the uni-variate degree $d$ polynomial $Q(x_1)$ to Verifier, where $Q(x_1)$ is given by $Q(x_1)=\sum_{x_2=0}^1\cdots\sum_{x_n=0}^1F(x_1,\cdots,x_n)$.
- Verifier: If $Q(0)+Q(1)\not\equiv K\mod p$, reject. Otherwise, pick $a_1\in\mathbb Z_p$ uniformly at random. Send $a_1$ to $P$ and recursively invoke the protocol for proving that $\sum_{x_2=0}^1\cdots\sum_{x_n=0}^1F(a_1,x_2,\cdots,x_n)\equiv Q(a_1)\mod p$.

:thinking:Proposition 16. Let $F(x_1,\cdots,x_n)$ be an arithmetic formula of total degree $d$, which is polynomial in $n$, let $p$ be a prime of bi-length polynomial in $n$, and let $K$ be an integer. The Sumcheck protocol is an interactive proof for the statement
$$
\sum_{x_1=0}^1\sum_{x_2=0}^1\cdots\sum_{x_n=0}^1F(x_1,\cdots,x_n)\equiv K\mod p
$$
with completeness $1$ and soundness error at most $nd/p$.

:thinking:Corollary 11. $coNP\subseteq IP$.

> Show that $\overline{3SAT}\in IP$.

# Interactive Proof for Polynomial Space

$PSPACE\subseteq IP$

Consider $PSPACE$-complete $TQBF$.

Consider whether $\psi=\exist x_1\in\{0,1\}\forall x_2\in\{0,1\}\cdots\exist x_n\in\{0,1\}\varphi(x_1,\cdots,x_n)$ is true.

WLOG, quantifiers are strictly alternating, $\varphi$ is a $3CNF$ formula with $n$ variables and $m$ clauses.

Note that $\psi$ is true iff
$$
\sum_{x_1}\prod_{x_2}\sum_{x_3}\cdots\sum_{x_n}A_\varphi(x_1,\cdots,x_n)\gt0.
$$
Unbinding outermost summation over $x_1$ is polynomial of **exponential** degree.

One solution to around the obstacle: limiting the scope of each variable using variable renaming.

Making use of **linearization** for the same purpose:

:book:Definition 47. Let $Q(x_1,\cdots,x_n)$ be a polynomial. By $\large\mathbf L_{x_i}$ binds the variable $x_i$, at the same time introducing a new variable $x_i$ (reusing the variable name $x_i$). Now replace the condition $\sum_{x_1}\prod_{x_2}\sum_{x_3}\cdots\sum_{x_n}A_\varphi(x_1,\cdots,x_n)\gt0$ by
$$
\sum_{x_1}\mathbf L_{x_1}\sum_{x_2}\mathbf L_{x_1}\mathbf L_{x_2}\cdots\sum_{x_n}\mathbf L_{x_1}\cdots\mathbf L_{x_n}A_\varphi(x_1,\cdots,x_n)\gt0.
$$
The extension of the Sumcheck protocol:

- The prover sends a prime $p$ and a number $K\not\equiv0\mod p$ s.t. the LHS is $K\text{ mod }p$.

  The LHS is a non-negative integer of magnitude at most $2^{2^n}$, hence is divisible by ay most $2^n$ distinct primes.

  By property of prime distribution function, the prime $p$ has bit-length $n+O(\log n)$.

- Each of the operators of the form $\sum_{x_i},\prod_{x_i},\mathbf L_{x_i}$ are unbound one by one, and $P$ sends the resulting uni-variate polynomial $Q(x_i)$.

- ......

:dart:Theorem 49. $IP=PSPACE$.

Two interesting observation:

1. The completeness of the extended Sumcheck protocol is $1$. Any interactive proof system may be converted into an equivalent interactive proof system with completeness $1$.
2. The message sent to the Prover by the Verifier may be viewed as just consisting of all random bits read since the last message sent.