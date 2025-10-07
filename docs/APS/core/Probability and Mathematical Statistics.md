---
title: APS 重点复习课程之概率论与数理统计
author: tinsir888
date: 2022/10/19
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 概率论与数理统计
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: e7db866c
---

# Overview

Basic Conceptions

- Frequency and Probability
- Conditional Probability
- Independence

Random Variable

- Discrete Random Variable
- Continuous Variable

Radom Variables

- 2-dimensional Random Variables
- Marginal Distribution
- Conditional Distribution

Expectation and Variance

- Expectation
- Variance
- Covariance and Correlation Coefficient

Law of Large Numbers and Central Limit Theorem

Sampling Distribution

Estimation Theory

Hypothesis Testing

# Basic Conceptions

## Frequency and Probability

### Frequency

1. Non-negativity: $0\leq f_n(A)\leq 1$
2. Full set: $f_n(S)=1$
3. Additivity: $f_n(\bigcup A_i)=\sum f_n(A_i)$, which $A_i$ is pairwise disjoint

### Probability

1. Non-negativity: $P(A)\geq 0$
2. Normative: $P(S)=1$
3. Additivity: $P(\bigcup A_i)=\sum P(A_i)$, which $A_i$ is pairwise disjoint

## Conditional Probability

### Definition

$P(B|A)=\frac{P(AB)}{P(A)}$

1. Non-negativity
2. Normative
3. Additivity

### Multiply Theorem

$P(AB)=P(B|A)P(A)$ for $P(A)>0$

### Full Probability Formula

$P(A)=\sum P(A|B_i)P(B_i)$, which $\bigcup B_i$ is full set and $B_i$ is pairwise disjoint.

### Bayesian Formula

$P(B|A)=\frac{P(B)P(A|B)}{P(A)}$

Posterior: P(B|A)

Likelihood: P(A|B)

Prior: P(B)

Evidence: P(A)

## Independence

A, B are mutually independent if $P(AB)=P(A)P(B)$.

# Random Variable

## Discrete Random Variable

### 0-1 Distribution

$P\{X=k\}=p^k(1-p)^k,k=0,1\ (0\lt p\lt 1)$

### Bernoulli Experiment and Binomial Distribution

#### Bernoulli Experiment

- Experiment E has only 2 possible outcomes: $A$ and $\bar A$
- $P(A)=p,P(\bar A)=1-p$

#### Binominal Distribution

- n-time Bernoulli Experiment

  probability of incident A occur k times in n-time experiments is $C_n^kp^k(1-p)^{n-k}$

- $X\sim B(n,p)$

### Poisson Distribution

$P\{X=k\}=\frac{\lambda^ke^{-\lambda}}{k!},k=0,1,2,\cdots$

$X\sim\pi(\lambda)$

#### Poisson Theorem

Limit of binominal distribution is Poisson distribution

$\lim_{n\rightarrow\infty}C_n^kp_n^k(1-p)^{n-k}=\frac{\lambda^ke^{-\lambda}}{k!}$

## Continuous Variable

### Cumulative Distribution Function

$F(x)=P\{X\leq x\},x\in R$

Distribution functions are non-decreasing functions.

$F(-\infty)=0,F(+\infty)=1$

### Probability Density Function

$f(x)=\frac{dF(x)}{dx}\geq 0$

$F(x)=\int_{-\infty}^xf(u)du$

$P\{x_1\lt X\leq x_2\}=F(x_2)-F(x_1)=\int_{x_1}^{x_2}f(x)dx$

#### Uniform Distribution

$f(x)=\frac{1}{b-a},a\lt x\lt b$

$X\sim U(a,b)$

#### Exponential Distribution

$f(x)=\frac{1}{\theta}e^{-\frac{x}{\theta}},x\gt 0$

#### Normal Distribution

aka Gaussian Distribution

$f(x)=\frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x-\mu)^2}{2\sigma^2}}$

$X\sim N(\mu,\sigma^2)$

Symmetry: $x=\mu$

Maximum: $f(\mu)=\frac{1}{\sqrt{2\pi}\sigma}$

Standard Normal Distribution: $\mu=0,\sigma=1$

law of 3σ

# Radom Variables

## 2-dimensional Random Variables

Cumulative Distribution Function: $F(x,y)=P\{(X\leq x)\bigcup(Y\leq y)\}=P\{X\leq x,Y\leq y\}$

aka joint distribution.

For discrete variables (x,y) can be finite or infinite.

- $F(x,y)=\sum_{x_i}\sum_{y_j}p_{ij}$

For continuous variables (x,y)

- $F(x,y)=\int_{-\infty}^y\int_{-\infty}^xf(u,v)dudv$
- $f(x,y)$ is joint probability density
  - $f(x,y)\geq 0$
  - $F(\infty,\infty)=\int_{-\infty}^\infty\int_{-\infty}^\infty f(u,v)dudv=1$
  - if f(x,y) is continuous at point (x,y), $\frac{\partial F(x,y)}{\partial x\partial y}=f(x,y)$

### n-dimensional random variables

$F(x_1,x_2,\cdots,x_n)=P\{X_1\leq x_1,X_2\leq x_2,\cdots,X_n\leq x_n\}$

## Marginal Distribution

For random continuous variable X,Y:

- Marginal distribution function
  - $F_X(x)=F(x,\infty)=\int_{-\infty}^x[\int_{-\infty}^\infty f(u,v)dv]du$
  - $F_Y(y)=F(\infty,y)=\int_{-\infty}^y[\int_{-\infty}^\infty f(u,v)du]dv$
- Marginal probability density
  - $f_X(x)=\int_{-\infty}^\infty f(x,y)dy$
  - $f_Y(y)=\int_{-\infty}^\infty f(x,y)dx$

## Conditional Distribution

$P\{X=x_i,Y=y_j\}=p_{ij}$

marginal distrubution

$P\{X=x_i\}=p_{i\cdot}=\sum_{j=1}^\infty p_{ij}$

$P\{Y=y_j\}=p_{\cdot j}=\sum_{i=1}^\infty p_{ij}$

$P\{X=x_i|Y=y_j\}=\frac{P\{X=x_i,Y=y_j\}}{P\{Y=y_j\}}=\frac{p_{ij}}{p_{\cdot j}}$

### Conditional Probability Density

$f_{X|Y}(x|y)=\frac{f(x,y)}{f_Y(y)}$

# Expectation and Variance

## Expectation

For discrete random variable, $E(X)=\sum_{k=1}^\infty x_kp_k$.

For continuous random variable, $E(X)=\int_{-\infty}^\infty xf(x)dx$

aka mathematical expectation or average

if Y=g(x), $E(Y)=E[g(X)]=\sum_{k=1}^\infty g(x_k)p_k$ (discrete)

$E(Y)=E[g(X)]=\int_{-\infty}^\infty g(x)f(x)dx$ (continuous)

### Multivariable

Given $Z=g(X,Y)$

$E(Z)=E[g(X,Y)]=\iint g(x,y)f(x,y)dxdy$ (continuous)

$E(Z)=E[g(X,Y)]=\sum\sum g(x_i,y_j)p_{ij}$

### Properties of Expectation

Given constant C, random variable X,Y

1. $E(CX)=CE(X)$
2. $E(C)=C$
3. $E(X+Y)=E(X)+E(Y)$
4. if X, Y are mutually independent, $E(XY)=E(X)E(Y)$

## Variance

### Definition

$D(X)=Var(X)=E\{[X-E(X)]^2\}$

$=\int (x-\mu)^2f(x)dx$ (continuous)

standard variance $\sigma(X)=\sqrt{D(X)}$

standardized random variable $X^*=\frac{X-\mu}{\sigma}$

### Properties of Variance

Given constant C, random variable X, Y

1. $D(C)=0$

2. $D(CX)=C^2D(X)$

3. $D(X+Y)=D(X)+D(Y)+2E\{(X-E(X))(Y-E(Y))\}$

   when X, Y are mutually independent, $D(X+Y)=D(X)+D(Y)$

4. $D(X)=0\Leftrightarrow P\{X=E(X)\}=1$

### Chebyshev's Inequality

Given random variable X, which satisfy $E(X)=\mu$,$D(X)=\sigma^2$

$\forall\epsilon\gt 0$, $P\{|X-\mu|\geq\epsilon\}\leq\frac{\sigma^2}{\epsilon^2}$

## Covariance and Correlation Coefficient

Covariance

- $Cov(X,Y)=E\{[X-E(X)][Y-E(Y)]\}$

  $=E(XY)-E(X)E(Y)$

- $Cov(X,Y)=Cov(Y,X)$

- $Cov(X,X)=D(X)$

Correlation coefficient 

- $\rho_{XY}=\frac{Cov(X,Y)}{\sqrt{D(X)D(Y)}}$

- $|\rho_{XY}|\leq 1$

- the more $|\rho_{XY}|$ close to 1, the more linear correlated between X and Y,

  the more $|\rho_{XY}|$ close to 0, the less linear correlated between X and Y

# Law of Large Numbers and Central Limit Theorem

## LLN (Law of Large Numbers)

### Weak Law

$\bar{X}_n\rightarrow\mu$, when $n\rightarrow\infty$

aka $\lim_{n\rightarrow\infty}\Pr(|\bar{X}_n-\mu|\lt\epsilon)=1$

### Strong Law

$\Pr(\lim_{n\rightarrow\infty}\bar X_n=\mu)=1$

## CLT (Central Limit Theorem)

### Independent and Identically Distributed

Given a set of **same-distributed** random variables $X_1,X_2,\cdots,X_n$ which are **mutually independent**.

Standardized random variables of $\sum X_k$: $Y_n=\frac{\sum X_k-E(\sum X_k)}{\sqrt{D(\sum X_k)}}=\frac{\sum X_k-n\mu}{\sqrt n\sigma}$

Distribution function of $Y_n$: $\lim_{n\rightarrow\infty}F_n(x)=\Phi$$(x)$, which means $Y_n$ approximately obeys standard normal distribution when n is very large.

### Lyapunov's Theorem

Given a set of random variables $X_1,X_2,\cdots,X_n$ which are **mutually independent**.

$E(X_k)=\mu_k$, $D(X_k)=\sigma_k^2\gt 0$

Let $B_n^2=\sum\sigma_k^2$

if $\exists\delta\gt 0$, $\frac{1}{B_n^{2+\delta}}\sum_kE\{|X_k-\mu_k|^{2+\delta}\}\rightarrow 0$,

then standardized random variable for $\sum X_k$ approximately obeys $N(0,1)$.

$\sum X_k=B_nZ_n+\sum_k\mu_k$ approximately obeys $N(\sum_k\mu_k,B_n^2)$.

### De Moivre-Laplace Theorem

Given $\eta_n$ obey $B(n,p)$,

$\forall x$, $\lim_{n\rightarrow\infty}\{\frac{\eta_n-np}{\sqrt{np(1-p)}}\leq x\}=\Phi(x)$

aka limit of binominal distribution is normal distribution.

# Sampling Distribution

sample mean $\bar X=\frac{1}{n}\sum_iX_i$

sample variance $S^2=\frac{1}{n-1}\sum_i(X_i-\bar X_i)=\frac{1}{n-1}(\sum_iX_i^2-n\bar X^2)$

## Empirical Distribution Function

Let $S(x)$ be number of samples less than $x$ in $X_1,X_2,\cdots,X_n$.

Empirical distribution function $F_n(x)=\frac{1}{n}S(x)$

## Chi-squared Distribution

$\chi^2$ distribution: given $X_1,X_2,\cdots,X_n\sim N(0,1)$ (independent).

Let $Q=\sum_iX_i^2$, then Q is distributed according to the $\chi^2$ distribution with **n degrees of freedom**. $Q\sim\chi^2(n)$.

### Properties of Chi-squared Distribution

- Additive $\chi_1^2\sim\chi^2(n_1)$, $\chi_2^2\sim\chi^2(n_2)$. then

  $\chi_1^2+\chi_2^2\sim\chi^2(n_1+n_2)$

- $E(\chi^2)=n$, $D(\chi^2)=2n$

## t Distribution

$X\sim N(0,1)$, $Y\sim\chi^2(n)$, they are mutually independent.

Let $z=\frac{X}{\sqrt{Y/n}}$, then z is distributed according to the t distribution with **n degrees of freedom**. $z\sim t(n)$ 

aka Student distribution

## F Distribution

$U\sim\chi^2(n_1)$, $V\sim\chi^2(n_2)$, they are mutually independent.

Let $F=\frac{U/n_1}{V/n_2}$, then F is distributed according to the F distribution with **(n1,n2) degrees of freedom**. $F\sim F(n_1,n_2)$

## Distribution of Sample Mean and Sample Variance

for any distribution with existent mean and variance:

$E(\bar X)=\mu$, $D(\bar X)=\sigma^2/n$

### Some Theorem

Samples $X_1,X_2,\cdots,X_n$ come from $N(\mu,\sigma^2)$,

1. $\bar X\sim N(\mu,\sigma^2/n)$
2. $\frac{(n-1)S^2}{\sigma^2}\sim\chi^2(n-1)$
3. $\bar X$ and $S^2$ are mutually independent
4. $\frac{\bar X-\mu}{\sigma/\sqrt n}\sim t(n-1)$

Samples $X_1,X_2,\cdots,X_{n_1}$ and $Y_1,Y_2,\cdots,Y_n$ come from $N(\mu_1,\sigma_1^2)$ and $N(\mu_2,\sigma_2^2)$ respectively.

$S_1^2=\frac{\sum_i(X_i-\bar X)^2}{n_1-1}$, $S_2^2=\frac{\sum_i(Y_i-\bar Y)^2}{n_2-1}$

5. $\frac{S_1^2/S_2^2}{\sigma_1^2\sigma_2^2}\sim F(n_1-1,n_2-1)$

6. when $\sigma_1^2=\sigma_2^2=\sigma^2$,

   $\frac{(\bar X-\bar Y)-(\mu_1-\mu_2)}{S_w\sqrt{\frac{1}{n_1}+\frac{1}{n_2}}}\sim t(n_1+n_2-2)$

   $S_w^2=\frac{(n_1-1)S_1^2+(n_2-1)S_2^2}{n_1+n_2-2}$

# Estimation Theory

## Point Estimation

$X_1,X_2,\cdots,X_n$ is one sample of X

$x_1,x_2,\cdots,x_n$ is a corresponding sample valule

Estimator $\hat\theta(X_1,X_2,\cdots,X_n)$

Estimate value $\hat\theta(x_1,x_2,\cdots,x_n)$

### Method of Moments 矩估计

Random variable X.

probability density function: $f(x;\theta_1,\cdots,\theta_k)$, $\theta_1,\cdots,\theta_k$ are waiting for estimation, $X_1,\cdots,X_n$ are samples from X.

Suppose the first k moments of distribution of X:

$\mu_l=E(X_l)=\int x^lf(x;\theta_1,\cdots,\theta_k)dx$ (continuous)

$\mu_l=E(X_l)=\sum x^lp(x;\theta_1,\cdots,\theta_k)dx$ (discrete)

$l=1,2,\cdots,k$

Sample moment $A_l=\frac{1}{n}\sum_iX_i^l$ convergence by probability to $\mu_l$

Let sample moments be estimator of moments of true distribution

### Maximum Likelihood Estimation

Random variable X, $P\{X=x\}=p(x;\theta)$

$\theta\in\Theta$ is parameter waiting for estimation.

Suppose $X_1,\cdots,X_n$ are samples from X, whose joint distribution is $\Pi_ip(x_i;\theta)$

Let $x_1,\cdots,x_n$ are a set of value of $X_1,\cdots,X_n$.

Likelihood function of sample $X_1,\cdots,X_n$ is:

$L(\theta)=\Pr\{X_1=x_1,\cdots,X_n=x_n\}=L(x_1,\cdots,x_n;\theta)=\Pi_ip(x_i;\theta)$

Maximum likelihood estimation value $\hat\theta(x_1,\cdots,x_n)$ satisfies:

$L(x_1,\cdots,x_n;\hat\theta)=\max_\theta L(x_1,\cdots,x_n;\theta)$

For continuous variable,

> let $\frac{dL(\theta)}{d\theta}=0$ or $\frac{d\ln L(\theta)}{d\theta}=0$ (log-likelihood)

For multi-parameters $\theta_1,\theta_2,\cdots,\theta_n$:

use likelihood equations: $\frac{\partial L}{\partial\theta_i}=0,i=1,2,\cdots,k$

or log-likelihood equations: $\frac{\partial\ln L}{\partial\theta_i}=0,i=1,2,\cdots,k$

## Criteria for Estimator

###  Bias of an Estimator

Estimator $\hat\theta(X_1,\cdots,X_n)$ has expectation $E(\hat\theta)$.

if $\forall\theta\in\Theta,E(\hat\theta)=\theta$, then $\hat\theta$ is an unbiased estimator of $\theta$

z.B. $S^2=\frac{\sum(X_i-\bar X)^2}{n-1}$ is an unbiased estimator of $\sigma^2$ whereas $\frac{\sum(X_i-\bar X)^2}{n}$ is biased.

### Effectiveness

the less variance is, the better estimator would be.

### Consistent Estimator

$\forall\theta\in\Theta,\forall\epsilon\gt 0,\lim_{n\rightarrow\infty}P\{|\hat\theta-\theta|\lt\epsilon\}=1$

## Confidence Interval (CI)

Distribution of X is $F(x;\theta),\theta\in\Theta$.

Given a value $\alpha\in(0,1)$, $X_1,\cdots,X_n$ are samples come from X.

Find 2 value $\underline\theta$ and $\overline\theta$:

- $P\{\underline\theta(X_1,\cdots,X_n)\lt\theta\lt\overline\theta(X_1,\cdots,X_n)\}\geq 1-\alpha$

Confidence interval: $(\underline\theta,\overline\theta)$

Confidence level: $1-\alpha$

Confidence lower and upper bound: $\underline\theta$ and $\overline\theta$

### Confidence Interval for Normal Distribution

#### Single Normal Distribution

Confidence interval with confidence level $1-\alpha$ for $\mu$:

> $\frac{\overline X-\mu}{\sigma/\sqrt n}\sim N(0,1)$
>
> $P\{|\frac{\overline X-\mu}{\sigma/\sqrt n}|\lt z_{\alpha/2}\}=1-\alpha$
>
> $(\overline X-\frac{\sigma}{\sqrt n}z_{\alpha/2},\overline X+\frac{\sigma}{\sqrt n}z_{\alpha/2})$

If $\sigma$ is unknown, replace it with $S$, then:

> $\frac{\overline X-\mu}{S/\sqrt n}\sim t(n-1)$
>
> Confidence interval with confidence level $1-\alpha$ for $\mu$ is:
>
> $(\overline X-\frac{S}{\sqrt n}t_{\alpha/2}(n-1),\overline X+\frac{S}{\sqrt n}t_{\alpha/2}(n-1))$

#### Double Normal Distributions

$X\sim N(\mu_1,\sigma_1^2)$ and $Y\sim N(\mu_2,\sigma_2^2)$

1. if $\sigma_1,\sigma_2$ are known, $\overline X\sim N(\mu_1,\sigma_1^2/n_1),\overline Y\sim N(\mu_2,\sigma_2^2/n_2)$

   then $\overline X-\overline Y\sim N(\mu_1-\mu_2,\sigma_1^2/n_1+\sigma_2^2/n_2)$ or

   $\frac{(\overline X-\overline Y)-(\mu_1-\mu_2)}{\sqrt{\sigma_1^2/n_1+\sigma_2^2/n_2}}\sim N(0,1)$

   Confidence interval with confidence level $1-\alpha$ for $\mu_1-\mu_2$:

   >  $(\overline X-\overline Y-z_{\alpha/2}\sqrt{\frac{\sigma_1^2}{n_1}+\frac{\sigma_2^2}{n_2}},\overline X-\overline Y+z_{\alpha/2}\sqrt{\frac{\sigma_1^2}{n_1}+\frac{\sigma_2^2}{n_2}})$

2. if $\sigma_1=\sigma_2=\sigma$ but are unknown,

   > $\frac{(\overline X-\overline Y)-(\mu_1-\mu_2)}{S_w\sqrt{\frac{1}{n_1}+\frac{1}{n_2}}}\sim t(n_1+n_2-2)$
   >
   > $S_w^2=\frac{(n_1-1)S_1^2+(n_2-1)S_2^2}{n_1+n_2-2}$

   Confidence interval with confidence level $1-\alpha$ for $\mu_1-\mu_2$:

   > $(\overline X-\overline Y-t_{\alpha/2}(n_1+n_2-2)S_w\sqrt{\frac{1}{n_1}+\frac{1}{n_2}},\overline X-\overline Y+t_{\alpha/2}(n_1+n_2-2)S_w\sqrt{\frac{1}{n_1}+\frac{1}{n_2}})$

3. if $\mu_1,\mu_2$ are unknown,

   $\frac{S_1^2/S_2^2}{\sigma_1^2/\sigma_2^2}\sim F(n_1-1,n_2-1)$

   Confidence interval with confidence level $1-\alpha$ for $\sigma_1^2/\sigma_2^2$:

   > $(\frac{S_1^2}{S_2^2}\frac{1}{F_{\alpha/2}(n_1-1,n_2-1)},\frac{S_1^2}{S_2^2}\frac{1}{F_{1-\alpha/2}(n_1-1,n_2-1)})$

## Confidence Interval for 0-1 Distribution

$X\sim B(n,p)$, $X_1,X_2,\cdots,X_n$ are samples of  X.

$\mu=p,\sigma^2=p(1-p)$

$\frac{\sum X_i-np}{\sqrt{np(1-p)}}=\frac{n\overline X-np}{\sqrt{np(1-p)}}\sim N(0,1)$

Confidence interval with confidence level $1-\alpha$ for $p$

> $P\{|\frac{n\overline X-np}{\sqrt{np(1-p)}}|\lt z_{\alpha/2}\}$
>
> $\rightarrow (n+z_{\alpha/2}^2)p^2-(2n\overline X+z_{\alpha/2}^2)p+n\overline X^2\lt 0$
>
> $\rightarrow (\frac{1}{2a}(-b-\sqrt{b^2-4ac}),\frac{1}{2a}(-b+\sqrt{b^2-4ac}))$
>
> $a=n+z_{\alpha/2}^2,b=-(2n\overline X+z_{\alpha/2}^2),c=n\overline X^2$

# Hypothesis Testing

我觉得 APS 面谈 25 分钟不会谈到这来……