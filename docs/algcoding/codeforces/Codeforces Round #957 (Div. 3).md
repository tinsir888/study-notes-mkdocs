---
title: Codeforces Round 957 (Div. 3) 解题思路
author: tinsir888
date: 2024/7/12
cover: /img/codeforces.jpg
katex: true
tags:
  - Codeforces
categories:
  - 算法与数据结构刷题
abbrlink: 837cad8b
---

本次比赛 [链接](https://codeforces.com/contest/1992)

现在的水平只能碰碰 Div 3

# A. Only Pluses

三个数越相近，乘积就越大。因此五次递增每次分别作用于当前三个数中最小的那个。

# B. Angry Monk

除了当前最长的那一个片段之外，其他片段都必须要掰成一节节之后再拼上。这样总步数最小。

# C. Gorilla and Permutation

定义的两个函数 $f(i)$ 表示数组前缀长度 $i$ 中所有大于 $k$ 的数字之和，$g(i)$ 表示数组前缀长度 $i$ 中所有小于 $m$ 的数字之和。要想最大化 $\sum_i(f(i)-g(i))$, 就必须要让 $f(i)$ 尽量大，$g(i)$ 尽量小。

所以构造的排列就是先是比 $k$ 大的数字降序排列，中间的无所谓，之后是比 $m$ 小的数字的升序排列。

# D. Test of Love

## 题目大意

给出一个长度为 $n$ 的序列，其中包含水 `W`、鳄鱼 `C` 和浮木 `L`。如果在岸上或在浮木上，可以向前最多跳 `m` 格，可以跳到水里、浮木或岸上；如果当前在水里，则可以向前游一格，但整个过程中最多能游 `k` 格；无论如何都不能进入鳄鱼所在的格子。问：是否从起始位置到达彼岸。

## 解题思路

暴搜。因为游是有限制的，跳是无限制的，所以能跳则跳。

- 如果当前在岸上或在浮木上，向前搜索选择跳到最远的浮木。如果前面 `m` 格都没有浮木，选择能跳到最远的水里。
- 如果当前在水里，向前游一格。
- 如果游到鳄鱼格，则搜索失败。
- 如果游到或跳到彼岸，则搜索成功。

## 参考代码

```c++
#include<bits/stdc++.h>
using namespace std;
int n,m,k;
//n length
//m jump
//k swim
string river;
bool BFS(int pos,int swim_remain){
	if(swim_remain<0)return false;
	if(pos>=n)return true;
	if(river[pos]=='C')return false;
	else if(river[pos]=='L'){
		for(int i=m;i>=1;i--){
			if(pos+i>=n)return true;
			else if(river[pos+i]=='L'){return BFS(pos+i,swim_remain);}
		}
		for(int i=m;i>=1;i--){
			if(pos+i>n)return true;
			else if(river[pos+i]=='W'){
				return BFS(pos+i,swim_remain);
			}
		}
		return false;
	}
	else if(river[pos]=='W'){
		return BFS(pos+1,swim_remain-1);
	}
}

void work(){
	cin>>n>>m>>k;
	cin>>river;
	for(int i=0;i<m;i++){
		if(BFS(i,k)){
			puts("YES");
			return;
		}
	}
	puts("NO");
	return;
}

int main(){
	int test_cases;
	cin>>test_cases;
	while(test_cases--)
	work();
	return 0;
}
```

# E. Novice's Mistake

## 题目大意

给定 $n$，求所有的 $(a,b)$ 对，满足 $a\times n-b$ 的结果对应的（非空）字符串刚好和将 $n$ 对应字符串复制并拼接 $a$ 次，然后裁剪掉长度为 $b$ 的后缀得到的字符串一样。

## 解题思路

$a,b$ 的范围 $10^5$，因此不能用最直观的暴力 $O(n^2)$ 解法。

注意到 $a\times n$ 的范围，最多不会超过 $n\times 10^4$。又根据“字符串复制拼接”操作，得出 $a\times n-b$ 对应结果只有那么几种。比如 $n=34$ 时，$a\times n-b$ 可能取值只有 $3,34,343,3434,34343,343434$。针对这些值再暴力枚举 $a$ 即可。此方法的时间复杂度为 $O(n\log n)$。

## 参考代码

```c++
#include<bits/stdc++.h>
using namespace std;
int n;
int ground_truth;
queue<int>res;
void verify(int X){
	for(int ai=1;ai<=10000;ai++){
		int b=ai*n-X;
		if(b<=0)continue;
		if(b>min(10000,ai*n))continue;
		string str_n=to_string(n);
		int len_an=ai*str_n.length();
		int len_X=to_string(X).length();
		if(len_an-b!=len_X)continue;
		if(b>=len_an)continue;
		res.push(ai);
		res.push(b);
	}
}
void work(){
	cin>>n;
	string str_n=to_string(n);
	int seg=str_n.length();
	//generate candidate
	str_n=str_n+str_n+str_n+str_n+str_n;
	while(str_n.length()>seg+4){
		str_n.erase(str_n.length()-1);
	}
	while(str_n.length()){
		verify(stoi(str_n));
		str_n.erase(str_n.length()-1);
	}
	cout<<res.size()/2<<endl;
	while(!res.empty()){
		int tmp=res.front();
		res.pop();
		cout<<tmp<<" ";
		tmp=res.front();
		res.pop();
		cout<<tmp<<endl;
	}
	return;
}
int main(){
	int test_cases;
	cin>>test_cases;
	while(test_cases--)
		work();
	return 0;
}
```

# F. Valuable Cards

## 题目大意

给定一个数组和一个值 $x$。目标是将数组划分成最少的段数，使得每段中都不存在任何一个子集，其中所有元素的积为 $x$。

## 解题思路

贪心。一段一段地找，只要发现有几个元素相乘等于 $x$，就立马切开。具体实现可以使用 C++ STL 里高效的 set 来存储所有 $x$ 除以一些元素的商，一旦新扫描的值匹配上了，就立刻切开。

## 参考代码

```c++
#include<bits/stdc++.h>
using namespace std;
const int maxn=1e5+5;
int n,x;
vector<int>a;
vector<int>temp;
set<int> divi;
void work(){
	a.clear();
	divi.clear();
	temp.clear();
	cin>>n>>x;
	for(int i=1;i<=n;i++){
		int tmp;
		cin>>tmp;
		if(x%tmp==0)a.push_back(tmp);
	}
	if(a.size()==0){
		puts("1");
		return;
	}
	int res=1,left=0,ptr=0;
	divi.insert(x);
	divi.insert(x/a[ptr]);
	ptr++;
	while(ptr<a.size()){
		if(divi.find(a[ptr])!=divi.end()){
			res++;
			left=ptr;
			divi.clear();
			divi.insert(x);
			divi.insert(x/a[ptr]);
			ptr++;
			continue;
		}
		temp.clear();
		for(auto it:divi){
			if(it%a[ptr]==0){
				temp.push_back(it/a[ptr]);
			}
		}
		for(auto it:temp){
			divi.insert(it);
		}
		ptr++;
	}
	cout<<res<<endl;
	return;
	return;
}

int main(){
	int test_cases;
	cin>>test_cases;
	while(test_cases--)
		work();
	return 0;
}
```

# G. Ultra-Meow

## 题目大意

定义 $MEX(a,b)$ 函数表示 $a$ 集合中未出现的第 $k$ 个正整数。求 $\{1,\cdots,n\}$ 的所有子集 $b$ 的 $MEX$ 值之和 $\sum_{b\subseteq a}MEX(b,|b|+1)$.

## 解题思路

对于 $n$，$MEX$ 函数的取值范围是 $1$ 到 $2n+1$。对于每个 $MEX$ 函数值，从 $0$ 到 $n$ 枚举子集的大小 $size$。

对于 $MEX$ 值为 $val$, 子集大小为 $size$ 而言，

- $1,\cdots,val-1$ 位置上必须刚好有 $size$ 个位置不在子集里。那么刚好有 $a=(val-1)-size$ 个位置上的值在子集里。

  （如果 $val$ 比 $n$ 大，$val-1$ 被进一步限制为 $n$）

  情况的可能性有 $\left(\begin{array}{cc}\min\{val-1,n\}\\a\end{array}\right)$ 种。

- 如果 $val$ 比 $n$ 小，那么 $val+1,\cdots,n$ 位置上就刚好有 $size-a$ 个位置上的值在子集里。该情况的可能性有 $\left(
  \begin{array}{cc}
  n-val\\
  size-a
  \end{array}
  \right)$ 种。

因此，$MEX$ 值为 $val$，子集大小为 $size$ 对应的子集个数为：
$$
\left(\begin{array}{cc}\min\{val-1,n\}\\
a
\end{array}\right)
\times
\left(
\begin{array}{cc}
\max\{0,n-val\}\\
size-a
\end{array}
\right)
$$
最终的结果就是对应的 $MEX$ 与之相乘再求和。

## 参考代码

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll mod = 1e9+7;
const int maxn = 5010;
ll C[maxn][maxn];
int n;
void prepare(){
	for(int i=1;i<maxn;i++){
		C[i-1][0]=1;
		for(int j=1;j<maxn;j++){
			C[i][j]=(C[i-1][j]+C[i-1][j-1])%mod;
		}
	}
}
void work(){
	cin>>n;
	int res=0;
	for(int sz=0;sz<=n;sz++){
		for(int mex=1;mex<=2*n+1;mex++){
			int a=mex-sz-1,b=sz-a;
			//a represents # all chosen elements that is less than k
			//b represents # all chosen elements that is greater than k
			if(a>=0&&b>=0){
				res=(res+C[min(mex-1,n)][a]*C[max(0,n-mex)][b]%mod*mex)%mod;
			}
		}
	}
	cout<<res<<endl;
	return;
}

int main(){
	prepare();
	int test_cases;
	cin>>test_cases;
	while(test_cases--)
		work();
	return 0;
}
```