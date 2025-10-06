---
title: 计算几何 W47 Voronoi Diagrams and 3D Convex Hull
author: tinsir888
date: 2023/11/23
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: e9babca3
---

# Voronoi Diagrams

$n$ points $p_1,\cdots,p_n$.

Nearest neighbor problem

举一个具象化的例子：

$n$ post offices

Find the closest post office

> For which region, point $p_i$ is the closest point?
>
> What is the region that $p_i$ servies?

（对于二维空间使用欧几里得距离定义的距离）两个最近点对连线线段的中垂线就是分界。

## Definition of Voronoi Diagrams

A set $P$ of $n$ points $p_1,\cdots,p_n$

decomposition of $\mathbb R^2$ into regions $R_1,\cdots,R_n$.

for any $q\in R_i$, $p_i$ is the closest point

## Theorems

**Cells of the Voronoi diagram of $P$ are convex.**

Proof:

> Consider $p_i$. Draw the bisector of $\overline{p_ip_j},j\neq i$ and let $h_j$ be the half space the contains the side of the bisector that includes $p_i$. The cell of $p_i$ is the intersection of all $h_j$'s.
>
> If not all points of $P$ lie on a line, the Voronoi diagram is made of straight line segments or rays and it is connected.
>
> - the cells are convex regions from the previous proof
> - the finite cells are polygons
> - if there is a full line $l$, then it implies that no bisector intersects $l$, which means all points lie on a line.

**The size of a planar Voronoi diagram is linear.**

Proof:

> $m$: number of edges in Voronoi graph, $I$: number of vertices in Voronoi graph, $H$: size infinite cell
>
> Euler's Formula: $V-E+R=2$
>
> For infinite edges:
>
> - put a bounding box，加入边界盒后的点数为 $V$，边数为 $E$，面数为 $R$.
> - $R=n+1$ one for each input point + outside
> - all vertices have degree three, except four special corner vertices
> - $2E=3(V-4)+4\times 2$
> - $E=4+H+m,V=4+H+I$
>
> 一顿推导，得出：
>
> $I=2n-2-H\le2n-5$
>
> $m=3n-H-3\le3n-6$

## Computing 2D Voronoi Diagrams

idea: sweep line

problem: the next point, can totally change everything above the sweep line.

we cannot assume we have compute the diagram above the sweep line correctly.

idea: define a region above the sweep line that we know is stable. Anything in this region is closer to $p$ than any point below the line i.e. cell of $c$ above the curve is stable.

sweep line: $Y=l$

curve: $P_i(l)=(X-a_i)^2+(Y-b_i)^2=(Y-l)^2$



Initially, all the Y-coordinates are the event points.

挨着向下扫描，在扫描线上方的点，两两之间用中垂线 bisector 分隔开，扫描线上方的点和扫描线用抛物线分隔开。这样得到的区域就是稳定的。

Other event points are when parabola segment disappear.

Can be found by checking three adjacent parabola segments.

We need a data structure for inserting and deleting parabola segments i.e., a balanced BST.



$O(n\log n)$ running time:

$\log n$ pere event points

$O(n)$ events points (since size of the diagram is linear)

## Generalization of Voronoi Diagrams

farthest point Voronoi diagram

higher order nearest neighbor

$k$-order: same set of $k$ nearest neighbors

different distance measure $L_\infty,L_1\cdots$

segments, curves etc.

distance based on shape enlarging

# Triangulations

Let $P$ be set of $n$ points in $\mathbb R^2$

**Theorem**: Any trangulation of $P$ has $2n-2-k$ triangles and $3n-3-k$ edges.

$k$ is number of edges on Convex Hull of $P$.

edge $e=\overline{pq}$ is illegal if $\min\alpha\lt\min\beta$.

Observation: Let $e$ be an illegaal edge of $\mathcal T$, let $\mathcal T'$ be triangulation obtained from $\mathcal T$ by flipping $e$, then $A(\mathcal T')\gt A(\mathcal T)$

Pseudo code for legal triangulation:

1. while $\mathcal T$ contains illegal edge $e=\overline{pq}$

   let $r$ and $s$ be the other points in the triangles incident to $e$

   remove $e$ and add $\overline{st}$

2. return $\mathcal T$

## The Delaunay Triangulation

Let $P$ be set of $n$ points in $\mathbb R^2$, let $\mathcal V(P)$ be the Voronoi diagram of $P$.

Let $\mathcal D(P)$ be the dual graph of $\mathcal V(P)$, $\mathcal D(P)$ is the Delaunay graph of $P$. 沃诺依图的对偶图是德劳内三角剖分

Theorem: $\mathcal D(P)$ is a plane graph

> Assume that $P$ is in geneal position: no four points cocircular, not three points colinear.

Theorem:

1. $p,q,r\in P$ are vertices of the same face of $\mathcal D(P)\Lrarr$ circle through $p,q,r$ contains no other point of $P$ in its interior.
2. $p,q\in P$ from an edge of $\mathcal D(P)\Lrarr$ there is a closed disk $C$ that contains $p$ and $q$ on its boundary and doesn't contain any other point of $P$



## Computing Delaunay Triangulation

using randomized incremental construction

as with point location, we maintain $\mathcal T$ and a point location data structure $\mathcal D$.



# 3D Convex Hull

convex hull ofo $n$ points in $3D$

composed of

- vertices
- edges
- faces

Complexity of a 3D CH is linear

>1. **假设边是“线”：** 在计算几何的上下文中，这里的“线”指的是 3D 凸包的边。   这些边形成凸包的边界。 
>2. **从放置在凸包顶点顶部的光源投射边缘的阴影：** 想象一下，您有一个光源直接位于凸包最高点（顶点）上方。   现在，将每条边的阴影投影到凸包下方的平坦表面上。   这类似于物体在光照射下投射的阴影。 
>3. **由于凸性，边缘的阴影不会交叉：** 因为根据定义，凸包是凸形状，因此当投影到平面上时，其边缘的阴影不会相交或相互交叉。   该特性是船体凸度的直接结果。 
>4. **由于我们是从不在凸包外部的点进行投影：** 投影是从凸包内部的点（具体来说，在最高顶点的顶部）完成的。   这确保了阴影投射在凸包内而不是外部。 

## Gift Wrapping for 3DCH

wrap a plane around the convex hull

1. project the points to 2D, find the 2D CH
2. an edge of 2D CH is a starting point
3. perform a DFS on the faces. (the face is marked so we don't visit it again)

$O(nh)$ time, $n$ is number of points, $h$ is output size.

## Randomized Incremental Algorithm for 3DCH

1. randomly shuffle the points
2. create the convex hull of the first four points
3. for $i=1,\cdots,n$ insert $p_i$

adding $p_i$:

1. faces visible from $p_i$ should be removed
2. new faces should be added, touching the boundary of the formerly visible faces

an edges is a boundary edge if out of two faces next to it one is visible but the other is not.

to find the visible faces

- for every point $p$ maintain a list of faces $l(p)$ visible from $p$
- for every face $f$ maintain a list of points $l(f)$ visible from $f$
- for every face $f\in l(p)$ place a point and back to the pointer $p\in l(f)$.



the points that can see $f$, $l(f)$ is a subset of points that can see $f_1$ and $f_2$, $l(f)\sub l(f_1)\cup l(f_2)$

如果一个点能同时看到两个相邻的面，那么这个点一定在这两个面延长面共同的上方。



if $F_i$ is the list of faces visible from $p_i$ then adding $p_i$ takes $\sum_{f\in F_i}|l(f)|$ time asymptotically

i.e. $O(\frac{n}{i})$ on average.

$O(n\log n)$ expected running time

