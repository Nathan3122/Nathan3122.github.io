---
layout: post
title: 线段树
categories: OI
description: 线段树基础知识
keywords: 数据结构,线段树,OI
topmost: false
---

线段树基础知识
--------------

- 线段树是解决 RMQ 问题中的一种常用的数据结构，树状数组能实现的功能是线段树能实现功能的子集。
- 线段树可以在 $$O(\log n)$$ 内实现。
- 单点/区间修改（加，乘，赋值，开根号……）。
- 单点/区间查询（求和，求积，求max，求min，求gcd）。

- $$d_x$$ 维护的区间是 $$[l,r]$$，左右儿子维护的区间是 $$[l,mid]$$ 和 $$[mid+1,r]$$，其中 $$mid=\left \lfloor \frac {l+r}{2}  \right \rfloor $$。

### 建树：

```cpp
#define mid ((l+r)>>1)
#define ls(x) (x<<1)
#define rs(x) (x<<1|1)
void build(int p,int l,int r){
	if(l==r){ // 点 p 代表区间 [l,l] 
		s[p]=a[l]; // 初始化 
		return;
	}
	build(ls(p),l,mid), build(rs(p),mid+1,r);
	s[p]+=s[ls(p)]+s[rs(p)]; // push_up(p) 
}
```
- push_up(p) 代表信息上传至 $$p$$，即将 $$s_{ls(p)},s_{rs(p)}$$ 的信息合并到 $$s_p$$ 中。
- 对于数组空间，每个点都会访问到自己的左右儿子，且线段树的空间具有扩充性，$$2^n+1$$ 个结点的线段树实际上会扩充到 $$2^{n+1}$$ 个。
- 所以线段树一般开 4 倍空间，即 int a[N],s[N * 4];
- 调用 $$build(1,1,n)$$ 就代表根结点 $$1$$ 维护区间 $$[1,n]$$ 向下递归建树，每一个叶子结点代表 $$a$$ 中一点。

### 区间查询：
- 设要找的区间是 $$[L,R]$$，我们递归区间 $$[l,r]$$，向 $$L \leq l\leq r \leq R$$ 的目标下递归。
- 仍然每次递归 $$[l,mid]$$ 和 $$[mid+1,r]$$，同时减掉 $$[L,R]$$ 外的区间。
- 一旦满足 $$[l,r]$$ 包含于 $$[L,R]$$，直接 return。

```cpp
int ask(int p,int l,int r,int L,int R){ // 区间查询 
	if(r<L||R<l) return 0; // [l,r]在[L,R]外
	if(L<=l&r<=R) return s[p]; // [l,r]在[L,R]内
   	push_down(p,l,r); // 下移懒标记
	return ask(ls(p),l,mid,L,R)+ask(rs(p),mid+1,r,L,R);//递归合并答案 
}
ask(1,1,n,l,r);
```
### 单点修改：
- 用 $$[l,r]$$ 二分查找要修改的位置 $$x$$，得到点 $$p$$。
- 由于 $$p$$ 一定是叶子结点，直接修改信息即可。

```cpp
int add(int p,int l,int r,int x,int y){
	if(l==r) s[p]+=y;
	x<=mid ? add(ls(p),l,mid,x,y) : add(rs(p),mid+1,r,x,y);
	s[p]+=s[ls(p)]+s[rs(p)];// push_up(p)
}
```
`x<=mid ? ……` 这个不能忘记


### 区间修改:
- **懒惰标记**：就是通过延迟对节点信息的更改，从而减少可能不必要的操作次数。每次执行修改时，我们通过打标记的方法表明该节点对应的区间在某一次操作中被更改，但不更新该节点的子节点的信息。实质性的修改则在下一次访问带有标记的节点时才进行。（摘自 OI Wiki）
- $$t[4 * N]$$ 是懒标记数组。

```cpp
int t[4*N];
void maketag(int p,int l,int r,int x){ // 打懒标记 
	s[p]+=(r-l+1)*x, t[p]+=x;
}
void push_down(int p,int l,int r){
	if(!t[p]) return; // 没有懒标记就返回
	maketag(ls(p),l,mid,t[p]); maketag(rs(p),mid+1,r,t[p]);
	t[p]=0; // 清除父亲的懒标记 
}
```

```cpp
void update(int p,int l,int r,int L,int R,int x){
	if(r<L||R<l) return;
	if(L<=l&&r<=R){
		maketag(p,l,r,x); return;
	}
	push_down(p,l,r);
	update(ls(p),l,mid,L,R,x); update(rs(p),mid+1,r,L,R,x);
	push_up(p);
}
```

- 结合 `add` 和 `push_down` 就可以实现区间修改的代码。


## [P3372 【模板】线段树 1](https://www.luogu.com.cn/problem/P3372)

```cpp
#include<bits/stdc++.h>
#define ll long long
#define mid ((l+r)>>1)
#define ls(x) (x<<1)
#define rs(x) (x<<1|1)
const int inf=0x3f3f3f3f,maxn=5e5+7;
using namespace std;
int n,m;
ll a[maxn],s[maxn<<2],t[maxn<<2];
void push_up(int p){
	s[p]=s[ls(p)]+s[rs(p)];
}
void build(int p,int l,int r){
	if(l==r){
		s[p]=a[l]; return;
	}
	build(ls(p),l,mid); build(rs(p),mid+1,r);
	push_up(p);
}
void maketag(int p,int l,int r,int k){
	s[p]+=1ll*(r-l+1)*k; t[p]+=k;
}
void push_down(int p,int l,int r){
	if(!t[p]) return;
	maketag(ls(p),l,mid,t[p]); maketag(rs(p),mid+1,r,t[p]);
	t[p]=0;
}
ll ask(int p,int l,int r,int L,int R){
	if(l>R||r<L) return 0;
	if(l>=L&&r<=R) return s[p];
	push_down(p,l,r);
	return ask(ls(p),l,mid,L,R)+ask(rs(p),mid+1,r,L,R);
}
void update(int p,int l,int r,int L,int R,int x){
	if(r<L||R<l) return;
	if(L<=l&&r<=R){
		maketag(p,l,r,x); return;
	}
	push_down(p,l,r);
	update(ls(p),l,mid,L,R,x); update(rs(p),mid+1,r,L,R,x);
	push_up(p);
}
int main(){
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++){
		scanf("%lld",&a[i]);
	}
	build(1,1,n);
	while(m--){
		int op,x,y,k;
		scanf("%d%d%d",&op,&x,&y);
		if(op==2){
			printf("%lld\n",ask(1,1,n,x,y));
		}else{
			scanf("%d",&k);
			update(1,1,n,x,y,k);
		}
	}
	return 0;
}
```
