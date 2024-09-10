---
title: '[Luogu P1462] 通往奥格瑞玛的道路'
tags:
  - 二分/二分答案
  - 最短路
id: '109'
categories:
  - - 二分/二分答案
  - - 其他
  - - 图论
  - - 最短路径
date: 2019-02-14 16:17:51
updated: 2019-02-14 16:17:51
---

# 题面

传送门:https://www.luogu.org/problemnew/show/P1462

* * *

# Solution

这道题如果去除掉经过城市的收费.那么就是裸的最短路 但是题目要求经过城市中最多的一次性收费的最小值,也就是说让经过的最大值尽可能小 那我们可以考虑二分这个最大值 一切收费大于我们二分的值的城市统统不走 在最短路那里改一下就好了 然后就OjbK了 时间复杂度 $O(n\*logn\*logb)$

* * *

# Code

```cpp
//Luogu P1462 通往奥格瑞玛的道路
//May,27th,2018
//最短路+二分答案
#include<iostream>
#include<cstdio>
#include<vector>
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
const int N=10000+100;
const int V_MAX=1000000000;
struct road
{
    int to,dis;
    friend bool operator  < (road A,road B)
    {
        return A.dis > B.dis;
    }
};
vector <road> e[N]; 
priority_queue <road,vector<road> > dl;
int dis[N],vis[N],n,m,b,v[N];
void dj(int lim)
{
    while(dl.empty()==false) dl.pop();
    memset(dis,0x3f,sizeof dis);
    memset(vis,0,sizeof vis);
    if(v[1]>lim) return;
    road now;
    now.to=1,now.dis=0,dis[1]=0;
    dl.push(now);
    while(dl.empty()==false)
    {
        now=dl.top();
        dl.pop();
        if(vis[now.to]==true) continue;
        vis[now.to]=true;
        for(int i=0;i<int(e[now.to].size());i++)
            if(dis[e[now.to][i].to] > now.dis+e[now.to][i].dis and v[e[now.to][i].to]<=lim)
            {
                dis[e[now.to][i].to]=now.dis+e[now.to][i].dis;
                road temp;
                temp.to=e[now.to][i].to,temp.dis=dis[e[now.to][i].to];
                dl.push(temp);
            }
    }
}
int main()
{
    n=read(),m=read(),b=read();
    for(int i=1;i<=n;i++)
    {
        e[i].reserve(4);
        v[i]=read();
    }
    for(int i=1;i<=m;i++)
    {
        int a=read(),b=read(),c=read();
        road temp;
        temp.dis=c,temp.to=b;
        e[a].push_back(temp);
        temp.to=a;
        e[b].push_back(temp);
    }

    int L=0,R=V_MAX,ans=0x3f3f3f3f;
    while(L<=R)
    {
        int mid=(L+R)/2;
        dj(mid);
        if(dis[n]<=b)
        {
            ans=min(ans,mid);
            R=mid-1;
        }
        else
            L=mid+1;
    }

    if(ans!=0x3f3f3f3f)
        printf("%d",ans);
    else
        printf("AFK");
    return 0;
}
```