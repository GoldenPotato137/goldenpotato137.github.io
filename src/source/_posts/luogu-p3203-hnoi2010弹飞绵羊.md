---
title: '[Luogu P3203] [HNOI2010]弹飞绵羊'
tags:
  - 数据结构
id: '209'
categories:
  - - LCT
  - - 数据结构
date: 2019-02-25 19:10:25
updated: 2019-02-25 19:10:25
---

# 题面

传送门：[洛谷](https://www.luogu.org/problemnew/show/P3203)

* * *

# Solution

这题其实是有类似模型的。 我们先考虑不修改怎么写。考虑这样做：每个点向它跳到的点连一条边，最后肯定会连成一颗以n+1为根的树（我们拿n+1代表被弹出去了）。题目所问的即是某个点到树根的链的长度。 那么，如果我们加上修改，显然，某个点连向的点会发生改变。对于一个能修改边的树，我们可以很自然的想到用LCT维护之。 至于怎么求某条链的长度呢？这也是LCT的基础操作之一，我们只需要先MakeRoot（n+1），然后再Acess（x），splay（x）就可以把这条链拉出来了，我们维护splay的size就好。 如果您看不懂上面这句话，请戳我来学习[LCT与原树的对应关系](https://www.goldenpotato.cn/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/lct%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/)

# Code

```cpp
//Luogu P3203 [HNOI2010]弹飞绵羊
//Jan,9th,2018
//LCT模板题II
#include<iostream>
#include<cstdio>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=200000+100;
int n,m;
struct LCT
{
    int son[N][2],fa[N],lazy[N],mstack[N],top,size[N];
    inline bool isRoot(int x)
    {
        return x!=son[fa[x]][0] && x!=son[fa[x]][1];
    }
    inline void update(int x)
    {
        size[x]=size[son[x][0]]+size[son[x][1]]+1;
    }
    inline void mirror(int x)
    {
        lazy[x]=!lazy[x],swap(son[x][0],son[x][1]);
    }
    inline void pushDown(int x)
    {
        if(lazy[x]==0) return;
        lazy[x]=0;
        mirror(son[x][0]),mirror(son[x][1]);
    }
    inline void rotate(int x,int type)
    {
        int y=fa[x],z=fa[y];
        if(isRoot(y)==false)
            son[z][y==son[z][1]]=x;
        fa[x]=z;
        son[y][!type]=son[x][type],fa[son[x][type]]=y;
        son[x][type]=y,fa[y]=x;
        update(y),update(x);
    }
    void splay(int x)
    {
        mstack[top=1]=x;
        for(int i=x;isRoot(i)==false;i=fa[i])
            mstack[++top]=fa[i];
        for(int i=top;i>=1;i--)
            pushDown(mstack[i]);
        while(isRoot(x)==false)
        {
            if(x==son[fa[x]][fa[x]==son[fa[fa[x]]][1]] and isRoot(fa[x])==false)
                rotate(fa[x],x==son[fa[x]][0]),
                rotate(x,x==son[fa[x]][0]);
            else
                rotate(x,x==son[fa[x]][0]);
        }
    }
    void Access(int x)
    {
        for(int t=0;x!=0;t=x,x=fa[x])
            splay(x),son[x][1]=t,fa[t]=x,update(x);
    }
    inline void MakeRoot(int x)
    {
        Access(x),splay(x);
        mirror(x);
    }
    inline void Link(int x,int y)//x翻为根连向y
    {
        MakeRoot(x);
        fa[x]=y;
    }
    inline void split(int x,int y)//y为根
    {
        MakeRoot(x);
        Access(y),splay(y);
    }
    inline void Cut(int x,int y)
    {
        split(x,y);
        son[y][0]=fa[x]=0;
        update(y);
    }
    int Query(int x)
    {
        MakeRoot(n+1);
        Access(x),splay(x);
        return size[x]-1;
    }
}lct;
int q[N];
int main()
{
    n=read();
    for(int i=1;i<=n;i++)
        q[i]=read();

    for(int i=n;i>=1;i--)
        if(i+q[i]>n)
            lct.Link(i,n+1);
        else
            lct.Link(i,i+q[i]);
    m=read();
    for(int i=1;i<=m;i++)
    {
        int op=read();
        if(op==1)
        {
            int x=read()+1;
            printf("%d\n",lct.Query(x));
        }
        else
        {
            int x=read()+1,K=read();
            if(x+q[x]>n)
                lct.Cut(x,n+1);
            else
                lct.Cut(x,x+q[x]);
            q[x]=K;
            if(x+q[x]<=n)
                lct.Link(x,x+q[x]);
            else
                lct.Link(x,n+1);
        }
    }
    return 0;
}
```