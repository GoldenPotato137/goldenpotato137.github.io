---
title: '[Luogu P4768] [NOI2018]归程'
tags:
  - 数据结构
id: '345'
categories:
  - - Kruskal重构树
  - - 倍增
  - - 数据结构
date: 2019-03-07 17:55:50
updated: 2019-03-07 17:55:50
---

# 题面

[\[Luogu P4768\] \[NOI2018\]归程](https://www.luogu.org/problemnew/show/P4768)

* * *

# Solution

这题可能要用到Kruskal重构树的相关知识，如果有需求的同学可以[看这里](https://www.goldenpotato.cn/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/kruskal%E9%87%8D%E6%9E%84%E6%A0%91%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/) 首先，根据我们之前在运输计划那道题的经验，我们会发现我们开车能经过的边一定在**以海拔为关键字的最大生成树上**。 根据Kruskal重构树的性质：**Kruskal重构树是一个堆**，我们可以考虑这样做： 我们先把Kruskal重构树按每条路的海拔从大到小建出来，那么**从某个点出发能开车到达的点一定是这个点的某个祖先的子树内的所有的点**，这个很好理解，因为Kruskal重构树是一个堆，那么堆之内的点的路一定>堆顶，堆外的点一定<=堆顶 所以说，我们只需要先把每个点到**1号节点的最短路**先求出来，每次我们**从出发点向上倍增**，找到刚好>积水线的点，然后找到这个点的子树内距离1最短的点即可，这个用简单的树形DP即可完成。 时间复杂度$O(mlogn)$ 就酱，这题就被我们切掉啦~(\*´ﾟ∀ﾟ｀)ﾉ

* * *

# Code

```cpp
#include<iostream>
#include<cstdio>
#include<vector>
#include<algorithm>
#include<queue>
#include<cstring>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=2*200000+2000;
const int M=400000+1000;
struct EDGE
{
    int s,to,l,h;
}e1[M];
vector <EDGE> e2[N];//e2需要初始化
int n,m;
long long dis[N];
struct SIT
{
    int to;
    long long w;
    SIT (int x,long long y)
    {
        to=x,w=y;
    }
    friend bool operator < (SIT x,SIT y)
    {
        return x.w>y.w;
    }
};
priority_queue <SIT> mqueue;
void dj()
{
    static bool vis[N];
    memset(vis,0,sizeof vis);
    memset(dis,0x3f,sizeof dis);
    dis[1]=0,mqueue.push(SIT(1,0));
    while(mqueue.empty()==false)
    {
        int now=mqueue.top().to;
        mqueue.pop();
        if(vis[now]==true) continue;
        vis[now]=true;
        for(int i=0;i<int(e2[now].size());i++)
            if(dis[e2[now][i].to] > dis[now]+e2[now][i].l)
            {
                dis[e2[now][i].to]=dis[now]+e2[now][i].l;
                mqueue.push(SIT(e2[now][i].to,dis[e2[now][i].to]));
            }
    }
}
int fa[N][21],depth[N],cnt;//fa需要初始化
vector <int> e3[N];//e3需要初始化
int MIN[N],sl[N];//存放距离最小值位置
bool cmp(EDGE x,EDGE y)
{
    return x.h>y.h;
}
int FindFather(int x)
{
    if(fa[x][0]==0) return x;
    return fa[x][0]=FindFather(fa[x][0]);
}
void Kruskal()
{
    for(int i=1;i<=n;i++)
        MIN[i]=i;
    sort(e1+1,e1+1+m,cmp);
    cnt=n;
    for(int i=1;i<=m;i++)
    {
        int fa1=FindFather(e1[i].s),fa2=FindFather(e1[i].to);
        if(fa1==fa2) continue;
        fa[fa1][0]=fa[fa2][0]=++cnt;
        sl[cnt]=e1[i].h,MIN[cnt]=cnt;
        e3[cnt].push_back(fa1),e3[cnt].push_back(fa2);
    }
    fa[cnt][0]=cnt;
}
void dfs(int now)
{
    for(int i=1;i<=20;i++)
        fa[now][i]=fa[fa[now][i-1]][i-1];
    for(int i=0;i<int(e3[now].size());i++)
    {
        depth[e3[now][i]]=depth[now]+1;
        fa[e3[now][i]][0]=now;
        dfs(e3[now][i]);
        if(dis[MIN[e3[now][i]]]<dis[MIN[now]])
            MIN[now]=MIN[e3[now][i]];
    }
}
int Query(int x,int h)
{
    for(int i=20;i>=0;i--)
        if(sl[fa[x][i]]>h)
            x=fa[x][i];
    return dis[MIN[x]];
}
int main()
{
    int T=read();
    for(;T>0;T--)
    {
        n=read(),m=read();

        for(int i=0;i<=2*n+10;i++)
            e2[i].clear(),e2[i].reserve(4);
        memset(fa,0,sizeof fa);
        for(int i=0;i<=2*n+10;i++)
            e3[i].clear(),e3[i].reserve(4);

        for(int i=1;i<=m;i++)
        {
            e1[i].s=read(),e1[i].to=read(),e1[i].l=read(),e1[i].h=read();
            e2[e1[i].s].push_back(e1[i]);
            swap(e1[i].s,e1[i].to);
            e2[e1[i].s].push_back(e1[i]);
        }

        dj();
        Kruskal();
        dfs(cnt);

        long long ans=0,q=read(),K=read(),S=read();
        for(int i=1;i<=q;i++)
        {
            long long v=read(),p=read();
            v=(v+K*ans-1)%n+1,p=(p+K*ans)%(S+1);
            printf("%lld\n",ans=Query(v,p));
        }
    }
    return 0;
}

```