---
title: '[Luogu P1345] [USACO5.4]奶牛的电信Telecowmunication'
tags:
  - 图论
id: '126'
categories:
  - - 最小割
  - - 网络流
date: 2019-02-15 23:48:11
updated: 2019-02-15 23:48:11
---

# 题面

传送门:[洛谷](https://www.luogu.org/problemnew/show/P1345)

* * *

# Solution

这道题,需要一个小技巧了解决。 我相信很多像我这样接蒟蒻，看到这道题，不禁兴奋起来：“这道题是裸的割边，我会做！！！” 然后兴冲冲的打了个DINIC，交一发，80分。 所以说我们有时候还是太naive。 重新读题，会发现这题割的不是边，是点。这样还能80分，数据真水 所以说，我们需要一个割边转割点的小技巧。 我们可以考虑“拆点”，即把**一个点拆成两个点，中间连一条边权为1的边**。 **前一个点作为“入点”，别的点连边连入这里。** **后一个点作为“出点”，出去的边从这里出去。** 这样，只要**我们切断中间那条边，就可以等效于除去这个点** 如图： ![krgVaj.png](https://s2.ax1x.com/2019/02/15/krgVaj.png) 红色的边边权为1，黑色的边边权为inf。 **原点和汇点的内部边权为inf，因为显然这两个点不能删除。** 题面给的边删除没意义（因为我们要删点），所以也设为inf(事实上设为1也没问题，因为删除这条边的权值可以理解为删除了一个点) 至于怎么算割边，可以证明割边在数值上等于最大流（本蒟蒻不会证） 至于怎么求最大流........可以参考[这个博客](http://www.cnblogs.com/SYCstudio/p/7260613.html) 最后记得**双倍空间** 然后就OjbK了

* * *

# Code

```cpp
//Luogu P1345 [USACO5.4]奶牛的电信Telecowmunication
//June,3rd,2018
//割边转割点
#include<iostream>
#include<cstdio>
#include<cstring>
#include<vector>
#include<queue>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1; c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=200+10;
const int inf=0x3f3f3f3f;
struct road
{
    int to,w,rev;
    road (int A,int B,int C)
    {
        to=A,w=B,rev=C;
    }
};
vector <road> e[N];
int n,m,c1,c2,depth[N];
queue <int> dl;
bool bfs()
{
    memset(depth,0,sizeof depth);
    depth[c1]=1;
    dl.push(c1);
    while(dl.empty()==false)
    {
        int now=dl.front();
        dl.pop();
        for(int i=0;i<int(e[now].size());i++)
            if(e[now][i].w>0 and depth[e[now][i].to]==0)
            {
                depth[e[now][i].to]=depth[now]+1;
                dl.push(e[now][i].to);
            }
    }
    if(depth[c2]==0) return false;
    return true;
}
int dfs(int now,int f)
{
    if(now==c2) return f;
    int ans=0;
    for(int i=0;i<int(e[now].size());i++)
        if(e[now][i].w>0 and depth[e[now][i].to]==depth[now]+1)
        {
            int temp=dfs(e[now][i].to,min(f,e[now][i].w));
            e[now][i].w-=temp;
            e[e[now][i].to][e[now][i].rev].w+=temp;
            f-=temp,ans+=temp;
            if(f==0) break;
        }
    return ans;
}
int Dinic()
{
    int ans=0;
    while(bfs()==true)
        ans+=dfs(c1,inf);
    return ans;
}
inline void AddLine(int s,int t,int w)
{
    e[s].push_back(road(t,w,e[t].size()));
    e[t].push_back(road(s,0,e[s].size()-1));
}
int main()
{
    n=read(),m=read(),c1=read(),c2=read();
    for(int i=1;i<=n;i++) e[i].reserve(8);
    for(int i=1;i<=n;i++)
        if(i==c1 or i==c2)
            AddLine(i,i+n,inf);
        else
            AddLine(i,i+n,1);
    for(int i=1;i<=m;i++)
    {
        int s=read(),t=read();
        AddLine(s+n,t,inf);
        AddLine(t+n,s,inf);
    }

    printf("%d",Dinic());
    return 0;
}

//正解（C++）
```