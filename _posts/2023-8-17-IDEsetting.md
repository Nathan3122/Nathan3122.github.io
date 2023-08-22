---
layout: post
title: IDE设置
categories: OI
description: IDE 常用设置
keywords: OI,缺省源,IDE设置
---

**以下是常用的 IDE 设置。**

----------------


### 缺省源:
```cpp
#include<bits/stdc++.h>
#define re register int
#define ll long long
#define ull unsigned long long
const int inf=0x3f3f3f3f;
using namespace std;
template <typename T>
T read(){
	T ret=0,sgn=0; char ch=getchar();
	while(!isdigit(ch)) sgn |= ch == '-', ch = getchar();
	while(isdigit(ch)) ret = ret*10 + ch-'0', ch = getchar();
	return sgn ? -ret : ret;
}
void write(int x){
	if(x<0) putchar('-'), x=-x;
	if(x>9) write(x/10);
	putchar(x%10+'0');
}

int main(){
//	int n=read<int>();
	return 0;
}
```



### 编译器配置集：
```cpp
-O2 -Wl,--stack=102400000 -DDEBUG -std=c++14
```
1. -O2 手动开O2 
1. -Wl,--stack=102400000
1. -DDEBUG 支持下面的 DEBUG 操作
1. -std=c++14 设置 c++14


### 文件操作 DEBUG：
```cpp
#ifndef DEBUG
	freopen("title.in","r",stdin);
	freopen("title.out","w",stdout);
#endif
```
常用以上代码，中间内容别人 IDE 运行，自己 IDE 不运行。
把 #ifdef DEBUG 中间的 $$n$$ 去掉可以实现中间内容自己 IDE 运行，别人 IDE 不运行


