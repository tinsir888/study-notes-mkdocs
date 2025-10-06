---
title: 计算几何 W39 Introduction to Computational Geometry
author: tinsir888
date: 2023/9/28
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: a44185d3
---

# 阅读材料

##  Classroom examples of robustness problems in geometric computations

by Lutz Kettner et al.

substituting floating point arithmetic for the assumed real arithmetic may cause implementation to fail.

*focus on floating point - real numbers issue*

IEEE 754

# 课堂笔记

## Computational Geometry

algorithms that deal with geometric data

- points
- lines
- circles
- hyperplanes

applications

- graphic
- databases
- geographical information system
- robotics

one example:

a file contains n lines where the i-th line contains two numbers $x_i$ and $y_i$, describing a point $p_i=(x_i,y_i)$.

find all the points contained inside a rectangle $[l,r]\times[b,t]$

## Overview

d dimensional point $(x_1,\cdots,x_d)$

d-dim line: $a_0+a_1X_1+a_2X_2+\cdots+a_{d-1}X_{d-1}=X_d$

other useful representations:

- curve $(f_1(t),f_2(t),\cdots,f_d(t))$
- line: $f_i$ is a linear function of $t$, i.e. $f_i(t)=a_it+b_i$.
- line segment or curve segment $t\in[0,1]$
- algebraic, semi-algebraic 代数，半代数
- sphere: $\sum_i(X_i-c_i)^2=r^2$

## Geometric Predicates

Point P, Line l (above, below, on)

how to judge whether 3 points $A,B,C$ are in the same line?

> check direction of  vector $AB$ and $AC$: $AB\cdot AC$
> $$
> \det
> \left [ \begin{matrix}
> x_1 &y_1 & 1 \\
> x_2 &y_2 & 1  \\
> x_3 &y_3 & 1 \\
> \end{matrix} \right ]\\
> right\ turn\ if \det\lt0\\
> left\ turn\ if \det\gt0\\
> $$

### Line Segments

whether line $l_1$ and line segment $s$ have intersect.

> if (q1 is on l1) or (q2 is on l1), then Tangent
>
> else if ((q1 is below l1) and (q2 is below l1)) then below
>
> else if((q1 is above l1) and (q2 is above l1)) then above
>
> else intersecting

for 2 line segments s1 and s2:

> if ((l1 intersects q) and (l2 intersects p)) then intersecting
>
> else non intersecting

## Reminder: Real RAM

- real RAM

  - a RAM equipped with real registers

  - a real register can store any real number

  - basic arithmetic operations on real registers take O(1) time

    i.e. we can compute sqrt of a number upto infinite precision in O(1) time

### floating point issues in practice

- floating point operations are imprecise
- imprecision can create impossible scenarios, infinite loops etc.
- different operations can suffer differently from floating point issues
- smarter coding can help
- libraries available e.g. CGAL multi-precision floating point numbers

## Convex Hull Algorithms

problem: compute the convex hull of a given set of n points in the plane.

a convex polygon, composed of vertices and edges

an upper hull + a lower full

### Definition of convex hull

a shape without bumps

i.e. for any 2 points p, q in a convex hull, the line segment pq is inside the convex hull.

### Graham's Scan

compute the upper hull

1. sort by x coordinate
2. initialize UH to empty, UH.add(p1,p2); s=2
3. for i = 3 to n do
   - while s>=2 and UH(s-1), UH(s), pi make a left turn
     - remove UH(s); s--;
   - UH.add(pi); s++;

**every point is removed at most once at step remove UH(s); s-- and add exactly once at UH.add(pi).**

O(n) time for step 3.

### Divide and Conquer

1. find the median x coordinate and partition $O(n)$
2. recurse on left & right $f(\frac{n}{2})$
3. merge $O(n)$

how to find the upper common tangent?

设凸多边形A和B的上外公切线为（Ai,Bj），切点分别为Ai和Bj，可知：A和B均在上外公切线（Ai,Bj）的右侧。进一步，“凸多边形A在（Ai,Bj）的右侧”等价于“Ai_next和Ai_pre均在（Ai,Bj）的右侧（或其中一个在右侧，另一个共线）”，简单起见，定义Ai_next为“Ai的next方向的顶点中第一个不与（Ai,Bj）共线的顶点”，同理Ai_pre，Bj_next，Bj_pre。“（Ai,Bj）是凸多边形A和B的上外公切线”这一命题等价于“Ai_next，Ai_pre，Bj_next，Bj_pre均在（Ai,Bj）的下方”。 

> add one by one, using graham's scan
>
> from a single point:
>
> - $O(\log n)$ time binary search.
>
> from a chain:
>
> - $O(\log^2n)$ time binary search using previous
> - $O(\log n)$ time (more careful approach)

total time complexity: $f(n)=2f(\frac{n}{2})+O(n)=O(n\log n)$

## Gift Wrapping (Jarvis March)

1. Initialize:

   p = the left most point

   r: an upward ray from p

   CH.add(p)

2. Repeat:

   1. Rotate r clockwise around p until $O(n)$

      the first point q is hit

   2. CH.add(q)

   3. p=q

   until p equals the left most point $O(h)$ times

running time: $O(n\times h)$

