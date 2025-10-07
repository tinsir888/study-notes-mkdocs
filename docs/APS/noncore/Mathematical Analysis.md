---
title: APS 非重点复习课程之高等数学
author: tinsir888
date: 2022/10/29
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 高等数学
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: d04ccb12
---

# Single Variable

## Derivative

A function of a real variable $y=f(x)$ is differentiable at a point $a$ of its domain, if its domain contains an open interval $I$ containing $a$, the limit:

$L=\lim_{h\rightarrow0}\frac{f(a+h)-f(a)}{h}$

## L'Hôpital's Rule

Used to find the limit value of an expression

The limits of the numerator (分子) and denominator (分母) are both 0 or infinite.

## Taylor's Formular

$f(x)=\sum_{i=0}^\infty\frac{f^{i}(x_0)}{i!}(x-x_0)^i$

$e^x=1+x+\frac{x^2}{2!}+\frac{x^3}{3!}+o(x^3)$

## Indefinite Integral

$\int f(x)dx=F(x)+C$

## Definite Integral

The definition of definite integral is based on finding the area of a curved-sided trapezoid (曲边梯形). Therefore, the definition of definite integral is used to find the area, i.e. to get a number.

## Newton Leibniz Formula

Given $\int f(x)dx=\Phi(x)+C$

$\int_a^bf(x)dx=\Phi(b)-\Phi(a)$

# Multivariable

## Double Integral

How to calculate?

1. find the domain of each variable
2. step by step, each step for a variable

z.B. $\sigma$: y=cx, x=a, x=b, Ox. c>0, b>a>0, $\iint_\sigma(x+y)d\sigma$

> evidently, $a\leq x\leq b$, $0\leq y\leq cx$
>
> $\iint_\sigma(x+y)d\sigma$
>
> $=\int_a^bdx\int_0^{cx}(x+y)dy$
>
> $=\int_a^b[xy+\frac{y^2}{2}]_0^{cx}dx$
>
> $=\int_a^b(\frac{2c+c^2}{2})x^2dx$
>
> $=\frac{1}{6}(2c+c^2)(b^3-a^3)$

If introduce new prarms u,v s.t. $x=x(u,v)$, $y=y(u,v)$

then $\iint_D f(x,y)dxdy=\iint_{D'}f[x(u,v),y(u,v)]|\frac{\partial(x,y)}{\partial(u,v)}|dudv$

Jacobian $J=|\frac{\partial(x,y)}{\partial(u,v)}|=x'_uy'_v-x'_vy'_u$

## Line Integral

$\int_Cf(x,y)ds=\int_a^bf(x(t),y(t))\sqrt{x'(t)^2+y'(t)^2}dt$

z.B. $L$ is a part of unit circle in the first quadrant (第一象限), $\int_Lxyds$

> Parametric equation of $L$ is: $x=\cos t$, $y=\sin t$, $0\leq t\leq\frac{\pi}{2}$
>
> Then $\int_Lxyds=\int_0^{\frac{\pi}{2}}\cos t\sin t\sqrt{(-\sin t)^2+\cos^2t}dt$
>
> $=\int_0^{\frac{\pi}{2}}\cos t\sin tdt=\frac{1}{2}$
>
> or
>
> $y=\sqrt{1-x^2}$, $0\leq x\leq 1$
>
> Then $\int_Lxyds=\int_0^1x\sqrt{1-x^2}\times\sqrt{1+\frac{x^2}{1-x^2}}dx$
>
> $=\int_0^1xdx=\frac{1}{2}$

# Numerical Series

Convergence 收敛

Divergence 发散

p- series $\sum_n\frac{1}{n^p}$: when p>1, convergent; when p<=1, divergent.

z.B. $\sum_{n=1}^\infty\frac{1}{n(n+1)(n+2)}$

> $a_n=\frac{1}{n(n+1)(n+2)}=\frac{n+2-n}{2n(n+1)(n+2)}$
>
> $=\frac{1}{2n(n+1)}-\frac{1}{2(n+1)(n+2)}=b_n-b_{n+1}$
>
> $\lim_{n\rightarrow\infty}b_n=\lim_{n\rightarrow\infty}\frac{1}{2n(n+1)}=0=b$
>
> $\therefore \sum_{n=1}^\infty a_n=b_1-b=\frac{1}{4}$