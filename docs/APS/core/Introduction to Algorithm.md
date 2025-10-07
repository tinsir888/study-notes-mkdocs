---
title: APS 重点复习课程之算法导论
author: tinsir888
date: 2022/10/20
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 算法
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: efc05345
---

# Overview

Mathematical Way to Evaluate Performance of Algorithm

- Time Complexity
- Space Complexity

Brute-force

Dynamic Programming

- Longest Common Subsequence

Greedy

- Hoffman Coding
- Minimum Spanning Tree (MST)
  - Prim
  - Kruskal

Graph

- Shortest Path

  - Floyd (All Pairs Shortest Paths)

  - Bellman-Ford (Single Pair)
  - Dijkstra (Single Pair)
- Top Sort
- Strong Connected Components (SCC)
  - Korsaraju
  - Tarjan
- Maximum Flow Problem
  - Dinic
  - Max Flow Min Cost: Linear Programming

Number Theory
- Greatest Common Devisor
- Lucas

String

- Knuth Morris Pratt (KMP)

NP Complete

# Mathematical Way to Evaluate Performance of Algorithm

## Time Complexity

Time complexity is the amount of computational effort required to execute the algorithm.

## The method of analyzing the time complexity

### For Loop

3 Loops of matrix multiply:

```c++
const int N=/**/;
int C[N][N];
int Matrix_mul(int A[][],int B[][],int n){
    for(int i=1; i<=n; i++)
        for(int j=1; j<=n; j++){
            C[i][j]=0;
            for(int k=1; k<=n; k++)
                C[i][k]=C[i][k]+A[i][k]*B[k][j];
        }
}
```

$T(n)=\sum_{i=1}^n\sum_{j=1}^n\sum_{k=1}^n1$

$=\sum_{i=1}^n\sum_{j=1}^nn$

$=\sum_{i=1}^nn^2$

$=n^3=\Theta(n^3)$

### For Recursion

Computing factorial of n.

```c++
template<typename T>
T factorial(T n){
    if(n==0)
        return 1;
    else
        return n*factorial(n-1);
}
```

According to the recurrence formula:

$T(n)=1+T(n-1)=1+1+T(n-2)=\cdots$

$=1+1+\cdots+1=n=\Theta(n)$

## Space Complexity

Space complexity is the memory space required to execute the algorithm.

# Brute-force

aka generate and test, very general

Implementation costs are proportional to the number of candidate  solutions – which in many practical problems tends to grow very quickly  as the size of the problem increases.

Typically used when the problem size is limited

# Dynamic Programming

Simplifying a complicated problem by breaking it down into simpler sub-problems in a recursive manner.

## Longest Common Subsequence

Sequence X and Y.

Let $f[i][j]$ be the length of LCS of X first-i subsequence and Y first-j subsequence. 

```c++
int LCS(string X, string Y){
    //the max len of x is m, max len of y is n
    int f[m][n];
    for(int i=0; i<m; i++)
        for(int j=0; j<n; j++)
            if (X[i] = Y[j])
                f[i][j] = f[i-1][j-1] + 1;
            else
                f[i][j] = max(f[i][j-1], f[i-1][j]);
    return f[m][n];
}
```

Time complexity: $\Theta(m*n)$, space complexity: $\Theta(m*n)$.

# Greedy

## Hoffman Coding

See in [note of  Data Structure](https://tinsir888.github.io/posts/dccc524f.html).

## Minimum Spanning Tree (MST)

A subset of the edges of a connected, edge-weighted undirected graph that connects all the vertices together, without any cycles and with the minimum possible total edge weight.

### Prim

（每趟选一个点）

Time Complexity: (depends on how to store graph)

Adjacent matrix: $\Theta(|V|^2)$

Adjacent list: $\Theta(|E|\log|V|)$

Step:

1. Initialize a tree with a single, chosen arbitrarily from the graph.
2. Grow the tree by one edge: of the edges that connect the tree to vertices not yet in the tree, find the minimum-weight edge, and transfer it to the tree.
3. Repeat step 2 until all vertices are in the tree.

### Kruskal

Kruskal's Algorithm = Greedy + disjoint sets

（每趟选一个边）

For a graph with *E* edges and *V* vertices, Kruskal's algorithm can be shown to run in $\Theta(E\log E)$ time, or equivalently, $\Theta(E\log V)$ time, all with simple data structures.

Step:

1. create a forest F (a set of trees), where each vertex in the graph is a separate Tree
2. create a sorted set containing all the edges in the graph
3. while S is nonempty and F is not yet spanning
   - remove an edge with minimum weight from S
   - if the removed edge connects two different trees then add it to the forest F, combining two trees into a single tree

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>

using namespace std;

typedef pair<int, int> Edge;

// Union-Find data structure
class UnionFind {
public:
  vector<int> parent;
  vector<int> size;

  UnionFind(int n) {
    parent.resize(n);
    size.resize(n);
    iota(parent.begin(), parent.end(), 0);
    fill(size.begin(), size.end(), 1);
  }

  int find(int x) {// disjoint set
    if (x == parent[x]) return x;
    return parent[x] = find(parent[x]);
  }

  void unite(int x, int y) {
    x = find(x);
    y = find(y);
    if (x == y) return;
    if (size[x] < size[y]) swap(x, y);
    parent[y] = x;
    size[x] += size[y];
  }

  bool same(int x, int y) {
    return find(x) == find(y);
  }

  int getSize(int x) {
    return size[find(x)];
  }
};

// Kruskal's algorithm
int kruskal(vector<Edge> edges, int n) {
  UnionFind uf(n);
  sort(edges.begin(), edges.end());

  int res = 0;
  for (Edge e : edges) {
    int u = e.first, v = e.second;
    if (!uf.same(u, v)) {
      uf.unite(u, v);
      res += e.first;
    }
  }

  return res;
}

int main() {
  // example graph from the Wikipedia page
  vector<Edge> edges = {
    {7, 1}, {8, 2}, {6, 3}, {5, 4},
    {9, 4}, {2, 5}, {2, 6}, {5, 6},
    {3, 7}, {3, 8}, {4, 8}, {4, 9}
  };
  int n = 9;

  cout << kruskal(edges, n) << endl;  // expect 31

  return 0;
}
```

# Graph

## Shortest Path

Finding a path between two vertices (or nodes) in a graph such that the  sum of the weights of its constituent edges is minimized.

### Floyd (All Pairs Shortest Paths)

```c++
#include <iostream>
#include <limits.h>
#include <stdio.h>

const int MAX = 100;

int min(int a, int b) {
  return a < b ? a : b;
}

void floyd(int distances[][MAX], int n) {
  for (int k = 0; k < n; k++) {
    for (int i = 0; i < n; i++) {
      for (int j = 0; j < n; j++) {
        distances[i][j] = min(distances[i][j], distances[i][k] + distances[k][j]);
      }
    }
  }
}

int main() {
  int n;
  int distances[MAX][MAX];

  // Read the number of vertices and the distances between them
  std::cin >> n;
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
      std::cin >> distances[i][j];
    }
  }

  // Run the Floyd algorithm to compute the shortest distances
  floyd(distances, n);

  // Print the results
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
      std::cout << distances[i][j] << " ";
    }
    std::cout << std::endl;
  }
  return 0;
}

```

The time complexity of the Floyd algorithm is $O(V^3)$, where V is the number of vertices in the graph.

### Bellman-Ford (Single Pair)

#### Code

```c++
#include <iostream>
#include <limits.h>
#include <vector>

const int MAX = 100;

int min(int a, int b) {
  return a < b ? a : b;
}

void bellman_ford(std::vector<std::pair<int, int>> adjacency_list[MAX], int n, int source) {
  int d[MAX];
  for (int i = 0; i < n; i++) {
    d[i] = INT_MAX;
  }
  d[source] = 0;

  for (int i = 0; i < n - 1; i++) {
    for (int u = 0; u < n; u++) {
      for (const auto& [v, w] : adjacency_list[u]) {
        if (d[u] != INT_MAX) {
          d[v] = min(d[v], d[u] + w);
        }
      }
    }
  }

  // Print the results
  for (int i = 0; i < n; i++) {
    std::cout << d[i] << " ";
  }
  std::cout << std::endl;
}

int main() {
  int n;
  std::vector<std::pair<int, int>> adjacency_list[MAX];
  int source;

  // Read the number of vertices, the edges, and the source vertex
  std::cin >> n;
  while (true) {
    int u, v, w;
    std::cin >> u >> v >> w;
    if (u == -1) break;
    adjacency_list[u].push_back({v, w});
  }
  std::cin >> source;

  // Run the Bellman-Ford algorithm to compute the shortest distances
  bellman_ford(adjacency_list, n, source);

  return 0;
}

```

#### Process

At each iteration, the algorithm considers all of the edges in the graph and updates the distances to each vertex if a shorter path is found. The algorithm continues until all of the edges have been relaxed a number of times equal to the number of vertices in the graph.

Here is a more detailed description of the Bellman-Ford algorithm:

1. Initialize the distances to all vertices to infinity, except for the source vertex, which has a distance of 0.

2. For each iteration of the outer loop:

   - For each edge in the graph:

     If the distance to the destination vertex of the edge is greater than the distance to the source vertex plus the weight of the edge, update the distance to the destination vertex.

3. If any of the distances to the vertices have been updated in the previous step, repeat the outer loop.

The algorithm terminates when all of the edges have been relaxed a number of times equal to the number of vertices in the graph, or when none of the distances are updated in the previous step. This ensures that the distances to all of the vertices are correct and that the shortest paths have been found.

#### Time Complexity

The time complexity of the Bellman-Ford algorithm using an adjacent list to store the graph is $O(VE)$, where V is the number of vertices and E is the number of edges in the graph.

### Dijkstra (Single Pair)

#### Code

```c++
#include <bits/stdc++.h>
using namespace std;
// Dijkstra algorithm to find the shortest path from
// the source vertex to the destination vertex
// in a graph with non-negative edge weights
vector<int> dijkstra(vector<vector<pair<int, int>>>& adj, int src, int dest) {
    // Set up the priority queue with the distances
    // of the vertices from the source vertex
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
    vector<int> dist(adj.size(), numeric_limits<int>::max());
    dist[src] = 0;
    q.emplace(dist[src], src);

    // Continuously dequeue the vertex with the smallest
    // distance from the queue and update the distances
    // of its neighbors
    while (!q.empty()) {
        int v = q.top().second;
        q.pop();
        for (auto& e : adj[v]) {
            int u = e.first;
            int w = e.second;

            if (dist[u] > dist[v] + w) {
                dist[u] = dist[v] + w;
                q.emplace(dist[u], u);
            }
        }
    }
    // Return the shortest-path tree as a sequence
    // of vertices from the source to the destination
    vector<int> path;
    int u = dest;
    while (u != src) {
        path.push_back(u);
        for (auto& e : adj[u]) {
            int v = e.first;
            if (dist[v] == dist[u] - e.second) {
                u = v;
                break;
            }
        }
    }
    path.push_back(src);
    reverse(path.begin(), path.end());
    return path;
}

```

#### Process

1. Start with the source vertex, and assign it a distance of 0.
2. Initialize the priority queue with all the vertices, ordered by their distances from the source.
3. Repeat the following steps until the destination is reached or the queue is empty:
   - Dequeue the vertex with the smallest distance from the queue.
   - Update the distances of its neighbors by considering the edge weights and the current distances of the vertices.
   - Re-order the queue according to the updated distances of the vertices.
4. If the destination was reached, the algorithm has produced a shortest-path tree. Otherwise, the destination is not reachable from the source.

#### Time Complexity

The time complexity of Dijkstra's algorithm using a priority queue is  $O(E\log V)$, where E is the number of edges and V is the number of vertices in the graph.

## Top Sort

### Code

```c++
#include <iostream>
#include <vector>
#include <queue>

const int MAXN = 100; // maximum number of nodes

std::vector<int> adj[MAXN]; // adjacency list
int in_degree[MAXN]; // in-degree of each node

// Perform a topological sort on the given graph and
// return the sorted order as a vector of node indices.
std::vector<int> top_sort(int n) {
  std::vector<int> order;
  std::queue<int> q;

  // Initialize in-degree of each node
  for (int i = 0; i < n; i++) {
    in_degree[i] = 0;
  }

  // Count in-degree of each node
  for (int u = 0; u < n; u++) {
    for (auto v : adj[u]) {
      in_degree[v]++;
    }
  }

  // Enqueue all nodes with 0 in-degree
  for (int i = 0; i < n; i++) {
    if (in_degree[i] == 0) {
      q.push(i);
    }
  }

  // Process the queue, adding each node to the sorted order
  // and decrementing the in-degree of its neighbors.
  while (!q.empty()) {
    int u = q.front();
    q.pop();
    order.push_back(u);
    for (auto v : adj[u]) {
      in_degree[v]--;
      if (in_degree[v] == 0) {
        q.push(v);
      }
    }
  }

  return order;
}

int main() {
  // Example usage:
  adj[5].push_back(2);
  adj[5].push_back(0);
  adj[4].push_back(0);
  adj[4].push_back(1);
  adj[2].push_back(3);
  adj[3].push_back(1);

  std::vector<int> order = top_sort(6);
  for (auto node : order) {
    std::cout << node << " ";
  }
  std::cout << std::endl;

  return 0;
}

```

### Process

To perform a topological sort on a DAG, the following steps can be followed:

1. Start by choosing an arbitrary node `v` in the graph and marking it as visited.
2. Consider all nodes `u` that are adjacent to `v`, and that have not yet been visited. For each such node `u`, mark it as visited and recursively apply the topological sort to it.
3. When all nodes that are adjacent to `v` have been visited, add `v` to the output list.
4. Repeat this process until all nodes in the graph have been visited and added to the output list. The resulting output list is the topological sort of the graph.

It is important to note that a topological sort is only possible for a DAG, as it is not possible to produce a linear ordering of the nodes in a graph that contains cycles. If the input graph contains a cycle, the topological sort will fail. So top sort can judge whether a graph contains circles.

### Complexity

The time complexity of topological sort is $O(V + E)$, where `V` is the number of nodes in the graph and `E` is the number of edges. This is because the algorithm visits each node  and each edge in the graph once, so the time complexity is proportional  to the sum of the number of nodes and edges.

## Strong Connected Components (SCC)

A strong connected component is a group of nodes in which there is a path from any node to any other node in the group.

### Korsaraju

1. Start by performing a depth-first search (DFS) on the graph to compute the finish times for each node. The finish time of a node is the time at which the DFS algorithm finishes visiting that node.
2. Create a new graph that is the transpose of the original graph. The transpose of a graph is a new graph with the same nodes as the original graph, but with the direction of each edge reversed.
3. Perform another DFS on the transposed graph, but this time visit the nodes in decreasing order of their finish times from the previous DFS. This will produce a forest of trees, where each tree represents a strong connected component in the original graph.

### Tarjan

1. Start by performing a depth-first search (DFS) on the graph.
2. As the DFS algorithm visits each node, maintain a stack of nodes that are currently in the DFS search tree.
3. When a node is first visited, push it onto the stack.
4. When a node is finished being visited (i.e., all of its descendants have been visited), pop it off the stack and add it to the current strong connected component.
5. If a node `v` is visited while another node `u` is on the stack, and there is a path from `u` to `v` in the search tree, then `v` must also be in the same strong connected component as `u`. In this case, remove all nodes from the stack that are on the path from `u` to `v`, and add them to the current strong connected component.

## Flow Problem

### Hungarian Algorithm

Time complexity: $O(n^3)$, finding best - match of Bipartite graph.

1. Begin by subtracting the smallest element from each row and column of the cost matrix. This ensures that all zeros in the final matrix will be part of a minimum-cost assignment.
2. Cover each row of the matrix with the fewest possible number of lines (vertical and horizontal) such that all zeros are covered.
3. If all rows are covered, then the algorithm terminates and the minimum-cost assignment is found. If not, then proceed to the next step.
4. Find the smallest uncovered element in the matrix, and add it to every element that is covered by a line. Subtract this smallest element from every element that is not covered by a line.
5. Go back to step 2.

### Max Flow Min Cost

One way to solve the minimum-cost flow problem is to use the shortest path algorithm. This involves finding the shortest path from the source to the sink in the network, and then incrementally sending flow along this path until the maximum flow is reached or the path is exhausted.

Another approach is to use linear programming to formulate the problem as a series of linear equations and then solve for the optimal flow using a linear programming solver.

# Number Theory

## Greatest Common Devisor

```c++
int gcd(int a,int b){
    if(a<b)a^=b^=a^=b;
    if(b==0)return a;
    return gcd(b,a%b);
}
```

Time complexity is $O(\log\min(a,b))$

## Lucas Theorem

p is prime.

$C_n^m\ mod\ p=C_{n/p}^{m/p}*C_{n\ mod\ p}^{m\ mod\ p}mod\ p$

Time complexity: $\log_pn*\log_pm$.

```c++
// C++ Version
long long Lucas(long long n, long long m, long long p) {
  if (m == 0) return 1;
  return (C(n % p, m % p, p) * Lucas(n / p, m / p, p)) % p;
}
```

# String

## Knuth Morris Pratt (KMP)

Time complexity: $O(n)$

A string-matching algorithm that is used to find the occurrences of a  pattern in a given string. It does this by preprocessing the pattern to  compute a table of failure function values, which are then used to skip  comparisons when searching for the pattern in the string. This can make  the KMP algorithm faster than other string-matching algorithms in  certain cases.

Next array means the maximum length of prefix and suffix which prefix = suffix of current sub-string.

```c++
int* getNext(string p) {
	int* next = new int[p.length()];
	next[0] = -1;//while the first char not match, i++,j++
	int j = 0;
	int k = -1;
	while (j < (int)p.length() - 1) {
		if (k == -1 || p[j] == p[k]) {
			j++;
			k++;
			next[j] = k;
		} else {
			k = next[k];
		}
	}
	return next;
}
int KMP(string T, string p) {
	int i = 0;
	int j = 0;
	int* next = getNext(T);
	while (i < (int)T.length() && j < (int)p.length()) {
		if (j == -1 || T[i] == p[j]) {
			i++;
			j++;
		} else {
			j = next[j];
		}
	}
	if (j == (int)p.length()) {
		return i - j;
	}
	return -1;
}
```

# NP Complete

to classify problems according to their computational complexity.

## P (polynomial time)

A problem is in P if there exists an algorithm for **solving** the problem such that the time it takes to solve the problem is at most a **polynomial** function of the size of the input.

## NP (nondeterministic polynomial time)

A problem is in NP if there exists an algorithm for **verify**ing solutions  to the problem such that the time it takes to verify a solution is at most a **polynomial** function of the size of the input. Can't (or not sure to) be solved in polynomial time.

$P\subseteq NP$, but $P\stackrel{?}=NP$ is not solved now.

## NP-hard

A problem is said to be NP-hard if it is at least as hard as any problem in NP. In other words, an NP-hard problem is a problem that is at least as difficult to solve as any problem in NP. Note that an NP-hard  problem does not necessarily have to be in NP, but any problem in NP is also NP-hard.

## NPC(omplete)

A problem is in NPC if it is both in NP and NP-hard.

z.B. TSP (traveling salesperson problem), The subset sum problem, The 3-satisfiability problem (3-SAT): Given a boolean formula in  conjunctive normal form with at most three literals per clause,  determine whether there exists a truth assignment to the variables that  makes the formula evaluate to true.

## Relations between them

$P\subseteq NP$

$NP-hard\bigcap NP=NPC$