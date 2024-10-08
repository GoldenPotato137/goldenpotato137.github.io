---
title: '[Luogu P5227] [AHOI2013] 连通图'
tags:
  - 数据结构
id: '368'
categories:
  - - 并查集
  - - 数据结构
  - - 线段树
date: 2019-03-22 11:50:13
updated: 2019-03-22 11:50:13
---

# 题面

[P5227 \[AHOI2013\]连通图](https://www.luogu.org/problemnew/show/P5227)

* * *

# Solution

这题可以离线，因此我们可以考虑用线段树分治维护动态图连通性来搞。 这题我们考虑离线下来搞。离线之后，我们会发现，**某条边会在某些询问区间中出现。** 考虑**以询问的编号为下标建线段树，我们把每一条边出现的时间段全部加到线段树里面去。** 接下来，直接**在线段树上跑dfs**,每到一个区间，就把这个区间里面存的边通通在并查集中连上；每完成一个区间，就把这个区间连上的边通通取消（类似于回溯）。 这样搞，我们每次到一个叶子节点的时候，这个叶子节点代表的询问上所要连的边一定已经全部连上了，直接在并查集中查询任意节点的父亲的$size$是否为$n$即可。 因为我们这里有撤销（回溯）操作，因此必需使用\*\*按秩合并的并查集。我们只需要开一个栈，把每次修改的fa的节点记录下来即可完成撤销的操作。 时间复杂度$O(nlog^2n)$ 吗？ 问题是这玩意能过啊..... 因此，我们的时间复杂度是$O($能过$)$ 就酱，我们又切掉一道题啦。(ﾉﾟ∀ﾟ)ﾉ

* * *

# Code

```cpp
//Luogu P5227 [AHOI2013]连通图
//Mar,22ed,2019
//线段树分治离线维护动态图连通性
#include<iostream>
#include<cstdio>
#include<vector>
#include<ctime>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=100000+100;
const int M=200000+100;
int n,m,q,p,ans[M];
struct UnF
{   
    int size[N],fa[N],mstack[M],top;
    void Init()
    {
        for(int i=1;i<=n;i++)
            size[i]=1;
    }
    int FindFather(int x)
    {
        if(fa[x]==0) return x;
        return FindFather(fa[x]);
    }
    void Link(int x,int y)
    {
        mstack[++top]=0;
        int fa_x=FindFather(x),fa_y=FindFather(y);
        if(size[fa_x]>size[fa_y]) 
            swap(x,y),swap(fa_x,fa_y);
        if(fa_x==fa_y) return;
        mstack[top]=fa_x;   
        fa[fa_x]=fa_y,size[fa_y]+=size[fa_x];
    }
    int Query()
    {
        if(size[FindFather(1)]==n) return true;
        return false;
    }
    void Undo()
    {
        if(mstack[top]==0)
        {
            top--;
            return;
        }
        size[fa[mstack[top]]]-=size[mstack[top]];
        fa[mstack[top]]=0;
        top--;
    }
}unf;
struct OP
{
    int s,t,id;
}op2[M*20],e[M];
struct SegmentTree
{
    #define mid ((now_l+now_r)>>1)
    #define lson (now<<1)
    #define rson (now<<11)
    vector <int> w[M<<2];
    void Insert(int l,int r,int x,int now,int now_l,int now_r)
    {
        if(now_l>=l and now_r<=r)
        {
            w[now].push_back(x);
            return;
        }
        if(l<=mid) Insert(l,r,x,lson,now_l,mid);
        if(r>mid) Insert(l,r,x,rson,mid+1,now_r);
    }
    void dfs(int now,int now_l,int now_r)
    {
        if(now_l>now_r) return;
        for(int i=0;i<int(w[now].size());i++)
            unf.Link(e[w[now][i]].s,e[w[now][i]].t);
        if(now_l==now_r)
            ans[now_l]=unf.Query();
        else
        {
            dfs(lson,now_l,mid);
            dfs(rson,mid+1,now_r);
        }
        for(int i=0;i<int(w[now].size());i++)
            unf.Undo();
    }
    #undef mid
    #undef lson
    #undef rson
}sgt;
int last[M],K;
int main()
{
    n=read(),m=read();
    for(int i=1;i<=m;i++)
        e[i].s=read(),e[i].t=read(),last[i]=1;
    K=read();
    for(int i=1;i<=K;i++)
    {
        int c=read();
        for(int j=1;j<=c;j++)
        {
            int x=read();
            op2[++p].s=last[x],op2[p].t=i-1,op2[p].id=x;
            last[x]=i+1;
        }
    }
    for(int i=1;i<=m;i++)
        if(last[i]!=K+1)
            op2[++p].s=last[i],op2[p].t=K,op2[p].id=i;

    for(int i=1;i<=p;i++)
        if(op2[i].s<=op2[i].t)
        {
            sgt.Insert(op2[i].s,op2[i].t,op2[i].id,1,1,K);
            //cerr<<op2[i].s<<" "<<op2[i].t<<" "<<op2[i].id<<endl;
        }

    unf.Init();
    sgt.dfs(1,1,K);

    for(int i=1;i<=K;i++)
        if(ans[i]==0)
            printf("Disconnected\n");
        else
            printf("Connected\n");
    return 0;
}

```