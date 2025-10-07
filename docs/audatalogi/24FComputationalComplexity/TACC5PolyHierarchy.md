---
title: 算法和计算复杂度 5 The Polynomoial-Time Hierarchy
author: tinsir888
date: 2024/7/12
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 5513d430
---

Idea: ==arithmetic hierarchy== from recursion theory.

Halting problem $HALT$ is a recursive enumerable language that is undecidable.

The polynomial-time hierarchy considers polynomial time and $SAT$ instead of $HALT$.

# Oracle Turing Machines

An oracle Turing machine $M^?$ is equipped with an additional tape, the **query tape**, and has three new special states $q_{ask},q_{yes},q_{no}$. The query tape is a write-only tape.

The operation of $M^?$ is defined relative to an oracle language $A$. 

Denote $M^?$ together with oracle language $A$ by $M^A$.

Whenever $M^A$ enters the state $q_{ask}$:

- Let $z$ be the string of symbols written to the query tape. The query tape is reinitialized to a black tape.
- If $z\in A$, computation resumes in the state $q_{yes}$.
- If $z\not\in A$, computation resumes in the state $q_{no}$.

Define $time_{M^A}(x)$ and $space_{M^A}(x)$ for an oracle Turing machine with an oracle language $A$ to be the number of computation steps and number of cells of work tape used during computation on input $x$. The same way we define time-bounded and space-bounded oracle Turing machines.

Then we need to define oracle versions of complexity classes. Straightforward to define time-bounded complexity classes, but be careful to define space-bounded complexity classes.

$DTIME^A(T(n)),DSPACE^A(S(n)),NTIME^A(T(n))$.

:book:Problem for non-deterministic space-bounded oracle Turing machine: it may write very long strings to the oracle tape. 

Solution: Requiring the oracle Turing machine to behave deterministically in the time between writing the first symbol to the oracle tape and making the oracle query. (deterministic oracle access)

Thus we define $NSPACE^A(S(n))$ as the class of languages computed by a $O(S(n))$ space-bounded non-deterministic oracle Turing machine **with deterministic oracle access** to oracle $A$.



The definition can be extended to other complexity classes.

$P^A$ and $NP^A$ for any oracle language $A$.

The oracle may be chosen from some class of languages. Let $\mathcal C$ be a class of languages.
$$
DTIME^\mathcal C(T(n))=\bigcup_{A\in\mathcal C}DTIME^A(T(n))
$$
Similarly we have $DSPACE^\mathcal C(T(n))$, $NTIME^\mathcal C(T(n))$ and $NSPACE^\mathcal C(T(n))$.

This again extends to all other complexity classes such as $P^{NP}$.

## Turing Reductions

:book:Definition 17 (Turing reductions). Let $L_1$ and $L_2$ be languages. We say that $L_1$ log-space Turing machine reduces to $L_2$ ($L_1\le_T^{\log}L_2$), if $L_1\in L^{L_2}$. Similarly we say $L_1$ polynomial time Turing reduces to $L_2$ ($L_1\le_T^PL_2$), if $L_1\in P^{L_2}$.

It also satisfies **transitivity**.

:thinking:Lemma 2. Transitivity.

- $L_1\le_T^{\log}L_2,L_2\le_T^{\log}L_3\Rightarrow L_1\le_T^{\log}L_3$
- $L_1\le_T^{P}L_2,L_2\le_T^{P}L_3\Rightarrow L_1\le_T^{P}L_3$

:thinking:Proposition 3. The class $L,NL,P,PSPACE,EXP$ are all closed under log-space Turing reductions. The class $P,PSPACE,EXP$ are all closed under polynomial-time Turing machine.

# The Polynomial-time Hierarchy via Oracles and Quantifiers

For a class of language $\mathcal C$, denote $co\mathcal C$ the class of complements of languages of $\mathcal C$.
$$
co\mathcal C=\{\overline L|L\in\mathcal C\}
$$
:book:Definition 18 (polynomial-time hierarchy). Let $\Delta_0^P=\Sigma_0^P=\Pi_0^P=P$. For $i\ge0$, define further $\Delta_{i+1}^P=P^{\Sigma_i^P}$, $\Sigma_{i+1}^P=NP^{\Sigma_i^P}$ and $\Pi_{i+1}^P=coNP^{\Sigma_i^P}$. Finally, let **Polynomial Hierarchy** $PH=\bigcup_{i\ge0}\Sigma_i^P$.

The classes $\Delta_i^P,\Sigma_i^P,\Pi_i^P$ are said to make up level $i$ of the polynomial-time hierarchy, and $PH$ denotes the entire polynomial-time hierarchy.

Note that $\Delta_1^P=P^P=P$, $\Sigma_1^P=NP^P=NP$, $\Pi_1^P=coNP^P=coNP$.

On second level, we have $P^{NP}, NP^{NP}, coNP^{NP}$, continuing ad infimum.

Straightforward:
$$
\Delta_i^P\subseteq\Sigma_i^P\cap\Pi_i^P\subseteq\Sigma_i^P\cup\Pi_i^P\subseteq\Delta_{i+1}^P
$$
:dart:Theorem 27. $PH\subseteq PSPACE$.

> Using polynomial space simply simulate the non-deterministic Turing machine same way as in proof of Theorem 10.

Then prove an equivalent definition of the classes $\Sigma_i^P$ and $\Pi_i^P$ in terms of **quantifiers**.

:dart:Theorem 28. Let $L$ be any language and let $i\ge1$. Then $L\in\Sigma_i^P$ iff there is a polynomial $q$ and another language $L'\in\Pi_{i-1}^P$  s.t.
$$
\forall x:[x\in L\Leftrightarrow\exists y:|y|\le q(|x|)\land\langle x,y\rangle\in L'].
$$

> 看不懂了。

:thinking:Corollary 3. Let $L$ be any language and let $i\ge1$. Then $L\in\Pi_i^P$ iff there is a polynomial $q$ and another language $L'\in\Sigma_{i-1}^P$  s.t.
$$
\forall x:[x\in L\Leftrightarrow\forall y:|y|\le q(|x|)\Rightarrow\langle x,y\rangle\in L'].
$$
:thinking:Corollary 4. Let $L$ be any language and let $i\ge1$. Then $L\in\Sigma_i^P$ iff there is a polynomial $q$ and another language $L'\in P$  s.t.
$$
\forall x:[x\in L\Leftrightarrow\exists^qy_1\forall^qy_2\cdots Q_i^qy_i:\langle x,y_1,y_2,\cdots,y_i\rangle\in L'],
$$
where $Q_i$ is $\exists$ if $i$ is odd, and $\forall$ if $i$ is even. Likewise, $L\in\Pi_i^P$ iff there is a polynomial $q$ and another language $L'\in P$ s.t.
$$
\forall x:[x\in L\Leftrightarrow\forall^qy_1\exists^qy_2\cdots Q_i^qy_i:\langle x,y_1,y_2,\cdots,y_i\rangle\in L'],
$$
where $Q_i$ is $\exists$ if $i$ is even, and $\forall$ if $i$ is odd.

Generalize whether $P=NP,NP=coNP$ to question whether $\Delta_i^P=\Sigma_i^P,\Sigma_i^P=\Pi_i^P$ for $i\gt1$.

:dart:Theorem 29. If $\Sigma_i^P=\Pi_i^P$ for some $i\ge1$ then $PH=\Sigma_i^P$.

> 继续看 天书。

# Completeness in the Polynomial-time Hierarchy

$\Sigma_iSAT$ Problem

Instance: Quantified Boolean formula with no free variables $\psi=\exists x_1\forall x_2\cdots Q_ix_i\varphi(x_1,\cdots,x_i)$, where $x_j\in\{0,1\}^{n_j}$ for all $j$ and $Q_i=\forall$ for even $i$ and $\exists$ for odd $i$.

Question: Is $\psi$ true?



$\Pi_iSAT$ Problem

Instance: Quantified Boolean formula with no free variables $\psi=\forall x_1\exists x_2\cdots Q_ix_i\varphi(x_1,\cdots,x_i)$, where $x_j\in\{0,1\}^{n_j}$ for all $j$ and $Q_i=\forall$ for odd $i$ and $\exists$ for even $i$.

Question: Is $\psi$ true?



:dart:Theorem 30. $\Sigma_iSAT$ is complete for $\Sigma_i^P$ and $\Pi_iSAT$ is complete for $\Pi_i^P$.

> 前面的推论 4 都没懂……这个证明就更不懂了

!!! info

    In case of $Q_i=\exists$, we ended up with a CNF formula $\varphi$.

    In case of $Q_i=\forall$, we ended up with a DNF formula $\varphi$.



:dart:Theorem 31. If $PH$ has a complete language, then $PH$ collapses.

> This follows since $PSPACE$ has complete problems.
>
> Collapses 在此处指多项式层次结构中某一层以上的所有级别都重合的情况。

# The Karp-Lipton Theorem

A possible way to prove $P\neq NP$ would be to show that there is some language in $NP$ that is not computed by a family of polynomial size Boolean circuits, i.e., that $NP\not\subseteq P/poly$.

This is stronger than $P\neq NP$ since $P\subseteq P/poly$. It turns out that it is not stronger than the assumption that the polynomial-time hierarchy does not collapse.

:dart:Theorem 32. Suppose that $NP\subseteq P/poly$. Then $PH=\Sigma_2^P\cap\Pi_2^P$.

> Proof by Theorem 29, Corollary 4.
>
> 继续看不懂……