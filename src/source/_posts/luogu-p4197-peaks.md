---
title: '[Luogu P4197] Peaks'
tags:
  - 数据结构
id: '341'
categories:
  - - splay
  - - 数据结构
date: 2019-03-07 11:43:01
updated: 2019-03-07 11:43:01
---

# 题面

[洛谷P4197](https://www.luogu.org/problemnew/show/P4197)

* * *

# Solution

这题的确是可以用库鲁斯卡尔重构树+主席树来搞，但是本蒟蒻太菜了并不会，因此只能给大家讲讲离线+splay启发式合并的搞法。 首先，我们考虑把询问离线下来并按限制从小到大排序。然后我们可以考虑把边一条一条插入到图里面去，直到某个询问的限制。这样子，问题就变为了询问某一个连通块的K小值，连通块可以合并。 这个问题就肥肠简单了，我们可以用各种各样的数据结构来处理，线段树合并，splay启发式合并都可以。 考虑到每个点期望加入$logn$次，时间复杂度为$O(nlogn)$

* * *

# Code

**我这题因为TLE调了很久，原因是我在查询的时候没有splay，导致势能分析失效，请各位读者引以为戒**

```cpp
//Luogu P4197 Peaks
//Mar,7th,2019
//splay启发式合并
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
const int N=100000+100;
const int M=50*N;
struct SPLAY
{
    #define root son[r][1]
    int son[M][2],size[M],cnt[M],fa[M],w[M],to,toUse[M],top;
    inline void update(int x)
    {
        size[x]=size[son[x][0]]+size[son[x][1]]+cnt[x];
    }
    inline void rotate(int x,int type)
    {
        int y=fa[x],z=fa[y];
        fa[x]=z,son[z][y==son[z][1]]=x;
        fa[son[x][type]]=y,son[y][!type]=son[x][type];
        fa[y]=x,son[x][type]=y;
        update(y),update(x);
    }
    void splay(int x,int to)
    {
        while(fa[x]!=to)
        {
            if(fa[fa[x]]!=to and x==son[fa[x]][fa[x]==son[fa[fa[x]]][1]])
                rotate(fa[x],x==son[fa[x]][0]),
                rotate(x,x==son[fa[x]][0]);
            else
                rotate(x,x==son[fa[x]][0]);
        }
    }
    int newNode()
    {
        if(top!=0)
        {
            son[toUse[top]][0]=son[toUse[top]][1]=0;
            size[toUse[top]]=fa[toUse[top]]=cnt[toUse[top]]=w[toUse[top]]=0;
            return toUse[top--];
        }
        return ++to;
    }
    void Insert(int num,int r)
    {
        if(root==0)
        {
            root=newNode(),fa[root]=r;
            w[root]=num,cnt[root]=1;
            update(root);
            return;
        }
        int now=root,last=r;
        while(now!=0)
        {
            if(w[now]==num)
            {
                cnt[now]++,update(now);
                splay(now,r);
                return;
            }
            last=now,now=son[now][num>w[now]];
        }
        now=newNode();
        fa[now]=last,son[last][num>w[last]]=now;
        w[now]=num,cnt[now]=1,update(now);
        splay(now,r);
    }
    int Query(int now,int r,int K)
    {
        if(size[son[now][1]]>=K)
            return Query(son[now][1],r,K);
        K-=(cnt[now]+size[son[now][1]]);
        if(K<=0)
        {
            splay(now,r);
            return w[now]; 
        }
        return Query(son[now][0],r,K);
    }
    void dfs(int now,int r)
    {
        if(now==0) return;
        for(int i=1;i<=cnt[now];i++)//有可能一个点上有多个数字
            Insert(w[now],r);
        dfs(son[now][0],r);
        dfs(son[now][1],r);
        toUse[++top]=now;
    }
    #undef root
}splay;
int fa[N],size[N],root[N];
int FindFather(int x)
{
    if(fa[x]==0) return x;
    return fa[x]=FindFather(fa[x]);
}
void Merge(int x,int y)
{
    if(FindFather(x)==FindFather(y)) return;
    if(size[FindFather(x)]>size[FindFather(y)]) swap(x,y);
    splay.dfs(splay.son[root[FindFather(x)]][1],root[FindFather(y)]);
    //cerr<<x<<" "<<y<<" "<<size[FindFather(x)]<<" "<<size[FindFather(y)]<<" "<<splay.size[splay.son[root[FindFather(y)]][1]]<<endl;
    size[FindFather(y)]+=size[FindFather(x)];
    fa[FindFather(x)]=FindFather(y);
}
struct edge
{
    int s,t,w;
    friend bool operator < (edge x,edge y)
    {
        return x.w<y.w;
    }
}e[M];
struct Query
{
    int x,K,w,ans,no;
    friend bool operator < (Query a,Query b)
    {
        return a.w<b.w;
    }
}query[M];
bool cmp(Query a,Query b)
{
    return a.no<b.no;
}
int Ask(int x,int K)
{
    if(size[FindFather(x)]<K) return -1;
    return splay.Query(splay.son[root[FindFather(x)]][1],root[FindFather(x)],K);
}
int n,m,q,h[N];
int main()
{
    int t=clock();
    freopen("4197.in","r",stdin);
    freopen("4197.out","w",stdout);

    n=read(),m=read(),q=read();
    for(int i=1;i<=n;i++)
        h[i]=read();

    for(int i=1;i<=n;i++)
        root[i]=splay.newNode(),size[i]=1;
    for(int i=1;i<=n;i++)
        splay.Insert(h[i],root[i]);
    for(int i=1;i<=m;i++)
        e[i].s=read(),e[i].t=read(),e[i].w=read();
    for(int i=1;i<=q;i++)
        query[i].x=read(),query[i].w=read(),query[i].K=read(),query[i].no=i;

    sort(e+1,e+1+m);
    sort(query+1,query+1+q);

    int w_to=1;
    for(int i=1;i<=q;i++)
    {
        //cerr<<i<<endl;
        for(;e[w_to].w<=query[i].w and w_to<=m;w_to++)
            Merge(e[w_to].s,e[w_to].t);
        query[i].ans=Ask(query[i].x,query[i].K);
    }

    sort(query+1,query+1+q,cmp);
    for(int i=1;i<=q;i++)
        printf("%d\n",query[i].ans);
    cerr<<clock()-t<<endl;
    return 0;
}

```