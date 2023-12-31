---
title: '[LuoguP2147] [SDOI2008]洞穴勘测'
tags:
  - 数据结构
id: '207'
categories:
  - - LCT
  - - 数据结构
date: 2019-02-25 19:07:56
---

# 题面

传送门：[洛谷](https://www.luogu.org/problemnew/show/P2147)

* * *

# Solution

这题...... 我们可以发现题目要求我们维护一个动态森林，而且只查询连通性.... 显然LCT模板题啊，关于LCT玩法，可以[猛戳这里学习](https://www.goldenpotato.cn/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/lct%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/)

* * *

# Code

```cpp
#include<iostream>
#include<cstdio>
#include<vector>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=10000+100;
struct LCT
{
    int fa[N],son[N][2],lazy[N],mstack[N],top;
    inline bool isRoot(int x)
    {
        return x!=son[fa[x]][0]&&x!=son[fa[x]][1];
    }
    inline void pushdown(int x)
    {
        if(lazy[x]==0) return;
        lazy[son[x][0]]=!lazy[son[x][0]],swap(son[son[x][0]][0],son[son[x][0]][1]);
        lazy[son[x][1]]=!lazy[son[x][1]],swap(son[son[x][1]][0],son[son[x][1]][1]);
        lazy[x]=0;
    }
    inline void rotate(int x,int type)
    {
        int y=fa[x],z=fa[y];
        if(isRoot(y)==false)    son[z][y==son[z][1]]=x;
        fa[x]=z;
        fa[son[x][type]]=y,son[y][!type]=son[x][type];
        son[x][type]=y,fa[y]=x;
    }
    void splay(int x)
    {
        mstack[top=1]=x;
        for(int i=x;isRoot(i)==false;i=fa[i])
            mstack[++top]=fa[i];
        for(int i=top;i>=1;i--)
            pushdown(mstack[i]);
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
            splay(x),son[x][1]=t;
    }
    void MakeRoot(int x)
    {
        Access(x);
        splay(x);
        lazy[x]=!lazy[x],swap(son[x][0],son[x][1]);
    }
    int FindRoot(int x)
    {
        Access(x),splay(x);
        while(son[x][0]!=0)
            pushdown(x),x=son[x][0];
        return x;    
    }
    void Link(int x,int y)
    {
        MakeRoot(x);
        fa[x]=y;
    }
    void split(int x,int y)
    {
        MakeRoot(x);
        Access(y),splay(y);
    }
    void Cut(int x,int y)
    {
        split(x,y);
        son[y][0]=fa[x]=0;
    }
}lct;
int n,m;
int main()
{
    n=read(),m=read();

    char OP[15];
    for(int i=1;i<=m;i++)
    {
        scanf("%s",OP+1);
        int u=read(),v=read();
        if(OP[1]=='C')
            lct.Link(u,v);
        else if(OP[1]=='D')
            lct.Cut(u,v);
        else
        {
            if(lct.FindRoot(u)==lct.FindRoot(v))
                printf("Yes\n");
            else
                printf("No\n");
        }
    }
    return 0;
}
```