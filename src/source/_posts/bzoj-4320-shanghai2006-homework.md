---
title: '[BZOJ 4320] ShangHai2006 Homework'
tags:
  - 数学
  - 数据结构
id: '360'
categories:
  - - 分块
  - - 数据结构
date: 2019-03-19 21:39:54
updated: 2019-03-19 21:39:54
---

# 题面

[4320: ShangHai2006 Homework](https://www.lydsy.com/JudgeOnline/problem.php?id=4320)

* * *

# Solution

这是一道分块妙题。 首先，我们发现这题是对一个比较大的任意模数取模，那我们传统的log数据结构可能还真没法下手。 既然如此，我们考虑分块。 我们把原题中的询问分为两类： 1. $p>=\\sqrt{300000}$ 2. $p<\\sqrt{300000}$ 对于第二种情况，我们可以开一个桶$f\[x\]$表示目前为止所有数字%$x$后取得的最小值。这个东西我们在插入数字的时候暴力更新一下即可。 对于第一种情况，事情有点复杂，我们这样做： 把所有数字平铺在数轴上，然后我们假设要求%$p$的ans，我们会发现，**这个ans一定是$p$的整数倍的(右边的最靠近它的数字-它的位置)的最小值**(正确性用草稿纸玩一玩就可以发现了) 那怎么维护某个位置的最右边的最靠近它的数呢？log数据结构显然可以做。问题是log数据结构在这里要T掉。 怎么办呢？我们发现这题可以离线做。 考虑这样做，**我们先把所有数字插入到数轴里面，然后我们维护一个并查集：每一个点的fa指向他的后一个点，如果这个点是有数字的，则他作为一个根。** 我们可以显然发现，**一个点的在它右边的最靠近它的数字一定是它所在的连通块的根**，对于删除数字，我们直接把这个点的fa指向后一个即可。 时间复杂度$O(n\\cdot \\sqrt{300000})$

* * *

# Code

```cpp
//BZOJ 4320: ShangHai2006 Homework
//Mar,19th,2019
//分块处理膜号妙题
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=100000+100;
const int MAX=300000+100;
int fa[MAX];
int GetFa(int x)
{
    if(x>=300001) return 0x3f3f3f3f;
    if(fa[x]==0) return x;
    return fa[x]=GetFa(fa[x]);
}
int num[N],ans[N],op[N],type[N],f[MAX];//type:0插入；1:询问
int n,size=550;
bool vis[MAX];
int main()
{
    freopen("4320.in","r",stdin);
    freopen("4320.out","w",stdout);

    int msize = 256 << 20; // 256MB
    char *p = (char*)malloc(msize) + msize;
    __asm__("movl %0, %%esp\n" :: "r"(p));

    n=read();

    memset(f,0x3f,sizeof f);
    for(int i=1;i<=n;i++)
    {
        char OP[4];
        scanf("%s",OP+1);
        op[i]=read();
        if(OP[1]=='A')
        {
            vis[op[i]]=true;
            for(int j=1;j<size;j++)
                f[j]=min(f[j],op[i]%j);
        }
        else
        {
            type[i]=1;
            if(op[i]<size) ans[i]=f[op[i]];
        }
    }
    for(int i=0;i<=300000;i++)
        if(vis[i]==false)
            fa[i]=i+1;
    for(int i=n;i>=1;i--)
        if(type[i]==0)
            fa[op[i]]=op[i]+1;
        else if(op[i]>=size)
        {
            ans[i]=0x3f3f3f3f;
            for(int j=0;j*op[i]<=300000;j++)
                ans[i]=min(ans[i],GetFa(j*op[i])-j*op[i]);
        }

    for(int i=1;i<=n;i++)
        if(type[i]==1)
            printf("%d\n",ans[i]);
    return 0;
}

```