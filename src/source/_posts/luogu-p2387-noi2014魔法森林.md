---
title: '[Luogu P2387] [NOI2014]魔法森林'
tags:
  - 数据结构
id: '203'
categories:
  - - LCT
  - - 数据结构
date: 2019-02-25 18:52:58
updated: 2019-02-25 18:52:58
---

# 题面

传送门：[洛谷](https://www.luogu.org/problemnew/show/P2387)

* * *

# Solution

这题的思想挺好的。 对于这种最大值最小类的问题，很自然的可以想到二分答案。很不幸的是，这题是双关键字排序的，我们怎么二分呢？ 先二分a再二分b？怎么看都布星啊。 那a+b作为关键字二分？也布星啊。 那咋搞啊？ 不如，我们换个想法，我们把其中一个关键字枚举，再看在这个关键字的限制下，另外一个尽可能小。 仔细想想，应该是能覆盖到所有的情况的。 所以说，我们可考虑这样做：我们先枚举a的大小（即所选的边的a必须小于这个值），在满足前者的条件下，使得从出发先到终点的路上的最大的b尽可能小。 对于第二个问题，是不是很眼熟？没错，这个问题就是著名的原题货车运输：我们要使得路径上经过的b值的最大值最小，这条路径一定是在以b为关键字的最小生成树上的（具体证明请移步货车运输那道题的题解）。 所以说，我们现在研究的问题就变为了如何快速的维护一个变化的最小生成树。 快速维护变化的树，我们可以很自然地想到使用LCT来维护。再结合我们之前维护动态最小生存树的知识：每加入一条边，它必定会连接两个点而形成一个环，我们要判断这条边是否会在新的生成树上，只需要看一下环上的最大的边权和这条边的关系就好了，如果这条边的边权比环上的最大值还要小，我们就可以把环上的那条最大的边断开，接上我们这条新的边。否则的话，这条边一定不会成为新的最小生成树的一部分。 所以说，我们的LCT只需要在每新加入一条边时，检查其连接的两端是否是联通的。如果不联通的话，加入这条边一定是没有问题的。如果联通的话，就把所连两端的链split出来，找到最大值，比较一下大小关系就好。 至于如何用LCT维护边，我的方法是用点来代替边，即一条边以一个有连向它的两个端点的边的点来替代。具体写法可以参照一下代码。 时间复杂度为$O(n∗logn∗logn)$

* * *

# Code

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int M=100000+100;
const int N=50000+100;
const int T=N+M;
struct road
{
    int s,t,a,b;
}e[M];
int n,m;
bool cmp(road x,road y)
{
    return x.a<y.a;
}
struct LCT
{
    int son[T][2],fa[T],lazy[T],MAX[T],num[T],mstack[T],top;
    inline void Update(int x)
    {
        MAX[x]=0;
        if(num[MAX[son[x][0]]]>=num[x] and num[MAX[son[x][0]]]>=num[MAX[son[x][1]]])
            MAX[x]=MAX[son[x][0]];
        if(num[MAX[son[x][1]]]>=num[x] and num[MAX[son[x][1]]]>=num[MAX[son[x][0]]])
            MAX[x]=MAX[son[x][1]];
        if(num[x]>=num[MAX[son[x][0]]] and num[x]>=num[MAX[son[x][1]]])
            MAX[x]=x;
    }
    inline void Mirror(int x)
    {
        lazy[x]=!lazy[x],swap(son[x][0],son[x][1]);
    }
    inline void PushDown(int x)
    {
        if(lazy[x]==0) return;
        lazy[x]=0;
        Mirror(son[x][0]),Mirror(son[x][1]);
    }
    inline bool IsRoot(int x)
    {
        return x!=son[fa[x]][0] and x!=son[fa[x]][1];
    }
    inline void Rotate(int x,int type)
    {
        int y=fa[x],z=fa[y];
        if(IsRoot(y)==false)    son[z][y==son[z][1]]=x;
        fa[x]=z;
        son[y][!type]=son[x][type],fa[son[x][type]]=y;
        son[x][type]=y,fa[y]=x;
        Update(y),Update(x);
    }
    inline void Splay(int x)
    {
        mstack[top=1]=x;
        for(int i=x;i!=0;i=fa[i])
            mstack[++top]=fa[i];
        for(int i=top;i>=1;i--)
            PushDown(mstack[i]);
        while(IsRoot(x)==false)
        {
            if(x==son[fa[x]][fa[x]==son[fa[fa[x]]][1]] and IsRoot(fa[x])==false)
                Rotate(fa[x],x==son[fa[x]][0]),
                Rotate(x,x==son[fa[x]][0]);
            else
                Rotate(x,x==son[fa[x]][0]);
        }
    }
    void Access(int x)
    {
        for(int t=0;x!=0;t=x,x=fa[x])
            Splay(x),son[x][1]=t,fa[t]=x,Update(x);
    }
    inline void MakeRoot(int x)
    {
        Access(x),Splay(x);
        Mirror(x);
    }
    inline int FindRoot(int x)
    {
        Access(x),Splay(x);
        while(son[x][0]!=0)
            PushDown(x),x=son[x][0];
        Splay(x);
        return x;
    }
    inline void Link(int x,int y)//x->y
    {
        if(FindRoot(x)==FindRoot(y)) return;
        MakeRoot(x);
        fa[x]=y;
    }
    inline void Split(int x,int y)//root:y
    {
        MakeRoot(x);
        Access(y),Splay(y);
    }
    inline void Cut(int x,int y)
    {
        Split(x,y);
        if(x==son[y][0] and fa[x]==y)
        {
            son[y][0]=fa[x]=0;
            Update(y);
        }    
    }
    inline int Query(int x,int y)
    {
        MakeRoot(x);
        Access(y),Splay(y);
        return MAX[y];
    }
    inline void AddLine(int x)
    {
        if(e[x].s==e[x].t) return;//自环
        num[n+x]=e[x].b,MAX[n+x]=n+x;
        if(FindRoot(e[x].s)!=FindRoot(e[x].t))
        {
            Link(n+x,e[x].s),Link(n+x,e[x].t);
            return ;
        }
        int t=Query(e[x].s,e[x].t);
        if(num[n+x]<num[t])
        {
            Cut(e[t-n].s,t);
            Cut(e[t-n].t,t);
            Link(e[x].s,n+x);
            Link(e[x].t,n+x);
        }
    }
    inline int Query2()
    {
        if(FindRoot(n)!=FindRoot(1)) return 0x3f3f3f3f;
        return num[Query(1,n)];
    }
}lct;
int main()
{
    n=read(),m=read();
    for(int i=1;i<=m;i++)
        e[i].s=read(),e[i].t=read(),e[i].a=read(),e[i].b=read();

    sort(e+1,e+1+m,cmp);
    int ans=0x3f3f3f3f;
    for(int i=1;i<=m;i++)
    {
        lct.AddLine(i);
        ans=min(ans,e[i].a+lct.Query2());
    }

    if(ans==0x3f3f3f3f)
        printf("-1"); 
    else
        printf("%d",ans);
    return 0;
}
```