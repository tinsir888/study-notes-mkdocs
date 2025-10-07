---
title: 随机算法 13 Nearest Neighbor Search with Different Distance Metrics
author: tinsir888
date: 2024/5/6
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: 70458c40
---

# Recall

Recall the problem of **nearest neighbor search**: Randomized $c$-approximate $R$-near neighbor search. Recall that given a set $P$ of $n$ points in $\mathbb R^d$ and parameters $R\gt0,\delta\gt0$. Also given a distance function $dist:\mathbb R^d\times\mathbb R^d\rightarrow\mathbb R$. For a query point $q$, if there is an $R$-near neighbor $p$ of $q$ ($dist(p,q)\le R$), we must report some point $p'$ that is a $cR$-near neighbor of $q$ ($dist(p',q)\le cR$) with probability $1-\delta$.

**Sensitive Hash Functions**. Family of hash functions $\mathcal H$ to be $(R,cR,P_1,P_2)$-sensitive for $dist$ if

1. for all $p,q\in\mathbb R^d$ with $dist(p,q)\le R:\Pr_{h\sim\mathcal H}[h(p)=h(q)]\ge P_1$.
2. for all $p,q\in\mathbb R^d$ with $dist(p,q)\ge cR:\Pr_{h\sim\mathcal H}[h(p)=h(q)]\le P_2$.

**Solutions**. With query time $O(L(d+tk))$ and space $O(nd+Ln)$, where $L=O(n^{\ln P_1/\ln P_2})$, $t$ is the time to evaluate a hash function from $H$ and $k=\log_{P_2}(1/n)$.

**Hamming Distance**. Hash functions for Hamming distance: a uniform hash function $h$ in $\mathcal H$ chooses ==a uniform random bit position and returns the corresponding bit as the hash value.== $\mathcal H$ was $(R,cR,1-R/d,1-cR/d)$-sensitive and we proved that exponent $\rho=\ln P_1/\ln P_2$ satisfies $\rho\le 1/c$.

# Other Distance Measures

## l~1~-distance

For 2 points $p,q\in\mathbb R^d$, we have $\ell_1$-distance $\sum_{i=1}^d|p_i-q_i|$. Given the radius $R$ and approximation factor $c$, we can design a sensitive family of hash functions as follows: For a radius $w$ to be determined, consider the family:
$$
\mathcal H=\left\{h_{i,o}(x)=\left\lfloor\frac{x_i-o}{w}\right\rfloor| i\in\{1,\cdots,d\},o\in[0,w)\right\}
$$
To get a hash function from $\mathcal H$, a coordinate $i$ and offset $o$ are drawn uniformly and independently.

The hash value of the point $x$ is $\lfloor(x_i-o)/w\rfloor$.

Consider $i$ and $h_{i,o}$ has been chosen, but $o$ is still uniform random in $[0,w)$. For points $p,q$, $h_{i,o}(p)$ and $h_{i,o}(q)$ equal precisely when $\lfloor(p_i-o)/w\rfloor=\lfloor(q_i-o)/w\rfloor$. The expression $\lfloor(x-o_i)/w\rfloor$ changes value precisely at points $x=o_i+jw$ for $j\in\mathbb Z$.

What's the probability that a point of the form $o_i+jw$ lies between $p_i$ and $q_i$?

- If $|p_i-q_i|\ge w$, then this happens with probability $1$.
- Otherwise, the probability is $|p_i-q_i|/w$.

Thus we have $\Pr[\lfloor(p_i-o_i)/w\rfloor=\lfloor(q_i-o_i)/w\rfloor]=\max\{0,1-|p_i-q_i|/w\}$.

Since $i$ is chosen uniformly, we get
$$
\Pr_{h\sim\mathcal H}[h(p)=h(q)]=\sum_{i=1}^d\frac{1}{d}\max\{0,1-|p_i-q_i|/w\}.
$$
Let us first assume $dist(p,q)\le R$. If we choose $w\ge R$, then the above simplifies to
$$
\Pr_{h\sim\mathcal H}[h(p)=h(q)]=\sum_{i=1}^d\frac1d\cdot(1-|p_i-q_i|/w)\\
=1-\frac{\sum_{i=1}^d|p_i-q_i|}{dw}=1-\frac{dist(p,q)}{dw}\ge1-\frac{R}{dw}
$$
On the other hand, assume $dist(p,q)\gt cR$ and assume we choose $w\ge cR$. Then we get:
$$
\Pr_{h\sim\mathcal H}[h(p)=h(q)]=\sum_{i=1}^d\frac{1}{d}\max\{0,1-|p_i-q_i|/w\}\\
=\sum_{i=1}^d\frac{1}{d}\cdot(1-\frac{\min\{w,|p_i-q_i|\}}{w})\\
=1-\frac{\sum_{i=1}^d\min\{w,|p_i-q_i|\}}{dw}.
$$
Assume first that $\forall i:|p_i-q_i|\le w$. Then the above equals
$$
1-\frac{\sum_{i=1}^d|p_i-q_i|}{dw}=1-\frac{dist(p,q)}{dw}\le1-\frac{cR}{dw}
$$
On the other hand, assume $\exists i:|p_i-q_i|\gt w$, then the above has upper bound:
$$
\Pr_{h\sim\mathcal H}[h(p)=h(q)]=1-\frac{\sum_{i=1}^d\min\{w,|p_i-q_i|\}}{dw}\\
\le 1-\frac{w}{dw}\le1-\frac{cR}{dw}
$$
Thus $\mathcal H$ is $(R,cR.1-R/(dw),1-cR/(dw))$-sensitive if we choose $w\ge cR$. This results in $L=O(n^\rho)$ for
$$
\rho=\frac{\ln P_1}{\ln P_2}=\frac{\ln(1-R/(dw))}{\ln(1-cR/(dw))}\le\frac{\ln(1-R/(dw))}{\ln(1-R/(dw))^c}=\frac{1}{c}
$$
We also have $k=\log_{P_2}(1/n)=\frac{\ln(1/n)}{\ln(1-cR/(dw))}$. By $1-x\le\exp(-x)$, we have
$$
k=\log_{P_2}(1/n)=\frac{\ln(1/n)}{\ln(1-cR/(dw))}\le\frac{\ln(1/n)}{-cR/(dw)}=dw\ln n/(cR).
$$

## Set Similarity Search

Consider the data consists of sets rather than points.

Input consist of $n$ sets $S_1,\cdots,S_n$, all subsets of a universe $U$.

Measure for set similarity: Jaccard coefficient
$$
J(A,B)=\frac{|A\cap B|}{|A\cup B|}
$$
The coefficient lies between $0$ and $1$ with $J(A,B)=1$ when $A$ and $B$ are identical and $J(A,B)=0$ when $A$ and $B$ are disjoint.

The Nearest Neighbor Queries on sets: Given a set $C$, find the set $S_i$ in the data that is most similar to $C$.

Goal: Use the LSH. For LSH we need to distance metric. Which is the opposite of Jaccard coefficient.
$$
dist_J(A,B)=1-J(A,B)
$$
How to define LSH function for this distance metric? Use *min-wise independent* families of hash function. A family of hash functions $\mathcal H:U\rightarrow\mathbb R$ is **min-wise independent** if $\forall x\in U,S\subset U$ with $x\notin S$, it holds that
$$
\Pr_{h\sim\mathcal H}[h(x)\lt\min_{y\in S}h(y)]=\frac{1}{|S|+1}.
$$
The above definition means that in any set, each element has exactly the same probability of receiving the smallest hash value. We assume that any $h\in\mathcal H$ returns distinct values for all $x\in U$ i.e. $h(x)\neq h(y)$ for any $x\neq y$ and any $h\in\mathcal H$.

Assume that we have a min-wise independent family of hash functions $\mathcal H$ and define from it a new family of hash function $\mathcal G_\mathcal H\subset 2^U\rightarrow U:$
$$
\mathcal G_\mathcal H=\{g_h(A)=\arg\min_{x\in A}h(x)|h\in\mathcal H\}.
$$

This definition needs a bit of explanation. The notation $2^U$ refers to all subsets of the universe $U$. So $\mathcal G_\mathcal H$ consists of functions that map sets $A\subseteq U$ to hash values in $U$.

The family $\mathcal G_\mathcal H$ has one function $g_h$ for each function $h\in\mathcal H$. To evaluate $g_h(A)$, we compute the element $x\in A$ receiving the smallest hash value when using $h$, and we return that element $x$ as the hash value of $A$. Simply speaking, $g_h(A)$ returns the element in $A$ with smallest hash value according to $h$,

Is $\mathcal G_\mathcal H$ sensitive w.r.t. $dist_J$? Consider 2 sets $A,B$ and let $x^*=\arg\min_{x\in A\cup B}h(x)$ be the element with smallest hash value in the union $A\cup B$. Observe that $g_h(A)=g_h(B)$ iff $x^*\in A\cap B$. Now define for every $x\in A\cap B$ the event $E_x$ s.t. $h(x)\lt\min_{g\in(A\cup B)\diagdown\{x\}}h(y)$, i.e., $E_x$ is the event that $x$ has the smallest hash value among all elements in $A\cup B$, we have
$$
\Pr_{g_h\sim\mathcal G_\mathcal H}[g_h(A)=g_h(B)]=\Pr_{h\sim H}[\arg\min_{x\in A\cup B}h(x)\in A\cap B]\\
=\Pr_{h\sim\mathcal H}[\bigcup_{x\in A\cap B}E_x]
$$
The events $E_x$ are disjoint. Therefore
$$
\Pr_{h\sim\mathcal H}[\bigcup_{x\in A\cap B}E_x]=\sum_{x\in A\cap B}\Pr_{h\sim\mathcal H}[E_x]\\
=\sum_{x\in A\cap B}\frac{1}{|(A\cup B)\diagdown\{x\}|+1}\\
=\frac{|A\cap B|}{|A\cup B|}=J(A,B)
$$
Now we bound $P_1,P_2$.

- For $P_1$. Let $A,B$ have $dist_J(A,B)\le R$. We see that $R\ge dist_J(A,B)=1-J(A,B)=1-\Pr_{g_h\sim\mathcal G_\mathcal H}[g_h(A)=g_h(B)]$, implying $\Pr_{g_h\sim \mathcal G_\mathcal H}[g_h(A)=g_h(B)]\ge1-R$.

- For $P_2$. Next let $A,B$ have $dist_J(A,B)\gt cR$. Then we have $cR\lt dist_J(A,B)=1-J(A,B)=1-\Pr_{g_h\sim \mathcal G_\mathcal H}[g_h(A)=g_h(B)]$ implying $\Pr_{g_h\sim \mathcal G_\mathcal H}[g_h(A)=g_h(B)]\lt 1-cR$.

Thus the family $\mathcal G_\mathcal H$ is thus $(R,cR,1-R,1-cR)$-sensitive.

Then we bound $\rho$ in $L=O(n^\rho)$:
$$
\rho=\ln P_1/\ln P_2=\ln(1-R)/\ln(1-cR)\le\ln(1-R)/\ln(1-R)^c=1/c.
$$
Then we bound $k$ as $k=\ln_{P_2}(1/n)=\ln(1/n)/\ln(1-cR)$. Analogously,
$$
k\le\ln(1/n)/\ln(\exp(-cR))=\ln n/(cR).
$$

### Constructing Min-Wise Independent Families

All of above assumed that we had a min-wise independent family of hash functions $\mathcal H$. Unfortunately it can be proved that such families of hash functions are impossible to construct using a small random seed. However, we can still find efficient families that are only approximately min-wise independent.

It suffices to get reasonable nearest neighbor search structures.

We say that a family of hash function $\mathcal H$ is $\epsilon$**-approximate min-wise independent**, if for any $x\in U$ and any $S\subset U$ with $x\notin S$, we have
$$
\Pr_{h\sim\mathcal H}[h(x)\lt\min_{y\in S}h(y)]\in\frac{1\pm\epsilon}{|S|+1}
$$
Indyk showed that any $O(\ln(1/\epsilon))$-wise independent family of hash functions is also $\epsilon$-approximate min-wise independent. A classic example of $k$-wise independent family of hash function is the following for any prime $p\gt U$:
$$
\mathcal H=\left\{ h_{a_{k-1},\cdots,a_0}(x)=\sum_{i=1}^{k-1}a_ix^i\text{ mod }p|a_0,\cdots,a_{k-1}\in[p] \right\}.
$$
Someone proved that the simple tabulation hash function is $\epsilon$-approximate min-wise independent with $\epsilon=O(\lg^2n/n^{1/c})$ where $c$ is number of tables used for the hash function and $n$ is the cardinality of the set $S$. ==So the quality depends on the set cardinalities and therefore simple tabulation hashing works best for rather large sets.==

How the approximation affects the sensitivity of the hash function:
$$
\Pr_{g_h\sim\mathcal G_\mathcal H}[g_h(A)=g_h(B)]=\sum_{x\in A\cap B}\Pr_{h\sim\mathcal H}[E_x]\\
\le\sum_{x\in A\cap B}\frac{1+\epsilon}{|(A\cup B)\diagdown\{x\}|+1}\\
=(1+\epsilon)\cdot\frac{|A\cap B|}{|A\cup B|}\\
=(1+\epsilon)\cdot J(A,B).
$$
Therefore, for $dist_J(A,B)\gt cR$, we have 
$$
\Pr_{g_h\sim\mathcal G_\mathcal H}[g_h(A)=g_h(B)]\\
\le
(1+\epsilon)J(A,B)\\
\le(1+\epsilon)(1-dist_J(A,B))\\
\le(1+\epsilon)(1-cR).
$$
Analogously, we also have
$$
\Pr_{g_h\sim\mathcal G_\mathcal H}[g_h(A)=g_h(B)]=\sum_{x\in A\cap B}\Pr_{h\sim\mathcal H}[E_x]\\
\ge\sum_{x\in A\cap B}\frac{1-\epsilon}{|(A\cup B)\diagdown\{x\}|+1}\\
=(1-\epsilon)\cdot\frac{|A\cap B|}{|A\cup B|}\\
=(1-\epsilon)\cdot J(A,B).
$$
This implies that for $dist_J(A,B)\le R$, we have
$$
\Pr_{g_h\sim\mathcal G_\mathcal H}[g_h(A)=g_h(B)]\\
\ge(1-\epsilon)J(A,B)\\
=(1-\epsilon)(1-dist_J(A,B))\\
\ge(1-\epsilon)(1-R).
$$
Thus $\mathcal G_\mathcal H$ is $(R,cR,(1-R)(1-\epsilon),(1-cR)(1+\epsilon))$-sensitive.

## l~2~-distance

$$
dist(p,q)=\sqrt{\sum_{i=1}^d(p_i-q_i)^2}
$$

Math Math Math!🤯

Let $w$ be a parameter to be fixed and consider the following family of hash functions
$$
\mathcal H=\left\{h_{o,u}(x)=\lfloor\frac{\langle x,u\rangle-o}{w}\rfloor|o\in[0,w),u\in\mathbb R^d\right\}.
$$
We simply pick $o$ uniformly in $[0,w)$ and pick vector $u$ s.t. each coordinate is independently $\mathcal N(0,1)$ distributed.

Consider two points $p,q$ and two fixed values of $\langle p,u\rangle$ and $\langle q,u\rangle$ (Once $u$ is chosen but $o$ is still uniform random). Similar to $\ell_1$-distance, we get
$$
\lfloor\frac{\langle p,u\rangle-o}{w}\rfloor=\lfloor\frac{\langle q,u\rangle-o}{w}\rfloor
$$
with probability $\max\{0,1-|\langle p-q,u\rangle|/w\}$.

Then consider the distribution of $\langle p-q,u\rangle$:
$$
\langle p-q,u\rangle=\sum_{i=1}^d(p_i-q_i)u_i\sim\mathcal N(0,\sum_i(p_i-q_i)^2)\\
\sim\mathcal N(0,dist(p,q)^2)\sim dist(p,q)\cdot\mathcal N(0,1)\\
\Rightarrow \langle p-q,u\rangle/w\sim (dist(p,q)/w)\cdot\mathcal N(0,1)\\
$$
By probability density function $f$ or $\mathcal N(0,1)$:
$$
\Pr[h(p)=h(q)]=\int_{-\infty}^\infty\frac{1}{\sqrt{2\pi}}\exp(-x^2/2)\cdot\max\{0,1-(dist(p,q)/w)|x|\}dx\\
\ge\Pr[h(p)=h(q)]=\int_{-\infty}^\infty\frac{1}{\sqrt{2\pi}}\exp(-x^2/2)\cdot(1-(dist(p,q)/w)|x|)dx\\
=\int_{-\infty}^\infty\frac{1}{\sqrt{2\pi}}\exp(-x^2/2)dx-\int_{-\infty}^\infty\frac{1}{\sqrt{2\pi}}\exp(-x^2/2)(dist(p,q)/w)|x|dx.
$$
By symmetry and integral of $f$ is $1$, the upper quantity
$$
\ge1-2\cdot(dist(p,q)/w)\cdot\frac{1}{\sqrt{2\pi}}\cdot\int_0^\infty\exp(-x^2/2)xdx\\
\ge1-2\cdot(dist(p,q)/w)\cdot\frac{1}{\sqrt{2\pi}}
$$
**For $p,q$ with dist less than $R$**, this is at least $1-\sqrt{2/\pi}(R/w)$.

For upper bound on $\Pr[h(p)=h(q)]$:
$$
\Pr[h(p)=h(q)]=\int_{-\infty}^\infty\frac{1}{\sqrt{2\pi}}\exp(-x^2/2)\cdot\max\{0,1-(dist(p,q)/w)|x|\}dx\\
\le\int_{-\infty}^\infty\frac{1}{\sqrt{2\pi}}\exp(-x^2/2)\cdot(1-(dist(p,q)/w)|x|)dx\\
+2\cdot\int_{w/dist(p,q)}^\infty\frac{1}{\sqrt{2\pi}}\exp(-x^2/2)\cdot(dist(p,q)/w)xdx\\
=\cdots\cdots\\
=1-\sqrt{2/\pi}(dist(p,q)/w)(1-e^{-(w/dist(p,q))^2/2}).
$$
Assume $w\gg dist(p,q)$ and ignore $(1-e^{-(w/dist(p,q))^2/2})$. Under this simplification, we get for $p,q$ with $dist(p,q)\gt cR$ that $\Pr[h(p)=h(q)]\le1-\sqrt{2/\pi}(cR/w)$.

The we bound $\rho$ in $L=O(n^\rho)$:
$$
\rho=\ln P_1/\ln P_2=\ln(1-\sqrt{2/\pi}(R/w))/\ln(1-\sqrt{2/\pi}(cR/w))\\
\le\ln(1-\sqrt{2/\pi}(R/w))/\ln(1-\sqrt{2/\pi}(R/w))^c=1/c
$$
If we don't ignore $(1-e^{-(w/dist(p,q))^2/2})$, then
$$
\rho\le\frac{1}{c(1-e^{-(w/dist(p,q))^2/2})}
$$
By choosing $w$, we need to estimate on the largest value of $dist(p,q)$.



Then we bound $k$.

$P_2=1-\sqrt{2/\pi}(cR/w)$, ignoring $(1-e^{-(w/dist(p,q))^2/2})$.

Be have $k=\log_{P_2}(1/n)=\ln(1/n)/\ln P_2=\ln(1/n)/\ln(1-\sqrt{2/\pi}(cR/w))$.
$$
k\le\ln(1/n)/\ln(e^{-(w/dist(p,q))^2/2})=\ln n/(\sqrt{2/\pi}cR/w)=O(w\ln n/(cR))
$$
This means we should be careful not to choose $w$ too large.