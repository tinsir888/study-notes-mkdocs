---
title: APS 非重点复习课程之线性代数
author: tinsir888
date: 2022/10/30
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 线性代数
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: '81964547'
---

# Determinant

It's a function whose definition domain is the $n\times n$ matrix $A$.

Für eine nur aus einem Koeffizienten bestehende $1\times1$-Matrix $A$ ist

$\det A=\det(a_{11})=a_{11}$

ist $A$ eine $2\times 2$-Matrix, dann ist

$\det A=\det\left(\begin{array}{cc}a_{11} & a_{12} \\ a_{21} & a_{22} \end{array}\right)=a_{11}*a_{22}-a_{12}*a_{21}$

für eine $N\times N$-Matrix $A$ gilt die Formel:

$\det A=\sum_{j_1\cdots j_n}(-1)^{\tau(j_1\cdots j_n)}a_{1j_1}a_{2j_2}\cdots a_{nj_n}$

which $\tau$ means number of reverse pairs of permutation $j_1\cdots j_n$

# Matrix

A table of numbers in m rows and n columns arranged by m*n numbers $a_{ij}$ is called a matrix of m rows and n columns.

Addition, Subtraction, Multiple with numbers, Multiple with another matrix, Transpose of the matrix

# Invertible Matrix

$A^*$ is adjugate matrix

define: $A_{ij}$ is matrix determinant multiply $(-1)^{i+j}$ which delete i-th row and j-th column

$A^*=\left(\begin{array}{cc} A_{11}&\cdots&A_{1n}\\ &\cdots&\\A_{n1}&\cdots& A_{nn} \end{array}\right)$

$A^{-1}=\frac{1}{\det A}A^*$

Another way of calculating invert matrix

$(A,I)$ Elementary Line Transformation to $(I,B)$, then $B=A^{-1}$

# Cramer's Rule

$\left\{\begin{array}{rcl} a_{11}x_1+a_{12}x_2+\cdots+a_{1n}x_n=b_1\\a_{21}x_1+a_{22}x_2+\cdots+a_{2n}x_n=b_2\\ \cdots\cdots\cdots\\a_{n1}x_1+a_{n2}x_2+\cdots+a_{nn}x_n=b_n \end{array}\right.$

$D=\det\left(\begin{array}{cc}a_{11}&a_{12}&\cdots&a_{1n}\\a_{21}&a_{22}&\cdots&a_{2n}\\&\cdots&\cdots\\a_{n1}&a_{n2}&\cdots&a_{nn} \end{array}\right)$

$D_1=\det\left(\begin{array}{cc}b_1&a_{12}&\cdots&a_{1n}\\b_2&a_{22}&\cdots&a_{2n}\\&\cdots&\cdots\\b_n&a_{n2}&\cdots&a_{nn} \end{array}\right)$

$D_2=\det\left(\begin{array}{cc}a_{11}&b_1&\cdots&a_{1n}\\a_{21}&b_2&\cdots&a_{2n}\\&\cdots&\cdots\\a_{n1}&b_n&\cdots&a_{nn} \end{array}\right)$

......

$D_n=\det\left(\begin{array}{cc}a_{11}&a_{12}&\cdots&b_1\\a_{21}&a_{22}&\cdots&b_2\\&\cdots&\cdots\\a_{n1}&a_{n2}&\cdots&b_n \end{array}\right)$

Then, $x_i=\frac{D_i}{D}$. (if D=0, no solution.)

# Eigen Value

矩阵的特征值

z.B. what's the eigen value & eigen vector of $A=\left(\begin{array}{cc} -1&1&1\\1&-1&1\\1&1&-1 \end{array}\right)$?

Eigen polynomial of $A$ is: $|\lambda I-A|=\det\left(\begin{array}{cc} \lambda+1&-1&-1\\-1&\lambda+1&-1\\-1&-1&\lambda+1 \end{array}\right)$

$=\det\left(\begin{array}{cc} \lambda+1-1-1&-1+\lambda+1-1&-1-1+\lambda+1\\-1&\lambda+1&-1\\-1&-1&\lambda+1 \end{array}\right)$

$=(\lambda-1)\times\det\left(\begin{array}{cc} 1&1&1\\-1&\lambda+1&-1\\-1&-1&\lambda+1 \end{array}\right)$

$=(\lambda-1)\times\det\left(\begin{array}{cc} 1&1&1\\0&\lambda+2&0\\0&0&\lambda+2 \end{array}\right)$

$=(\lambda-1)(\lambda+2)^2$

$|\lambda I-A|=0\Rightarrow\lambda=1,-2$

So 1 and -2 are eigen values of matrix A.

For eigen vector $x$, $Ax=\lambda x$

$(A-\lambda I)x=0$, solve the 2 equations with $\lambda=1,-2$

when $\lambda=1$, $x_1=x_2=x_3$, so $k_1=(1,1,1)^T$, $\xi k_1(\xi\neq0)$ is eigen vector of $A$.

when $\lambda=-2$, $x_1+x_2+x_3=0$ so $k_2=(a,b,-a-b)^T$ is eigen vector of $A$.

trace $tr(A)=\sum\lambda$

determinant $\det A=\Pi\lambda$