---
title: 计算几何 W41 Polygon Triangulation
author: tinsir888
date: 2023/10/12
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: ec2f698
---

# DCEL

## Definition

a planar subdivision in a complex structure 平面剖分

components:

- vertices
- faces
- edges

store two copies of each edge (opposite directions)

- one copy of edge for each adjacent face
- the adjacent face is always to the right
- edges are clockwise around each face 很巧妙，所有面都有一个顺时针的环构成

an edge points to twin, adjacent face, start and end points `e.twin,e.face,e.start,e.end` twin 指的是相同端点，方向相反的边

edge around a face are in a linked-list

each vertex points to an out-going adjacent edge

each face points to an adjacent edge

# Line Segment Intersection

input: a set of n line segments

output: list of all intersections between the line segments. output has size k

idea: a sweep line algorithm i.e. insert the segments one by one from top to bottom.

the sweep line stops at **event points**

3 types of event points

1. top of a segment
2. bottom of a segment
3. intersection point

2 data structure are required

1. list of event points - priority queue

   allows insertion and deletion of events

2. status of sweep line

   store the ordered list of segments intersecting the sweep line

## The Algorithm

扫线算法

`SweepLine(P)`: P is a set of n line segments

1. Init

   `l.y=inf` //sweep line coordinate

   `Q=null` //priority queue

   sort P and add end points to Q as Top or Bottom events

   init. `l` to null //status of the line

2. while Q is not empty

   1. `E=Q.getNextEvent()`

   2. if `E` is top event of `s`, `l.insert(s)`

      add int. points of `l.prev(s)` and `s`, and `l.next(s)` and `s` to `Q`.

   3. if `E` is bottom event of `s`

      add int. points of `l.prev(s)` and `l.next(s)` to `Q` then remove `s` from `l`.

   4. if `E` is int event of `s1`, `s2`

      `l.swap(s1,s2)` then add int. points of `l.prev(s2)`, `s2`, and `l.next(s1)`, `s1` to `Q`

## Sweep Line Status

a  data structure

- maintain order
- find
- insert
- delete
- swap order

solution: an abstract balanced binary search tree

SweepLine(P ): P is a set of n line segments

1. Initialization:
   • .y ← +∞ // sweep line coordinate
   • Q ← ∅ // priority queue
   • Sort P and add end points to Q as Top or Bottom events
   • Init. to ∅ // status of the line.

2. While Q is not empty

   2.1 E ← Q.getNextEvent()

   2.2 If E is top event of s, .insert(s)

   ​	Add int. points of .prev(s) and s, and l.next(s) and s to Q

   2.3 If E is bottom event of s

   ​	Add int. points of l.prev(s) and l.next(s) to Q then remove s from l

   2.4 If E is int event of s1, s2, l.swap(s1, s2) then add int. points of l.prev(s2), s2, and l.next(s1), s1 to Q

扫线算法的中文解释：

两个数据结构：一条链表，负责记录所有线段的端点和已经找到的焦点，每个点按 y 递减顺序排列。一颗二叉树，负责记录与扫描线段相交的线段（的端点），每条线段按照上端点的 x 坐标递增。

开始扫描！

扫描到一个新点是某线段的上端点：将改点存入二叉树中，在树中找到该端点对应线段的左邻居线段和右邻居线段，检查该线段是否与左右邻居分别相交，如果相交就把端点存入链表。

扫描到一个新点是某线段的下端点：在二叉树中找到该线段的上端点，找到上端点的左右邻居，检查左右另据是否相交。如果有交点，存入链表。

扫描到一个交点：输出这个交点的坐标，找到左线段的左邻居，检查左线段是否和左邻居相交；找到右线段的右邻居，检查右线段是否与右邻居相交。如果有交点就存入链表。

时间复杂度 $O((n+k)\log n)$ n 是线段树，k 是交点数。

# Polygon Traingulation

多边形的三角剖分

a polygon $P$ with $n$ vertices

trangulation: decomposition of $P$ into triangels

it is fundamental proprocessing step.

一个多边形的三角剖分可能有多解

a simple polygon $P$ must satisfy:

1. no self-intersections
2. no holes

output: a decomposition into trangles using DCEL, or just a list of triangles

## claim

1. It's always possible.

   通过归纳法 induction 证明，如果连线了，那么分成的两个部分的其他点的个数是 1 到 n-2

   given a single polygon P with >3 vertices, there exist two non-adjacent vertices, v, w, s.t. vw is inside P and does not intersect the boundary of P.

   let u be the leftmost point, both neighbors of  are to the right of u. 

   - case 1 vw does not intersect boundary of P.

   - case 2 vw intersects boundary of P.

     there exist at least vertices of P inside uvw.

     solution: let x be the vertex be the farthest away from vw. no vertex inside trinangle uv'w' (v'w' 平行于 vw 并且过 x 点), line px doesnot intersect P and cuts it into to polygons of smallersize.

2. number of triangles is $n-2$.

## Algorithm for Monotone Polygon Triangulation

trivial algorithm: $O(n^2)$

a Y-monotone polygon: every horizontal line intersects at most two edges of P

claim: a Y-monotone polygon can be triangulated in $O(n)$ time.

We can triangulate by recursively adding diagonals:

1. Take the highest leftmost vertex v .
2. Try first to connect the neighbors u and w.
3. If u and w cannot be connected, connect v to the vertex farthest from the edge (u, w) inside the triangle spanned by u, v , and w.

