---
title: Codeforces Round 805 (Div. 3) G Passable Paths
author: tinsir888
date: 2022/7/11
cover: /img/codeforces.jpg
katex: true
tags:
  - LCA
  - Codeforces
categories:
  - 算法与数据结构刷题
abbrlink: 682df6fc
---

> This is a hard version of the problem. The only difference between an easy and a hard version is in the number of queries.
>
> Polycarp grew a tree from *n* vertices. We remind you that a tree of $n$ vertices is an undirected connected graph of $n$ vertices and $n−1$ edges that does not contain cycles.
>
> He calls a set of vertices passable if there is such a path in the tree that passes through each vertex of  this set without passing through any edge twice. The path can visit  other vertices (not from this set).
>
> In other words, a set of vertices is called passable if there is a simple path that passes through all the vertices of this set (and possibly some other).
>
> For example, for a tree below sets $\{3,2,5\}, \{1,5,4\}, \{1,4\}$ are passable, and $\{1,3,5\}, \{1,2,3,4,5\}$ are not.

# 题目大意

给定一颗 n 个节点的树，进行 q 次查询。每次查询输入 k 的节点编号，问这 k 个节点在树上是否能构成一条链。

# 思路

如果 k 个节点能连成一条链，那么在树上就能找得一个点 r，使得剩下的点 r *要么在左边，要么在右边*子树的某一条链上。由此可见，需要大量进行“求树上两个点的最近公共祖先运算”，观察数据规模，每次运算如果使用朴素的 $O(n)$ 算法，会超时。因此需要采用 LCA 倍增的算法将查询时间降低为 $O(\log n)$。

# 具体实现

令左边子树最深节点为 ld，右边子树最深的节点为 rd，树上两个节点的最近公共祖先为 $LCA(*,*)$。 

1. 先对树进行 DFS，标注出每个节点的深度。 
2. 找出 ld 和 rd。ld：遍历每个节点，取最深的点。rd：遍历每个节点，取最深的并且满足不和 ld 在同一边（即 $LCA(ld,rd)!=rd$）的节点。如果查询时凑巧所有的点都在同一颗子树的一条链上，即 rd 不存在，直接判定结果为 YES。
3. 计算出节点 r 为 $LCA(ld,rd)$ 。
4. 遍历每个节点 pi，检查 pi 是否满足 $LCA(p,ld)==p\&\&LCA(p,rd)==r$（当 pi 在 r 左子树的一条链上）或 $LCA(p,rd)==p\&\&LCA(p,ld)==r$（当 pi 在 r 右子树的一条链上）。
  - 如果有一个节点不满足条件，则最终结果判定为 NO。
  - 如果所有节点满足条件，则最终结果判定为 YES 。

# 参考代码

```c++
#include<bits/stdc++.h>
using namespace std;
inline int read(){
	int x=0;
	char ch=getchar();
	while(ch<'0'||ch>'9')ch=getchar();
	while(ch<='9'&&ch>='0'){
		x=x*10+ch-48;
		ch=getchar();
	}
	return x;
}
const int maxn=2e5+5;
const int maxdep=20; 
vector<int>G[maxn];
int n,qt,dep[maxn],fa[maxn][25],q[maxn];
void dfs(int p,int anc){
	dep[p]=dep[anc]+1;
	for(int i=1;i<=maxdep;i++){
		fa[p][i]=fa[fa[p][i-1]][i-1];
	}
	vector<int>::iterator it;
	for(it=G[p].begin();it!=G[p].end();it++){
		if(*it==anc)continue;
		fa[*it][0]=p;
		dfs(*it,p);
	}
}
int lca(int l,int r){
	if(dep[l]<dep[r]){
		l^=r^=l^=r;
	}
	for(int k=maxdep;k>=0;k--)
		if(dep[fa[l][k]]>=dep[r])
			l=fa[l][k];
	if(l==r)return l;
	for(int k=maxdep;k>=0;k--)
		if(fa[l][k]!=fa[r][k]){
			l=fa[l][k];
			r=fa[r][k];
		}
	return fa[l][0];
}
void work(){
	memset(q,0,sizeof q);
	int k=read();
	for(int i=1;i<=k;i++)
		cin>>q[i];
	int le=q[1],ri=-1;
	for(int i=2;i<=k;i++){
		if(dep[q[i]]>dep[le])le=q[i];
	}
	for(int i=1;i<=k;i++){
		if(q[i]!=le){
			int tmp=lca(le,q[i]);
			if(tmp!=q[i])
				if(ri==-1||dep[q[i]]>dep[ri])
					ri=q[i];
		}
	}
	if(ri==-1){
		puts("YES");
		return;
	}
	bool f=1;
	int r=lca(le,ri);
	for(int i=1;i<=k;i++){
		int tmp1=lca(q[i],le);
		int tmp2=lca(q[i],ri);
		if(!((tmp1==q[i]&&tmp2==r)||(tmp1==r&&tmp2==q[i])))
			f=0;
	}
	puts(f?"YES":"NO");
	return;
}
int main(){
	n=read();
	for(int i=1;i<n;i++){
		int x=read(),y=read();
		G[x].push_back(y);
		G[y].push_back(x);
	}
	dfs(1,0);
	qt=read();
	while(qt--)
		work();
	return 0;
}
```

