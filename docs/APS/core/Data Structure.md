---
title: APS 重点复习课程之数据结构
author: tinsir888
date: 2022/10/18
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 数据结构
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: dccc524f
---

# Overview

Linear List

- Sequence Table (Array)
- Linked List
- Stack
- Queue

Tree

- Binary Tree
- Pre-order, In-order, Post-order, on the level Traversal
- Binary Sort Tree
- B Tree
- B+ Tree

Graph

- Adjacent Matrix and Adjacent List
- DFS
- BFS

Search Algorithm

- Binary Search
- Heap Search
- B Tree

Sort Algorithm

- Bubble Sort
- Selective Sort

- Quick Sort
- Merge Sort

# Linear list

## Sequence Table

```c++
template<typename T>
T arr[100];//applied by array
```

- insert an element at i-th position: every element behind i-th position move to the next one respectively

  O(n) for each insert-element operation

- remove an element at i-th position: every element beind i-th position move to the last one respectively

  O(n) for each remove-element operation

## Linked List

### Single Linked List

```c++
template<typename T>
struct Node{
    T val;
    Node *next;
};
```

### Double Linked List

```c++
template<typename T>
struct Node{
    T val;
    Node *next;
    Node *last;
}
```

## Stack

- A special linear list: insert and delete operations are only allowed to occur at only side of the list.
  - Top: the side used to insert or delete element
  - Bottom: the other side
- Insert an element: `stk.push(element)`
- Delete an element: `stk.pop(element)`

## Queue

- A special linear list: insert only allowed at the back, delete only allowed at the front.

- back(tail) front(head). FIFO (first in first out)

# Tree

- non-linear structure
- root: node on the top, has no precursor (ancestor)

- leaf node: has no successors (offspring)

- depth: distance between current node and root
- height: distance between current node and the farthest leaf node **of sub-tree**

## Binary Tree

```c++
template<typename T>
typedef struct node{
    T val;
    node* left_child=nullptr;
    node* right_child=nullptr;
}node;
```



- max number of nodes on the i-th level of Binary Tree: $2^{i-1}$ (two to the power of i minus one)

- a binary tree of depth k has at most: $2^{k}-1$ nodes

- number of leaf nodes: a, number of nodes of two out-degree: b, a=b+1 （叶子节点数=出度为 2 的节点数+1）
- the depth of a complete binary tree with n nodes: $ground(\log_2n)+1$

### Complete Binary Tree

Except *the nodes in second last layer and the last layer*, every nodes have both right and left subtree.

（就是除了最后两层节点，其它节点都必有左右子节点）

## Traversal

### Pre-order Traversal

For each node being visited: current node→left successor→right successor

```c++
void pre_order(Node* cur){//recursion
    if(cur!=NULL){
        visit(root);
        pre_order(cur->left_child);
        pre_order(cur->right_child);
    }
}
void un_pre_order(Node* cur){//no-recursion
    /*
    apply with stack
    1. visit current node first, then push it into stack.
    2. visit left child, after finished, pop.
    3. visit right child
    */
    stack<Node*>stk;
    Node* p=cur;
    while(p!=NULL||(!stk.empty())){
        if(p!=NULL){
            visit(p);
            stk.push(p);
            p=p->left_child;
        }
        else{
            p=stk.top();
            stk.pop();
            p=p->right_child;
        }
    }
}
```

### In-order Traversal

For each node being visited: left successor→current node→right successor

```c++
void in_order(Node* cur){//recursion
    if(cur!=NULL){
        in_order(cur->left_child);
        visit(cur);
        in_order(cur->right_child);
    }
}
void un_in_order(Node* cur){//no-recursion
    /*
    apply with stack
    1. push left child into stack in order.
    2. visit top of stack, then pop it.
    3. traverse right child in order.
    */
    stack<Node*>stk;
    Node* p=cur;
    while(p!=NULL||(!stk.empty())){
        if(p!=NULL){
            stk.push(p);
            p=p->left_child;
        }
        else{
            p=stk.top();
            stk.pop();
            visit(p);
            p=p->right_child;
        }
    }
}
```

### Post-order Traversal

For each node being visited: left successor→right successor→current node

```c++
void post_order(Node* cur){//recursion
    if(cur!=NULL){
        post_order(cur->left_child);
        post_order(cur->right_child);
        visit(cur);
    }
}
/*
no-recursion:
apply with stack.
1. traverse left child in order, mark with 'l'
2. traverse right child in order, mark with 'r'
3. only allow node marked with 'r' to be visited.
*/
typedef struct Tnode{
    Tnode* node;
    char tag;
}Tnode;
void un_post_order(Node* cur){//no-recursion
    Node *p=cur;
    Tnode *n;
    stack<Tnode*>stk;
    while(p!=NULL||(!stk.empty())){
        while(p!=NULL){
            n=new Tnode;
            n->node=p;
            n->tag='l';
            stk.push(n);
            p=p->left_child;
        }
        n=stk.top();
        stk.pop();
        if(n->tag=='l'){
            n->tag='r';
            stack.push(n);
            p=n->node;
            p=p->right_child;
        }
        else{//if(n->tag='r')
            visit(n->node);
            p=NULL;
        }
    }
}
```

### Traversal on the level (BFS)

apply with queue.

1. push current node into queue
2. when current queue is not empty, pop a element A and visit it.
3. push left child and right child of A into queue.

```c++
void on_the_level(Node* cur){
    queue<Node*>q;
    Node* p=cur;
    q.push(p);
    while(!q.empty()){
        p=q.front();
        q.pop();
        visit(p);
        if(p->left_child!=NULL)
            q.push(p->left_child);
        if(p->right_child!=NULL)
            q.push(p->right_child);
    }
}
```

## Binary Sort Tree

- the value of every nodes in the left subtree is smaller than the subtree's root's value.

  （所有左子树节点的值都小于当前节点的值）

- the value of every nodes in the right subtree is bigger than the subtree's root's value.

  （所有右子树节点的值都大于当前节点的值）

- in-order traversal of BST brings an increasing sequence.

### AVL Tree (Balanced BST)

- a BST.
- absolute value of depth difference between left and right subtrees is no more than 1
- every subtree of AVL tree is also AVL tree.

## B Tree

- similar to the BST, it focus on effective search
- it differently record not only the key-value, but also a in-order table of the key-values.
- don't need to be binary

## B+ Tree

- each node of the B+ tree except the leaf nodes can be seen as a index, the B+ tree only store the key values in the leaf nodes, and the other nodes store the most(high/low)key-value of its subtree.

- characters are like this:

  once we have find the equal value of search in the nodes, we don't stop the search process until we arrive the leaf nodes. which means both the successful or the failure situation we have to traverse a complete path in the tree.

- widely used in the storage method of database to save time of searching

## Huffman Tree

related to Huffman coding. (in order to compress)

For each char and corresponding probability/frequency:

1. find the current 2 least frequency, merge them.
2. replace these 2 least frequency with a sum of them.
3. loop 1.

# Graph

## How to store a graph?

### Adjacent Matrix

- advantages: simple to insert and delete edges
- disadvantages: hard to insert or delete vertex
- space complexity: $O(n^2)$ with a 2-dimensional matrix

### Adjacent List

each vertex correspond to a list.

the list stores vertex which directly linked with current vertex

## DFS & BFS on a graph

……

# Search Algorithm

## Binary Search

```c++
template<typename T>
T bs(T left,T right){
    assert(left<=right)
    if(left==right)return left;
    int mid=(left+right)/2;
    if(judge(mid))return mid;
    bs(left,mid);
    bs(mid+1,right);
}
```

## B Tree

## B+ Tree

## Hash Map

# Sort Algorithm

## Bubble Sort

time complexity: $O(n^2)$

1. compare adjacent 2 data, if left > right, swap.
2. we assure in every loop, the last one is max value.
3. loop 1. and 2.

## Selective Sort

time complexity: $O(n^2)$

1. for each position, loop:
2. find the min value, swap with data at current position.

## Quick Sort

- average time complexity: $O(n\log n)$
- time complexity in worst condition: $O(n^2)$

1. find a random element as basis.
2. place the element smaller than basis to left, bigger than basis to right.
3. recursion to quick sort subarray.

## Merge Sort

- time complexity: $O(n\log n)$
- 2 times space complexity than quick sort, but time complexity is more stable.

1. divide n element into two n/2-subsequences
2. conquer: using merge sort to merge 2 subsequences.
3. merge 2 sorted subsequence.

