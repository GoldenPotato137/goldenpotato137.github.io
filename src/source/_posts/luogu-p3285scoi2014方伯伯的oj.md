---
title: '[Luogu  P3285][SCOI2014]方伯伯的OJ'
tags:
  - 数据结构
id: '349'
categories:
  - - splay
  - - 数据结构
  - - 线段树
date: 2019-03-11 14:59:29
updated: 2019-03-11 14:59:29
---

# 题面

[洛谷P3285](https://www.luogu.org/problemnew/show/P3285)

* * *

# Solution

这是一道数据结构大暴力题。 我们可以很显然的发现对于询问排名，**维护排名的操作，我们可以直接上一个维护下标的splay**。 因为点的数量奇多，这让我们回想起NOIP2017 列队，**我们可以用“splay 动态开点”这样的操作来解决**，即一开始我们把所有信息全部压到一个点里面去（即一个点代表一段区间），需要的时候再用“拆点”把点拆开。 问题是这破题很让人讨厌地出了两个基于编号的操作。因为我们的splay是以下标做为权值来的，失去了维护编号的能力。 怎么办呢？我们可以考虑直接记录每个编号的点在splay中的下标。很不幸的是，这里的点的编号十分巨大，没法直接开桶来存。 但是，因为我们splay维护的是一个一个区间，我们可以考虑**开一颗动态开点的线段树，然后在拆点/改编号的时候暴力区间维护一下即可。** 时间复杂度$O(mlogn)$ 就酱，这题我们就切掉啦٩(๑>◡<๑)۶

* * *

# Code

```cpp
//Luogu P3285 [SCOI2014]方伯伯的OJ
//Mar,11th,2019
//动态开点splay+动态开点线段树鬼畜题
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
const int N=100000*2+100;
const int M=200000000;
struct SegmentTree
{
    #define mid ((now_l+now_r)>>1)
    int son[N*20][2],w[N*20],to;
    void Change(int l,int r,int num,int now,int now_l,int now_r)
    {
        if(now_l>=l and now_r<=r)
        {
            w[now]=num;
            return;
        }
        if(l<=mid)
        {
            if(son[now][0]==0) son[now][0]=++to,w[to]=w[now];
            Change(l,r,num,son[now][0],now_l,mid);
        }
        if(r>mid)
        {
            if(son[now][1]==0) son[now][1]=++to,w[to]=w[now];
            Change(l,r,num,son[now][1],mid+1,now_r);
        }
    }
    int Query(int x,int now,int now_l,int now_r)
    {
        if(now_l==now_r) return w[now];
        if(x<=mid)
        {
            if(son[now][0]==0) return w[now];
            else return Query(x,son[now][0],now_l,mid);
        }
        else
        {
            if(son[now][1]==0) return w[now];
            else return Query(x,son[now][1],mid+1,now_r);
        }
    }
    #undef mid
}sgt;//编号->下标
struct SPLAY
{
    #define root son[0][1]
    int son[N][2],fa[N],cnt[N],no[N],size[N],to;
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
                rotate(fa[x],x==son[fa[x]][0]);
            rotate(x,x==son[fa[x]][0]);
        }
    }
    void split(int x,int K)//传入下标
    {
        splay(x,0);
        int t1=son[x][0],t2=son[x][1];
        while(son[t1][1]!=0) t1=son[t1][1];
        while(son[t2][0]!=0) t2=son[t2][0];
        splay(t1,0);
        splay(t2,root);

        if(K!=1)
        {
            son[x][0]=++to,fa[to]=x;
            no[to]=no[x],size[to]=cnt[to]=K-1;
            sgt.Change(no[to],no[to]+cnt[to]-1,to,1,1,M);
        }
        if(K!=cnt[x])
        {
            son[x][1]=++to,fa[to]=x;
            no[to]=no[x]+K,size[to]=cnt[to]=cnt[x]-K;
            sgt.Change(no[to],no[to]+cnt[to]-1,to,1,1,M);
        }
        no[x]=no[x]+K-1,cnt[x]=1;
    }
    int GetKth(int x,int K)//返回下标
    {
        if(size[son[x][0]]>=K) 
            return GetKth(son[x][0],K);
        K-=size[son[x][0]];
        if(K<=cnt[x])
        {
            if(cnt[x]==1)
            {
                splay(x,0);
                return x;
            }
            split(x,K);
            splay(x,0);
            return x;
        }
        K-=cnt[x];
        return GetKth(son[x][1],K);
    }
    int Change(int x,bool type)//传入编号
    {
        int t=sgt.Query(x,1,1,M),ans;
        splay(t,0);
        ans=size[son[t][0]]+x-no[t]+1;
        split(t,x-no[t]+1);
        if(son[t][0]!=0 or son[t][1]!=0)
            split(t,1);
        if(type==0)
        {
            son[fa[t]][t==son[fa[t]][1]]=0;
            update(fa[t]);
            splay(1,0);
            int now=son[root][1];
            while(son[now][0]!=0) now=son[now][0];
            son[now][0]=t,fa[t]=now;
            update(now),splay(now,0);
        }
        else
        {
            son[fa[t]][t==son[fa[t]][1]]=0;
            update(fa[t]);
            splay(3,0);
            int now=son[root][0];
            while(son[now][1]!=0) now=son[now][1];
            son[now][1]=t,fa[t]=now;
            update(now),splay(now,0);
        }
        return ans;
    }
    int Change2(int x,int y)//传入编号
    {
        int t=sgt.Query(x,1,1,M);
        split(t,x-no[t]+1);
        sgt.Change(y,y,t,1,1,M);
        no[t]=y;
        splay(t,0);
        return size[son[t][0]]+1;
    }
    int Query(int K)
    {
        return no[GetKth(root,K)];
    }
    void Init(int n)
    {
        root=++to,fa[root]=0;
        son[root][1]=++to,fa[to]=root,cnt[to]=size[to]=n,no[to]=1;
        sgt.to=1;
        sgt.Change(1,n,to,1,1,M);
        son[to][1]=to+1,fa[to+1]=to,to++;
        update(to),update(to-1),update(root);
    }
    void Print(int now)
    {
        if(now==0) return;
        Print(son[now][0]);
        cout<<"no:"<<now<<" ["<<no[now]<<","<<no[now]+cnt[now]-1<<"] "<<"size:"<<size[now]<<" cnt:"<<cnt[now]<<" sonl&r:"<<son[now][0]<<" "<<son[now][1]<<endl;
        Print(son[now][1]);
    }
    #undef root
}splay;
int n,m;
int main()
{
    n=read(),m=read();

    splay.Init(n);
    int ans=0;
    for(int i=1;i<=m;i++)
    {
        int op=read(),x=read()-ans;
        if(op==1)
        {
            int y=read()-ans;
            printf("%d\n",ans=splay.Change2(x,y));
        }
        else if(op==2)
            printf("%d\n",ans=splay.Change(x,0));
        else if(op==3)
            printf("%d\n",ans=splay.Change(x,1));
        else
            printf("%d\n",ans=splay.Query(x));
        //ans=0;//RTC
        //splay.Print(splay.son[0][1]);
        //cerr<<endl;
    }
    return 0;
}

```