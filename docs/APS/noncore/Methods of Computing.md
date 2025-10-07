---
title: APS 非重点复习课程之计算方法
author: tinsir888
date: 2022/11/05
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 计算方法
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 4449b777
---

 

# Lagrange Interpolation

拉格朗日插值

$P_n(x)=\sum_{i=1}^ny_i(\Pi_{j\neq i}^{1\leq j\leq n}\frac{x-x_j}{x_i-x_j})$

Using polynomials to approximate the original function.

## Residuals

$R(x)=f(x)-p(x)=\frac{f^{(n+1)}(\xi)}{(n+1)!}\omega(x)$, $\xi\in(a,b)$ relies on x.

# Newton Interpolation

## Difference Quotient

aka 差商

$f[x_i,x_{i+1}]=\frac{f(x_{i+1})-f(x_i)}{x_{i+1}-x_i}$ first order difference quotient

$f[x_i,x_{i+1},x_{i+2}]=\frac{f[x_{i+1},x_{i+2}]-f[x_i,x_{i+1}]}{x_{i+2}-x_i}$ second order difference quotient

m-th order difference quotient

## Formula

$N_n(x)=f(x_0)+f[x_0,x_1](x-x_0)+f[x_0,x_1,x_2](x-x_0)(x-x_1)+\cdots$

## Residuals

$R_n(x)=f[x_0,x_1,\cdots,x_n,x](x-x_0)(x-x_1)\cdots(x-x_n)$

# Runge Phenomenon

龙格现象

The more points used for interpolation, the more the result deviates from the original function.

## Solutions

divided the interval into several small intervals, and the interpolation is performed in each small intervals

# Spline Interpolation Cubic Polynomial

样条插值

$f(x)$ in $[a,b]$, given n+1 points $x_i$, $f(x_i)$. $S(x)$ satisfies:

1. in each small interval $[x_i,x_{i+1}]$, $S(x)$ is 3-times polynomial.
2. in each point $S(x_i)=f(x_i)$
3. in $[a,b]$, exist $S'(x)$ and $S''(x)$.

The $S(x)$ is Spline Interpolation Cubic Polynomial 样条插值三次

# Ordinary Least Squares

最小二乘法

Like find the minimum of loss function (Machine Learning)

1. Find the partial derivative for each x in the polynomial
2. put the results into a system of equations
3. calculate the result.