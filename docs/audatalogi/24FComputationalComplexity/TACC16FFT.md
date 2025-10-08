---
title: 算法和计算复杂度 16 Fast Fourier Transform
author: tinsir888
date: 2024/11/20
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 4f31c6f5
---

# Algebraic Structure

$GF(p^m)$: Galois field of order $p^m$, $p$ is prime.

!!! info

    伽罗瓦域是一个包含加法和乘法的代数结构，满足加法和乘法的交换律、结合律、分配律，每个元素都有逆元。



$\mathbb Z_n$: Integers modulo $n\ge1$.

$M_{m,n}(R)$: $m$ by $n$ matrices over a ring $R$.

!!! info

    这个玩意是一个矩阵，只是其中每一个元素都来自环。**乘法通常不满足交换律**。



$M_n(R)$: $M_{n,n}(R)$.

# The Discrete Fourier Transform

[~~数 字 信 号 处 理~~](https://tinsir888.github.io/posts/bafe16c2.html)

The key to fast multiplication of integers and polynomials is the discrete Fourier transform.

## Roots of Unity

$$
e^{\frac{2\pi}{n}i}=\cos\frac{2\pi}{n}+i\sin\frac{2\pi}{n}\\
i=\sqrt{-1}
$$

A complex number $\alpha\in\mathbb C$ is a $n$-th **root of unity** if $\alpha^n=1$.

It is a **primitive $n$-th root of unity** if $\alpha^m\neq1$ for all $m=1,\cdots,n-1$.

There are exactly $\varphi(n)$ primitive $n$-th roots of unity where $\varphi(n)$ is the number of positive integers less than or equal to $n$ that are relatively prime to $n$.

:thinking:Lemma of cancellation property. Let $\omega$ be any primitive $n$-th root of unity.

$$
\sum_{j=1}^{n-1}\omega^{js}=\begin{cases}
0\text{ if }s\not\equiv0\mod n\\
n\text{ if }s\equiv0\mod n
\end{cases}
$$

> - If $s\equiv0\mod n$, $\omega^{js}=1$. Thus the lemma holds.
>
> - If $s\not\equiv0\mod n$, $x^n-1=(x-1)\sum_{j=0}^{n-1}x^j$.
>
>   Substituting $x=\omega^s$ makes LHS $0$. Thus RHS equals $0$, since $\omega^s\neq1$, the lemma holds.

Let $F(\omega)=F_n(\omega)$ denote the matrix

$$
\left[
\begin{array}{ccccc}
1&1&1&\cdots&1\\
1&\omega&\omega^2&\cdots&\omega^{n-1}\\
1&\omega^2&\omega^4&\cdots&\omega^{2(n-1)}\\
\vdots&&&&\vdots\\
1&\omega^{n-1}&\omega^{2(n-1)}&\cdots&\omega^{(n-1)^2}
\end{array}
\right]
$$

:book:Definition of DFT and its inverse. Let $\mathbf a=(a_0,\cdots,a_{n-1})^\top\in\mathbb C^n$. The **discret Fourier transform** of $\mathbf a$ is

$$
DFT_n(\mathbf a)=\mathbf A=(A_0,\cdots,A_{n-1})^\top\\
A_i=\sum_{j=0}^{n-1}a_j\omega^{ij}\forall i=0,\cdots,n-1
$$

i.e.,

$$
DFT_n(\mathbf a)=
\frac{1}{n}
\cdot
\left[
\begin{array}{ccccc}
1&1&1&\cdots&1\\
1&\omega^{-1}&\omega^{-2}&\cdots&\omega^{-n+1}\\
\vdots&&&&\vdots\\
1&\omega^{-n+1}&\omega^{-2(n-1)}&\cdots&\omega^{-(n-1)^2}
\end{array}
\right]
$$

$$
\cdot\left[
\begin{array}{c}
A_0\\ A_1\\ \vdots\\ A_{n-1}
\end{array}
\right].
$$

The inverse discrete Fourier transform of $\mathbf A=(A_0,\cdots,A_{n-1})^\top$ is $DFT_n^{-1}(\mathbf A)=\frac{1}{n}F(\omega^{-1})\cdot \mathbf A$.

$$
DFT_n^{-1}(\mathbf a)=F(\omega)\cdot\left[
\begin{array}{c}
a_0\\ a_1\\ \vdots\\ a_{n-1}
\end{array}
\right]
=\left[
\begin{array}{c}
A_0\\ A_1\\ \vdots\\ A_{n-1}
\end{array}
\right].
$$

:thinking:Lemma of reverse multiplication. $F(\omega^{-1})\cdot F(\omega)=F(\omega)\cdot F(\omega^{-1})=nI_n$ where $I_n$ is the identity matrix.

> Proof by Lemma of cancellation property.

Connection to polynomial evaluation and interpolation. Let $\mathbf a$ be the coefficient vector of the polynomial $P(X)=\sum_{i=1}^{n-1}a_iX^i$. Then computing $DFT(\mathbf a)$ amounts to evaluating the polynomial $P(X)$ at all the $n$-th roots of unity, at

$$
X=1,\omega,\omega^2,\cdots,\omega^{n-1}.
$$

Similarly, computing $DFT^{-1}(\mathbf A)$ amounts to recovering the polynomial $P(X)$ from its values $(A_0,\cdots,A_{n-1})$ at the same $n$ points.

## The Fast Fourier Transform

Naive algorithm for $DFT$ and $DFT^{-1}$: $O(n^2)$.

Fast Fourier Transform: $O(n\log n)$.

FFT algorithm: divide-and-conquer.

Let $n$ be a power of $2$. Computing $DFT(\mathbf a)$ amounts to computing the $n$ values

$$
P(1),P(\omega),\cdots,P(\omega^{n-1}).
$$

Actually, $P(X)$ has even part and odd part.

$$
P(X)=P_e(X^2)+X\cdot P_o(X^2).
$$

Where $P_e$ and $P_o$ are polynomials of degrees at most $n/2$ and $(n-1)/2$ respectively.

### Pseudo code of FFT

Input: A polynomial $P(X)$ with coefficients given by an $n$-vector $\mathbf a$, $\omega$, a primitive $n$-th root of unity.

Output: $DFT_n(\mathbf a)$

1. Evaluate $P_e(X^2)$ and $P_o(X^2)$ at $X^2=1,\omega^2,\omega^4,\cdots,\omega^{2n-2}$.
2. Multiply $P_o(\omega^{2j})$ by $\omega^j$ for $j=0,\cdots,n-1$.
3. Add $P_e(\omega^{2j})$ to $\omega^jP_o(\omega^{2j})$ for $j=0,\cdots,n-1$.

Analysis of FFT:

- In step 1, since $\omega^n=1,\omega^{n+2}=\omega^2,\cdots,\omega^{2n-2}=\omega^{n-2}$. So it suffices to evaluate $P_e$ and $P_o$ at only $n/2$ values.

  i.e., at all the $(n/2)$-th roots of unity. This is equivalent to the problem of computing $DFT_{n/2}(P_e)$ and $DFT_{n/2}(P_o)$.

- Step 2 and 3 take $n$ multiplications and $n$ additions respectively.

Thus, $T(n)=2T(n/2)+2n\Rightarrow T(n)=2n\log n$ for $n$ is a power of $2$.

:dart:Theorem of FFT's complexity. Assuming the availability of a primitive $n$-th root of unity, the discrete Fourier transform $DFT_n$ and its inverse can be computed in $O(n\log n)$ complex arithmetic operations.

# Polynomial Multiplication

## Convolution and polynomial multiplication

Assume $n\ge2$. The convolution of two $n$-vectors $\mathbf a,\mathbf b$ is the $n$-vector

$$
\mathbf c=\mathbf a*\mathbf b=(c_0,\cdots,c_{n-1})^\top
$$

where $c_i=\sum_{j=0}^ia_jb_{i-j}$.

Let $P(X),Q(X)$ be polynomials of degrees less than $n/2$. Then $R(X)=P(X)Q(X)$ is a polynomial of degree less than $n-1$.

Let $\mathbf a$ and $\mathbf b$ denote the coefficient vectors of $P$ and $Q$. Then it is not hard to see $\mathbf a*\mathbf b$ gives the coefficient vector of $R(X)$.

**Thus convolution is essentially polynomial multiplication.**

:dart:Theorem of convolution. Let $\mathbf a,\mathbf b$ be $n$-vectors whose initial $\lfloor n/2\rfloor$ entries are zeros. Then

$$
DFT^{-1}(DFT(\mathbf a)*DFT(\mathbf b))=\mathbf a*\mathbf b.
$$

This reduces the problem of convolution to two $DFT$ and $DFT^{-1}$ computations.

:dart:Theorem of polynomial computation's complexity. Assuming the availability of a primitive $n$-th root of unity, we can compute the product $PQ$ of two polynomials $P,Q\in\mathbb C[X]$ of degree less than $n$ in $O(n\log n)$ complex operations.

# Modular FFT

Define $\mathbb Z_M$ where $M=2^L+1$.

An element $x\in\mathbb Z_M$ is a zero-divisor if there exist $y\neq0$ s.t. $x\cdot y=0$.

The inverse element of $x$ is $y$ s.t. $xy=1$.

:thinking:An element $x\in\mathbb Z_M$ has a multiplicative inverse $x^{-1}$ iff $x$ is not a zero-divisor.

## Representation and basic operations modulo M

Let $2^L\equiv-1\mod M$ be denoted with special symbol $\bar 1$. Each element of $\mathbb Z_M\diagdown\{\bar 1\}$ as binary string $(b_{L-1},\cdots,b_0)$ of length $L$; the element $\bar1$ is given a special representation.

> For example, $M=17,L=4$. Then $13$ is represented by $(1,1,0,1)$.

It's easy to add and subtract in $\mathbb Z_M$ under this representation in $O(L)$ time.

Multiplying $X$ by $2^j$ amounts to left-shifting the string $X$ by $j$ positions.

If $(b_{L-1},\cdots,b_0)$ is multiplied by $2^j(0\lt j\lt L)$, the results is given as

$$
(b_{L-j-1},\cdots,b_0,0,\cdots,0)-(0,\cdots,0,b_{L-1},\cdots,b_{L-j})
$$

## Primitive roots of unity modulo M

Let $K=2^k$ and $K$ divides $L$. We define $\omega=2^{L/K}$.

:thinking:Lemma: In $\mathbb Z_M$, $\omega$ is a primitive $(2K)$-th root of unity.

:thinking:The cancellation property holds:

$$
\sum_{j=0}^{2K-1}\omega^{js}\equiv
\begin{cases}
0\mod M\text{ if }s\not\equiv0\mod 2K\\
2K\mod M\text{ if }s\equiv0\mod 2k
\end{cases}
$$

:dart:Theorem. The transforms $DFT_{2K}(\mathbf a)$ and $DFT^{-1}_{2K}(\mathbf A)$ for $(2K)$-vectors $\mathbf a,\mathbf A\in(\mathbb Z_M)^{2K}$ can be computed using the FFT method, taking $O(KL\log K)$ bit operations.

> $T(2K)=2T(K)+O(KL)$

# Fast Integer Multiplication

:dart:Theorem of integer multiplication's complexity. Given two integers $u,v$ of sizes at most $n$ bits, we can form their product $uv$ in $O(n\log n\log\log n)$ bit operations.

Here we prove a weaker bound $n\log^{2.6}n$.

## A simplified Schönhage-Strassen algorithm

Goal: Product $W=UV$. Assume that $U,V$ are $N$-bit binary numbers where $N=2^n$.

Choose $K=2^k,L=3\cdot2^\ell$ where

$$
k=\lfloor\frac{n}{2}\rfloor,\ell=\lceil n-k\rceil.
$$

$k,\ell$ are integers, $n$ is not necessarily be integer.

$k+\ell\ge n$, we may view $U$ as $2^{k+\ell}$-bit numbers, padding with zeros. Break up $U$ into $K$ pieces, each of bit size $2^\ell$.

Similar padding to $V$, we have $(2K)$-vector:

$$
\bar U=(0,\cdots,0,U_{K-1},\cdots,U_0)\\
\bar V=(0,\cdots,0,V_{K-1},\cdots,V_0)
$$

where each component has $2^\ell$ bits.

We can regard $\bar U,\bar V$ as coefficient vectors of the polynomials $P(X)=\sum_{j=0}^{K-1}U_jX^j$ and $Q(X)=\sum_{j=0}^{K-1}V_jX_j$. Let

$$
\bar W=(W_{2K-1},\cdots,W_0)
$$

be the convolution of $\bar U,\bar V$. Each $W_i$ in $\bar W$ satisfies the inequality

$$
0\le W_i\le K\cdot2^{2\cdot2^\ell}
$$

since it is the sum of at most $K$ products of the form $U_jV_{i-j}$. Hence

$$
0\le W_i\lt 2^{3\cdot2^\ell}\le M
$$

where $M=2^L+1$ as usual.

$\bar W$ is the coefficient vector of the product $R(X)=P(X)Q(X)$. Since $P(2^{2^\ell})=U$ and $Q(2^{2^\ell})=V$, it follows that $R(2^{2^\ell})=UV=W$. Hence

$$
W=\sum_{j=0}^{2K-1}2^{2^\ell j}W_j.
$$

We can easily obtain each summation in this sum from $\bar W$ by multiplying each $W_j$ with $2^{2^\ell j}$. As each $W_j$ has $k+2\cdot2^\ell\lt L$ non-zero bits.

Each bit of $W$ is obtained by summing at most $3$ bits plus at most $2$ carry bits.

:thinking:Lemma: The product $W$ can be obtained from $\bar W$ in $O(N)$ bit operations.

> $\bar W=DFT^{-1}(DFT(\bar U)\cdot DFT(\bar V))$.
>
> These transforms take $O(KL\log K)=O(n\log n)$ bit operations.
>
> The inner product requires $2K$ multiplications of $L$-bit numbers, which is recursively accomplished. Let $T(N)$ denote the bit complexity of algorithm:
>
> $$
> T(N)=O(N\log N)+2K\cdot T(L).
> $$
>
> Write $t(n)=T(N)/N$ where $N=2^n$. The recurrence becomes
>
> $$
> t(n)=O(n)+2\frac{K}{N}T(L)=O(n)+2\frac{3}{L}T(L)=O(n)+6t(n/2+c),
> $$
> 
> for some constant $c$. Define $s(n)=t(n+2c)$. Then
>
> $$
> s(n)=O(n+2c)+6t((n/2)+2c)=O(n)+6s(n/2).
> $$
>
> This resolves to $s(n)=O(n^{\lg 6})$. Back-substituting, we obtain
>
> $$
> T(N)=O(N\log^\alpha N),\alpha=\lg6\lt2.5848
> $$

## Refinements

The choice of $L=3\cdot2^\ell$ is sub-optimal.

The method implies

$$
T(N)=O(N\log^{2+\epsilon}N)
$$

for any $\epsilon\gt0$.

Improvement: compute each $W_i$ in two parts:

- Let $M'=2^{2\cdot2^\ell}+1$ and $M''=K$.
- Since $M',M''$ are relatively prime and $W_i\lt M' M''$, it follows that if we have computed $W_i'=W_i\mod M'$ and $W_i''=W_i\mod M''$, then we can recover $W_i$ using the Chinese remainder theorem.
- Computing all the $W_i''$'s and the reconstruction of $W_i$ from $W_i',W_i''$ can be accomplished in linear time.

Thus the new recurrence is
$$
t(n)=n+4t(n/2),
$$
which has the solution $t(n)=O(n^2)$ or $T(N)=O(N\log^2N)$.

In order to get ultimate result, we need improve recurrence $t(n)=n+2t(n/2)$ by using a variant convolution called *negative wrapped convolution* and $DFT_K$ instead of $DFT_{2K}$.

