---
title: 算法和计算复杂度 17 LLL Algorithm
author: tinsir888
date: 2024/12/11
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 9415313b
---

这课终于上完了。上得真挺难受:sob:

Lecture notes by Rothvoss, University of Washington

# Lattices

Lattices are **integral combinations** of linearly independent vectors:
$$
\left\{\sum_{i=1}^k\lambda_i\mathbf b_i\vert\lambda_1,\cdots,\lambda_k\in\Z\right\}
$$
where $\mathbf b_1,\cdots,\mathbf b_k\in\R^n$ are linearly independent vectors.

Another equivalent definition: a lattice is a **discrete subgroup** of $\R^n$.

If $k=n$ the lattice has **full rank**. Without specification, the we consider lattice with full rank.

Let $\mathbf B\in\R^{n\times n}$ be the matrix that has the basis vector $\mathbf b_1,\cdots,\mathbf b_n$ as columns, the lattice is
$$
\Lambda(\mathbf B)=\left\{\sum_{i=1}^k\lambda_i\mathbf b_i\vert\lambda_1,\cdots,\lambda_k\in\Z\right\}.
$$

The matrix $\mathbf B$ is called **basis** of the lattice $\Lambda(\mathbf B)$.

:warning:A lattice has more than one basis.

## Unimodular matrices

幺模矩阵，~~什么玩意从来没听说过~~

:book:Definition 1 (unimodular matrices). An $n\times n$ matrix $\mathbf U$ is called **unimodular**, if $\mathbf U\in\Z^{n\times n}$ and $\det(\mathbf U)\in\{\pm1\}$.

简单一句话，就是行列式等于正负 1 的方阵。

:thinking:Lemma 1. If $\mathbf U$ is unimodular, then $\mathbf U^{-1}$ is also unimodular.

:thinking:Lemma 2. Let $\mathbf B_1,\mathbf B_2\in\R^{n\times n}$ non-singular. Then $\Lambda(\mathbf B_1)=\Lambda(\mathbf B_2)$ iff there is a unimodular matrix $\mathbf U$ with $\mathbf B_2=\mathbf B_1\mathbf U$.

## The fundamental parallelepiped

“基平行六面体”

The fundamental parallelepiped of the lattice $\Lambda(\mathbf B)$ is the polytope
$$
\mathcal P(\mathbf B)=\left\{\sum_{i=1}^n\lambda_i\mathbf b_i\vert0\le\lambda_i\lt1\forall i\in[n]\right\}.
$$
简单来讲，就是两个基向量在 0 到 1 的线性组合覆盖的所有区域。在二维空间是个平行四边形，在三维空间是个平行六面体。

For every vector $\mathbf x\in\R^n$, there is a unique coefficient vector $\lambda\in\R^n$ so that $\mathbf x=\sum_{i=1}^n\lambda_i\mathbf b_i$. ~~高中数学知识~~

So $\mathbf x$ can be written as
$$
\mathbf x=\sum_{i=1}^n\lfloor\lambda_i\rfloor\mathbf b_i+\sum_{i=1}^n(\lambda_i-\lfloor\lambda_i\rfloor)\mathbf b_i.
$$
The left half $\in\Lambda(\mathbf B)$, and the right half $\in\Lambda(\mathbf B)$.

This translates of the parallelepiped placed at lattices points exactly partition the $\R^n$. This is called a **tiling** of $\R^n$.

平铺

Fundamental parallelepiped can be rewritten as
$$
\mathcal P(\mathbf B)=\{\mathbf {Bx}:\mathbf x\in[0,1[^n\},
$$
it is the image of the hyper-cube $[0,1]^n$ under the linear map given by $\mathbf B$. By transformation formula
$$
vol(\mathcal P(\mathbf B))=vol([0,1[^n)\cdot|\det(\mathbf B)|.
$$
Fundamental parallelepiped it self depend on the choice of the basis, but its volume doesn't.

:thinking:Lemma 3. Let $\mathbf B\in\R^{n\times n}$ and $\Lambda=\Lambda(\mathbf B)$ be the generated lattice. Then the determinant of the lattice $\det(\Lambda)=|\det(\mathbf B)|$ is independent of the chosen basis. Moreover, $\det(\Lambda)=vol(\mathcal P(\mathbf B))$.

# Minkowski's Theorem

闵可夫斯基格点定理

A set $K$ is **convex** if $\forall x,y\in K$ and $0\le\lambda\le1$, $\lambda x+(1-\lambda)y\in K$.

A set $K$ is centrally symmetric if $x\in K$ iff $-x\in K$.

For convex symmetric set $K$, we define a **Minkowski norm**:
$$
\vert\vert x\vert\vert_K=\min\{\lambda\ge0:x\in\lambda K\},\\
\lambda K=\{\lambda x|x\in K\}.
$$
That is, $||x||_K$ gives the scaling factor that one needs until the scaled copy of $K$ includes $x$.

Define $y+K=\{x+y|x\in K\}$ as the translate of $K$ by $y$.

Minkowski's theorem says that every large enough symmetric convex set must contain a non-zero lattice point.

:dart:Theorem 4 (Minkowski). Let $K\subseteq\R^n$ be a bounded symmetric convex set with $vol(K)\gt2^n$. Then $K\cap(\Z^n\diagdown\{\mathbf 0\})\neq\empty$.

:dart:Theorem 5 (Blichfeldt). Let $S\subseteq\R^n$ be a measurable set with $vol(S)\gt1$. Then there are $\mathbf s_1,\mathbf s_2\in S$ with $\mathbf s_1-\mathbf s_2\in\Z^n$.

## Minkowski's Theorem for General Lattices

:dart:Theorem 6. (Minkowski's first theorem). Let $\Lambda$ be a lattice and $K$ be a symmetric convex set with $vol(K)\gt2^n\det(\Lambda)$. Then $K\cap(\Lambda\diagdown\{\mathbf 0\})\neq\empty$.

## Shortest Vector

The shortest vector w.r.t. L2-norm:
$$
SVP(\Lambda)=\min\{\vert\vert\mathbf x\vert\vert_2\vert\mathbf x\in\Lambda\diagdown\{\mathbf 0\}\}
$$
is its length.

:thinking:In fact, finding the shortest vector is an $NP$-hard problem.

We can get some estimates on it.

:dart:Theorem 7. Any lattice $\Lambda\subseteq\R^n$, one has $SVP(\Lambda)\le\sqrt n\cdot\det(\Lambda)^{1/n}$.

:dart:Theorem 8 (Minkowski's second theorem). For any full-rank lattice $\Lambda\in\R^n$, one has
$$
\left(\prod_{i=1}^n\lambda_i(\Lambda)\right)^{1/n}\le\sqrt n\cdot\det(\Lambda)^{1/n}.
$$

## Dirichlet's Theorem

:dart:Theorem 9. A vector $\alpha\in[0,1]^n$ of real numbers. There are numbers $p_1,\cdots,p_n\in\Z_{\ge0}$ and $q\in\{1,\cdots,Q\}$ so that
$$
\max_{i=1,\cdots,n}|\frac{p_i}{q}-\alpha_i|\le\frac{1}{Q^{1/n}q}.
$$

# Gram Schmidt Orthogonalisation

To find approximate shortest vector.

A lattice $\Lambda(\mathbf B)$, we want to find a non-zero vector in polynomial time that has length $||x||_2\le\alpha\cdot SVP(\Lambda(\mathbf B))$. Here $\alpha=\alpha(n)\ge1$, called approximation factor (as small as possible).

The Gram Schmidt orthogonalisation takes indepedent vectors $\mathbf b_1,\cdots,\mathbf b_n\in\R^n$ as input and computes an orthogonal basis $\mathbf b^*_1,\cdots,\mathbf b^*_n$ so that $\text{span}(\mathbf b_1,\cdots,\mathbf b_k)=\text{span}(\mathbf b^*_1,\cdots,\mathbf b^*_k)$ for all $k=1,\cdots.n$.

## Pseudo-code

Input: $\mathbf b_1,\cdots,\mathbf b_n\in\R^n$

Output: Orthogonal basis $\mathbf b^*_1,\cdots,\mathbf b^*_n$

1. $\mathbf b_1^*\gets\mathbf b_1$
2. $\mathbf b_2^*\gets\mathbf b_2-\mu_{1,2}\mathbf b_1^*$ with $\mu_{1,2}\gets\frac{\lang\mathbf b_2,\mathbf b_1^*\rang}{||\mathbf b_1^*||_2^2}$
3. ...
4. $\mathbf b_j^*\gets\mathbf b_j-\sum_{i\lt j}\mu_{i,j}\mathbf b_i^*$ with $\mu_{i,j}\gets\frac{\lang\mathbf b_j,\mathbf b_i^*\rang}{||\mathbf b_i^*||_2^2}\forall j=1,\cdots,n$

施密特正交化，属于是复习大一上学的线性代数了。

By Cavalieri's principle （aka 祖暅原理）, the shifting does not change the volume of the fundamental parallelepiped. Hence,
$$
\det(\Lambda(\mathbf B))=vol(\mathcal P(\mathbf B))=\prod_{i=1}^n||\mathbf b_i^*||_2.
$$
:dart:Theorem 10. Let $\mathbf B$ be a basis and $\mathbf B^*$ be its Gram Schmidt orthogonalisation. Then $SVP(\Lambda(\mathbf B))\ge\min_{i=1,\cdots,n}||\mathbf b_i^*||_2$

第一个正交化向量总是相等的。基向量的顺序很重要，这为 LLL 算法埋下伏笔。

# The Lenstra-Lenstra-Lovász Algorithm

:dart:Theorem 11. Given a regular matrix $\mathbf B\in\Q^{n\times n}$ one can compute a vector $\mathbf x\in\Lambda(\mathbf B)\diagdown\{\mathbf 0\}$ of length $\vert\vert\mathbf x\vert\vert_2\le2^{n/2}\cdot SVP(\Lambda(\mathbf B))$ in polynomial time.

The running time is actually $O(n^6\log^3(n\vert\vert\mathbf B\vert\vert_\infty))$.

Consider basis $\mathbf b_1,\cdots,\mathbf b_n$:

- **Subtracting vectors from each other**. In $n=2$, we can always subtract multiple of $\mathbf b_1$ from it so that $\mu_{1,2}\le 1/2$. In higher dimensions, we can achieve $|\mu_{i,j}|\le1/2$ for every $i,j$.
- **Switching the order**. It might be that $\mathbf b_1$ is much longer than $\mathbf b_2$. In this case, we may swap the order of $\mathbf b_1$ and $\mathbf b_2$. For higher dimension, we swap $\mathbf b_i$ and $\mathbf b_{i+1}$ if $\vert\vert\mathbf b_i\vert\vert_2\gg\vert\vert\mathbf b_{i+1}\vert\vert_2$.

## Coefficient Reduction

A basis is **coefficient-reduced** if $\vert\mu_{i,j}\vert\le1/2$ for all $i\lt j$.

:thinking:Lemma 12. Given any basis $\mathbf B$ one can compute a coefficient-reduced basis $\tilde{\mathbf B}$ in polynomial time so that $\Lambda(\tilde{\mathbf B})=\Lambda(\mathbf B)$ and the Gram Schmidt orthogonalisations are identical.

## Main Procedure

:book:Definition of LLL-reduced: Let $\mathbf B\in\R^{n\times n}$ be a lattice basis and let $\mu_{i,j}$ be the coefficients from the Gram Schmidt orthogonalisation. The basis is called LLL-reduced if the following is satisfied.

- Coefficient reduced: $\vert\mu_{i,j}\vert\le1/2$ for all $i\lt j$.
- Lovász condition: $\vert\vert\mathbf b_i^*\vert\vert_2^2\le2\vert\vert\mathbf b_{i+1}^*\vert\vert_2^2$ for $i=1,\cdots,n-1$.

:thinking:Lemma 13. Let $\mathbf B$ be an LLL-reduced basis. Then $||\mathbf b_1||_2\le2^{n/2}\cdot SVP(\Lambda(\mathbf B))$.

> By Lovász condition, $||\mathbf b_1||_2^2=||\mathbf b_1^*||_2^2\le2||\mathbf b_2^*||_2^2\le\cdots\le2^{i-1}||\mathbf b_i^*||_2^2$.
>
> By Theorem 10, $SVP(\Lambda(\mathbf B))^2\ge 2^{-n}\cdot||\mathbf b_1||_2^2$.
>
> Taking square roots then gives the claim.

## Pseudo-code

Input: A lattice basis $\mathbf B\in\R^{n\times n}$

Output: An LLL reduced basis $\tilde{\mathbf B}$

1. Compute a Gram Schmidt orthogonalisation $\mathbf b_1^*,\cdots,\mathbf b_n^*$ with coefficients $\mu_{i,j}$ and update whenever we change the order of the basis.
2. while $\mathbf B$ is not LLL reduced, do
   - Apply coefficient reduction so that $\mu_{i,j}\le1/2$.
   - If there is an index $i$ with $||\mathbf b_i^*||_2^2\gt2||\mathbf b_{i+1}^*||_2^2$ then swap $\mathbf b_i$ and $\mathbf b_{i+1}$ in the ordering.

:thinking:Lemma 14. Suppose we have vectors $\mathbf a_1,\mathbf a_2$ with Gram Schmidt orthogonalisation $\mathbf a_1^*,\mathbf a_2^*$ so that $||\mathbf a_1^*||_2^2\ge2||\mathbf a_2^*||_2^2$ and let $\mu\le1/2$. Let $\mathbf a_2^{**},\mathbf a_1^{**}$ be the Gram Schmidt orthogonalisation for the reverse order $\mathbf a_2,\mathbf a_1$. Then $||\mathbf a_2^{**}||_2\le\sqrt{3/4}\cdot||\mathbf a_1^*||_2$.