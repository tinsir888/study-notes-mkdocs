---
title: 算法和计算复杂度 11 Probabilistic Checkable Proofs
author: tinsir888
date: 2024/10/26
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 4e02908b
---

Proof systems where a **polynomial-time probabilistic verifier** can spot-check a given written proof by inspecting a few randomly selected symbols of the proof. Cost: Introducing some error.

:book:Definition 48 (PCP verifier). A (non-adaptive) PCP verifier $V$ is a **polynomial-time probabilistic Turing machine** equipped with **an additional random access proof tape** together with a write-only query tape and a read-only answer tape. Two additional states: *query* state and *continue* state. The symbols on the proof tape are given by a proof alphabet.

Operation of $V$: relative to the proof $\pi$ placed on the proof tape.

Denote $V$ together with $\pi$: $V^\pi$. During computation $V^\pi$ can enter the query state at most once.

- Contents of the query tape is a list of positions of proof tape.
- Symbols of the proof tape placed in those positions are placed on the answer tape, after which computation resumes in the continue state.

We restrict that only **ONE ACCESS** to the proof.

Important parameters of PCP verifier:

1. Number of random bits used during computation
2. Number of queries made to the proof tape.

:book:Definition 49 (PCP languages). Let $r:\N\to\N,q:\N\to\N$ be functions, let $\Sigma$ be a non-empty finite set, and let $0\le s\lt c\le1$ be reals. $PCP_{c,s}^\Sigma(r(n),q(n))$ is the class of languages $L$ for which there exist a PCP verifier $V$ that on input $x$ of length $n$ uses at most $r(n)$ random bits and queries at most $q(n)$ positions of a proof $\pi\in\Sigma^*$ s.t.
$$
\forall x\in L,\exists\pi\in\Sigma^*:\Pr[V^\pi(x)=yes]\ge c\\
\forall x\not\in L,\exists\pi\in\Sigma^*:\Pr[V^\pi(x)=yes]\le s
$$
$c$ for completeness, $s$ for soundness.

We often use the notion as follows:

- $PCP_{c,s}(r(n),q(n))$ when $\Sigma=\{0,1\}$
- $PCP^\Sigma(r(n),q(n))$ when $c=1,s=1/2$
- $PCP(r(n),q(n))$ when both $\Sigma=\{0,1\}$ and $c=1,s=1/2$.

:thinking:Proposition 17.

1. $P=PCP(0,0)$
2. $NP=PCP(0,n^{O(1)})$
3. $coRP=PCP(n^{O(1)},0)$

By enumerating over all strings of $O(\log n)$ bits and using these in place of the random bits one obtain the slightly stronger statements $P=PCP(O(\log n),0)$ and $NP=PCP(O(\log n),n^{O(1)})$.

By enumerating over all possible proofs of length $O(\log n)$ one obtains $P=PCP(0,O(\log n))$.

Verifier of $PCP(r(n),q(n))$ is able to access at most $2^{r(n)}q(n)$ different positions of the proof string $\pi$.

:thinking:Proposition 18. $PCP(r(n),q(n))\subseteq NTIME(n^{O(1)}2^{r(n)})$.

:dart:Theorem 50 (PCP theorem). $NP=PCP(O(\log n),O(1))$.

:dart:Theorem 51. $NEXP=PCP(n^{O(1)},n^{O(1)})$.

## A weak version of the PCP theorem

:dart:Theorem 52. $NP\subseteq PCP((\log n)^{O(1)},(\log n)^{O(1)})$.

Some observations about interactive proofs and probabilistically checkable proofs.

1. Consider an interactive proof system $(P,V)$. On input $x$, we tabulate all possible replies $P(\langle x,m_1,\cdots,m_i\rangle)$ a prover $P$ may send during a run of the protocol with input $x$ into a written proof $\pi$.

   Then verifier $V$ may be viewed as an adaptive PCP verifier that instead of communicating with $P$ simply queries the proof $\pi$.

   In Sumcheck protocol, all msg of $V$ consist of uniformly random bits, which means that all queries may be made non-adaptively. Together with Theorem 49, this gives that $IP\subseteq PCP(n^{O(1)},n^{O(1)})$.

2. Arithmetic formula $F$ in Sumcheck is only evaluated once, at the end of the protocol, and on a uniformly chosen random input $(a_1,\cdots,a_n)\in\Z_p^n$.

For proving Theorem 52, we need to show that $3SAT\in PCP((\log n)^{O(1)},(\log n)^{O(1)})$.

> Some details is omitted.

:thinking:Lemma 18. For any function $f:\{0,1\}^\ell\to R$, where $R$ is any ring, there is a unique multilinear polynomial $\tilde f$ s.t. $f(x)=\tilde f(x)$ for all $x\in\{0,1\}^\ell$.

:thinking:Lemma 19. Let $f:\{0,1\}^\ell\to\{0,1\}$ and let $x\in\{1,\cdots,M\}^\ell$. Then
$$
|\tilde f(x)|\le2^\ell M^\ell=(2M)^\ell
$$
:book:Definition 50. Let $\varphi$ be a $3CNF$ formula with $n$ var and $m$ clauses and let functions $h_1,h_2,h_3$ and $s_1,s_2,s_3$ be defined as above. Let $a:\{0,1\}^\ell\to\Z$ be any function. We define the arithmetic formula $A_{\varphi,a}$ in $4\ell$ variables computing a polynomial of total degree $3(2\ell+2\ell)=12\ell$ by
$$
A_{\varphi,a}(c,v_1,v_2,v_3)=\prod_{i=1}^3\tilde h_i(c,v_i)(\tilde s_i(c)-\tilde a(v_i))^2.
$$
We have that $a:\{0,1\}^\ell\to\Z$ is an assignment satisfying $\varphi$ iff
$$
\sum_{(c,v_1,v_2,v_3)\in\{0,1\}^{4\ell}}A_{\varphi,a}(c,v_1,v_2,v_3)=0.
$$
:book:Definition 51. Let $f$ and $g$ be functions in $\ell$ variables taking values in a set $S$. The **normalized Hamming distance** $d(f,g)$ between $f$ and $g$ is
$$
d(f,g)=\Pr_{x\in S^\ell}[f(x)\neq g(x)].
$$
:book:Definition 52. Let $\mathbb F$ be field and let $S\subseteq\mathbb F$. We say that a function $f:S^\ell\to\mathbb F$ is $\delta$**-close  to a multilinear polynomial**, if there exist a multilinear polynomial $P$ in $\ell$ variables s.t. $d(f,P)\le\delta$, viewing $P$ as a function $P:S^\ell\to\mathbb F$.

The **aligned full-line test** of a function $f:S^\ell\to\mathbb F$ consists of the following steps:

1. Pick $i\in[\ell]$ uniformly at random.
2. Pick $a_1,\cdots,a_{i-1},a_{i+1},\cdots,a_\ell\in S$ uniformly at random.
3. Evaluate $f$ on the set of inputs $L=\{a_1,\cdots,a_{i-1},b,a_{i+1},\cdots,a_\ell|b\in S\}$.
4. Accept if $f_L(b)=f(a_1,\cdots,a_{i-1},b,a_{i+1},\cdots,a_\ell)$ is a linear function. Otherwise reject.

:dart:Theorem 53. Let $|S|\ge4\ell+2$. Let $\beta$ be the probability that the aligned full-line test rejects the function $f:S^\ell\to\mathbb F$. Then either $\beta\gt\frac{1}{8\ell}$ or $f$ is $\beta\ell$-close to a multilinear polynomial.

:thinking:Corollary 12. Let $|S|\le4\ell+2$ and $\epsilon\le1/8$. If a function $f:S^\ell\to\mathbb F$ is not $\epsilon$-close to a multilinear polynomial, the aligned full-line test rejects $f$ with probability at least $\epsilon/\ell$.

~~别学了，学不明白的……~~