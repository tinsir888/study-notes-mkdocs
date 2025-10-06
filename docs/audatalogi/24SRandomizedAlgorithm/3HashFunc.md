---
title: 随机算法 3 Hash Functions
author: tinsir888
date: 2024/2/12
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: c9fbf51f
---

Lecturer: Kasper

Lecture notes is based on *High Speed Hashing for Integers and Strings* by Mikkel Thorup

# Hash Functions

Universe $U$. Mapping randomly to a range $[m]=\{0,\cdots,m-1\}$.

Truly random hash function $h:U\rarr[m]$.

$h$ is idealized, can not be implemented. To represent a truly random hash functions, we need to store at least $|U|\log_2m$ bits. (too large, impossible!)

Idea: hash functions contain only a small element or seed of randomness so that the hash function is sufficiently random for the desired application. e.g. Prime number $p$, for random hash function $h:[p]\rarr[0,\cdots,p-1]$ could be $h(x)=(ax+b)\mod p$ where $a,b$ are random numbers that together form the random seed describing the function.

## Definition and Properties

Definition 1: A hash function $h:U\rarr[m]$ is a random variable in the class of all functions $U\rarr[m]$, i.e., it consists of a random variable $h(x)$ for each $x\in U$.

3 things we care about $h$:

1. Space. The size of the random seed that is necessary to calculate $h(x)$ given $x$.
2. Speed. The time it takes to calculate $h(x)$ given $x$.
3. Properties of random variables.

# Universal Hashing

To generate a random hash function $h:U\rarr[m]$ from a key universe $U$ to a set of hash values $[m]=\{0,\cdots,m-1\}$. We think of $h$ as a random variable following some distribution over functions $U\rarr[m]$. We want $h$ to be **universal** which means that for any given distinct keys $x,y\in U$, when $h$ is picked at random, we have low collision probability:
$$
\Pr_h[h(x)=h(y)]\le{1\over m}
$$
For many applications, it suffices if for some $c=O(1)$, we have
$$
\Pr_h[h(x)=h(y)]\le{c\over m}
$$
Then $h$ is called **c-approximately universal**.

Exercise 2.1 Is the truly independent hash function $h : U \rarr [m]$ universal?

> Yes.

Exercise 2.2 If a hash function $h : U \rarr [m]$ has collision probability 0, how large must $m$ be?

> $m\ge|U|$.

Exercise 2.3 Let $u \le m$. Is the identity function $f (x) = x$ a universal hash function $[u] → [m]$?

> Yes.

## Applications

*hash tables with chaining*. (detail see in [Lecture 2](https://tinsir888.github.io/posts/36868314.html))

## Multiply-mod-prime

Assume $m\lt u$ and $m\gt1$.

The classic universal hash function is based on a prime number $p\ge u$. We pick a uniformly random $a\in[p]_+=\{1,\cdots,p-1\}$ and $b\in[p]=\{1,\cdots,p-1\}$, and define $h_{a,b}:[u]\rarr[m]$ by
$$
h_{a,b}(x)=((ax+b)\text{ mod }p)\text{ mod }m
$$
Given any distinct $x,y\in[u]\subseteq[p]$, we want for random $a,b$:
$$
\Pr_{a\in[p]_+,b\in[p]}[h_{a,b}(x)=h_{a,b}(y)]\lt{1\over m}
$$
:warning:Strict inequality! Strictly better.

Fact: If $p$ is prime and $\alpha,\beta\in[p]_+$ then $\alpha\beta\not\equiv0\text{ mod } p$.

Let $x,y\in[p],x\neq y$ be given. For given pair $(a,b)\in[p]^2$, define $(q,r)\in[p]^2$ by
$$
(ax+b)\text{ mod }p=q\\
(ay+b)\text{ mod }p=r
$$
Lemma: These two equations define a 1-1 correspondence between pairs $(a,b)$ and $(q,r)$.

> (Use Fact to prove)
>
> Assume that there exist two different pairs $a,b$ and $a',b'$ which corresponds to same $q,r$.
> $$
> (ax+b)\text{ mod }p=q\ (1)\\
> (ay+b)\text{ mod }p=r\ (2)\\
> (a'x+b')\text{ mod }p=q\ (3)\\
> (a'y+b')\text{ mod }p=r\ (4)
> $$
> For $((1)-(2))-((3)-(4))$, by the Fact above, it yields $a-a'\equiv0\mod p$.
>
> For $((1)-(3))-((2)-(4))$, it yields $b-b'\equiv0\mod p$.
>
> Thus $a=a',b=b'$ contradicts assumption.
>
> Thus its bijection.

Since $x\neq y$, by Fact above, $r=q\Lrarr a=0$. Thus when we pick $(a,b)\in[p]_+\times[p]$, we get $r\neq q$.

Why the inequality is strict?

> We get a collision $h_{a,b}(x)=h_{a,b}(y)$ iff $q\text{ mod }m=r\text{ mod }m$.
>
> Let us fix $q$ and set $i=q\text{ mod }m$. There are at most $\lceil p/m\rceil$ values $r\in[p]$ with $r\text{ mod }m=i$ and one of them is $r=q$. Therefore, the number of $r\in[p]\diagdown\{q\}$ with $r\text{ mod }m=i=q\text{ mod }m$ is at most $\lceil p/m\rceil-1\le(p+m-1)/m-1=(p-1)/m$. However, there are values of $i'\in[m]$ with only $\lfloor p/m\rfloor$ values of $q'\in[p]$ with $q'\text{ mod }m=i'$, and then the number of $r'\in[p]\diagdown\{q'\}$ with $r'\text{ mod }m=i'=q'\text{ mod }m$ is $\lfloor p/m\rfloor-1\lt\lceil p/m\rceil-1$. Summing over all $p$ values of $q\in[p]$, we get that the number of $r\in[p]\diagdown\{q\}$ with $r\text{ mod }m=i=q\text{ mod }m$ is strictly less than $p(\lceil p/m\rceil-1)\le p(p-1)/m$. Then 1-1 correspondence implies that there are strictly less than $p(p-1)/m$ collision pairs $(a,b)\in[p]_+\times[p]$. Since each of the $p(p-1)$ pairs from $[p]_+\times[p]$ are equally likely, we conclude that the collision probability is strictly below $1/m$.

### Implementation for 64-bit Keys

Hashing scheme
$$
h(x)=((ax+b)\text{ mod }p)\text{ mod }m
$$
$u=2^{64},m=2^{20}$.

Since $p\gt u=2^{64}$, we generally need to reserve more than 64 bits for $a\in[p]_+$, so the product $ax$ has more than 128 bits. We can get the product of 32-bit numbers, representing them as 64-bit numbers, and getting the full 64-bit result. We need at least 6 such 64-bit multiplications to compute $ax$.

How do we compute  $ax\text{ mod }p$? For 64-bit numbers, we have a general mod-operation. Idea: Let $p$ be a Mersenne prime $p=2^q-1$, e.g. $2^{61}-1,2^{89}-1$.
$$
x\equiv(x\text{ mod }2^q+\lfloor x/2^q\rfloor)\mod p
$$

> To prove this, it suffices to prove: $x\text{ mod }2^q+\lfloor x/2^q\rfloor=x-\lfloor x/2^q\rfloor p$. (Obviously)

```c++
//p=pow(2,q)-1
y=(x&p)+(x>>q);
if(y>=p) y-=p;
```

## Multiply-shift

Hashing from $w$-bit integers to $\ell$-bit integers.

We pick a uniformly random odd $w$-bit integer $a$, and then we compute $h_a:[2^w]\rarr[2^\ell]$, as
$$
h_a(x)=\lfloor(ax\text{ mod }2^w)/2^{w-\ell}\rfloor.
$$
```c++
//(a*x%pow(2,w))>>(w-l)
return ((a%pow(2,w)*(x%pow(2,w)))%pow(2,w))>>(w-l);
//for w=64, variable are uint64_t
return (a*x)>>(64-l);
```

This scheme is faster than $h_{a,b}(x)=((ax+b)\text{ mod }p)\text{ mod }n$. Numbers are stored as bit strings, with the least significant bit to the right. Integer division by a power of 2 is accomplished by a right shift. For hashing 64-bit integers, we further exploit that 64-bit multiplication automatically discards overflow, which is the same as multiplying modulo $2^{64}$.

Multiply-shift is **2-approximately universal**, i.e., for $x\neq y$,
$$
\Pr_{\text{odd }a\in[2^w]}[h_a(x)=h_a(y)]\le2/2^\ell=2/m
$$
To prove this, it suffices to prove that the probability that bis $w-\ell,\cdots,w-1$ of $a(y-x)$ are all 0s or all 1s is at most $2/2^\ell$.

Fact: If $\alpha$ is odd and $\beta\in[2^q]_+$, then $\alpha\beta\not\equiv0(\text{ mod }2^q)$.

> ~~Proof omitted.~~
>
> 证明的大致思路：假设 $x\lt y,h_a(x)=h_a(y)$。显然，如果要碰撞，那么 $ay=ax+a(y-x)$ 中，$a(y-x)$ 对应第 $w-\ell$ 到第 $w-1$ 这 $\ell$ 位比特必须全 0 或者全 1。再证全 0 和全 1 的概率是 $2/2^\ell$.
>
> 再用上面的 Fact “任何奇数和 2 的多次幂互质”证明 $a(y-x)$ 对应第 $w-\ell$ 到第 $w-1$ 这 $\ell$ 位比特是均匀分布即可。

# Strong Universality

**Strong universality**: For $h:[u]\rarr[m]$, we consider pairwise events of the form that for given distinct keys $x,y\in[u]$ and possibly non-distinct hash values $q,r\in[m]$, we have $h(x)=q$ and $h(y)=r$. We say a random hash function $h:[u]\rarr[m]$ is strongly universal if the probability of every pairwise event is $1/m^2$.

We note that if $h$ is strongly universal, it is also universal since
$$
\Pr[h(x)=h(y)]=\sum_{q\in[m]}\Pr[h(x)=q\and h(y)=q]=m/m^2=1/m
$$
Observation: An equivalent definition of strong universality is that each key is hashed uniformly into $[m]$, and that every two distinct keys are hashed independently.

> ~~Proof omitted.~~

Emphasizing the independence, strong universality is also called **2-independence**, as it concerns a pair of two events. As for universality, we may accept some relaxed notion of strong universality e.g. *3-independence, k-independence*

**c-approximately strongly universality**: $h:[U]\rarr[m]$ is c-approximately strongly universal if

1. $h$ is c-approximately uniform, meaning for every $x\in U$ and for every hash value $q\in[m]$, we have $\Pr[h(x)=q]\le c/m$;
2. Every pair of distinct keys hash independently.

## Applications

**Coordinated sampling**: allocation of strongly universal hashing.

Basic idea: small samples can reason about the similarity of huge sets.

First we consider sampling from a single set $A\subseteq U$ using a strongly universal hash function $h:U\rarr[m]$ and a threshold $t\in\{0,\cdots,m\}$. We now sample $x$ if $h(x)\lt t$, which by uniformity happens with probability $t/m$ for any $x$.

Let $S_{h,t}(A)=\{x\in A|h(x)\lt t\}$ denote the resulting sample from $A$. Then by linearity of expectation, $\mathbb E[|S_{h,t}(A)|]=|A|\cdot t/m$. Conversely, this means that if we have $S_{h,t}(A)$, then we can estimate $|A|$ as $|S_{h,t}(A)|\cdot m/t$.

The important application is not just sampling from a single set, but rather the sampling from different sets $B$ and $C$ so that we can later reason about the similarity, estimating the sizes of their union $B\cup C$ and intersection $B\cap C$.
$$
S_{h,t}(B\cup C)=S_{h,t}(B)\cup S_{h,t}(C)\\
S_{h,t}(B\cap C)=S_{h,t}(B)\cap S_{h,t}(C)
$$
We can then estimate the size of the union and intersection multiplying the corresponding sample sizes by $m/t$.

Sampling from different sets can be done in a distributed way.

Application: In machine learning, we can store the samples of many different large sets; On the Internet, routers can store samples of the packets passing through.

To get a fixed sampling probability $t/m$ for each $x\in U$, we only need that $h:U\rarr[m]$ is uniform. This ensures that the estimate $|S_{h,t}(A)|\cdot m/t$ of $A$ is unbiased.

For $a\in A$, let $X_a=[h(a)\lt t],X=\sum_{a\in A}X_a$. Then $X=|S_{h,t}(A)|$, but the reasoning below applies when $X_a$ is any 0-1 indicator variable that depends only $h(a)$.

Because $h$ is strongly universal, for any distinct $a,b\in A$, we have that $h(a),h(b)$ are independent, and hence so are $X_a,X_b$. Therefore $X=\sum_{a\in A}X_a$ is the sum of pairwise independent 0-1 variables. Now the following concentration bound applies to $X$.

Lemma: Let $X=\sum_{a\in A}X_a$ where $X_a$ are pairwise independent 0-1 variables. Let $\mu=\mathbb E[X]$. Then $\text{Var}(X)\le\mu$ and for any $q\gt0$,
$$
\Pr[|X-\mu|\ge q\sqrt\mu]\le1/q^2
$$

> ~~Proof by Chebyshev's inequality~~

## Multiply-mod-prime

The classic strongly universal hash scheme is a multiply-mod-prime scheme. For some prime $p$, uniformly at random we pick $(a,b)\in[p]^2$ and define $h_{a,b}:[p]\rarr[p]$ by
$$
h_{a,b}(x)=(ax+b)\text{ mod }p.
$$
Each pair $h_{a,b}(x)=q$ and $h_{a,b}(y)=r$ happens with probability $1/p^2$.

## Multiply-shift

For any bit-string $z$ and integers $j\gt i\ge0,z[i,j)=z[i,j-1]$ denotes the number represented by bits $i,\cdots,j-1$, so
$$
z[i,j)=\lfloor(z\text{ mod }2^j)/2^i\rfloor.
$$
To get strongly universal hashing $[2^w]\rarr[2^\ell]$, we may pick any $\overline w\ge w+\ell-1$. For any pair $(a,b)\in[\overline w]^2$, we define $h_{a,b}:[2^w]\rarr[2^\ell]$ by
$$
h_{a,b}(x)=(ax+b)[\overline w-\ell,\overline w).
$$
It is strongly universal, proof omitted.

# Beyond Strong Universality

There are more advanced algorithmic applications that need more powerful hash functions.

**k-independent hash functions**: A hash random function $H:U\rarr[m]$ is k-independent if for any distinct keys $x_1,\cdots,x_k\in[u]$, the hash values $H(x_1),\cdots,H(x_k)$ are independent random variables, each uniformly distributed in $[m]$.

From prime $p$, we can implement a k-independent $H:[p]\rarr[p]$ using $k$ random coefficients $a_0,\cdots,a_{k-1}\in[p]$, defining
$$
H(x)=\sum_{i=0}^{k-1}a_ix^i\text{ mod }p.
$$
However, there is no efficient implementation of k-independent hashing on complex objects such as variable length strings. What we can do instead is to use the signature idea from Section 2, and first hash the complex keys to unique hash values from a limited integer domain $[u]$.