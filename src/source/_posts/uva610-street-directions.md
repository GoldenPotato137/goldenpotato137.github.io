---
title: UVA610 Street Directions
tags:
  - 图论
id: '417'
categories:
  - - 图论
  - - 边双/点双
date: 2019-04-09 09:31:53
updated: 2019-04-09 09:31:53
---

## 题面

[UVA610 Street Directions](https://www.luogu.org/problemnew/show/UVA610)


## Solution

先来解释一下题面意思：我们现在有一个联通的无向图，**我们要把整个图改造为有向图，在保证强连通的情况下使得双向边尽可能少**。 

我们不妨思考一下：如果一条双向边被我们改造为了单向边，会导致某一个方向上的断开。因此，我们先对原图做边双缩点，**桥边是不可能被改造为单向边的**（因为改造后直接导致边双间不能互相联通）。

除了桥边之外，其他边都是可以改造为单向边的。 

因此，**我们可以在每一个边双里面做一个dfs来连单向边，桥边直接连上双向边即可**。 

时间复杂度$O(n)$ 就酱，这题就被我们切掉啦ヾ(●´∀｀●)

* * *

# Code

```cpp
//Luogu  UVA610 Street Directions
//Apr,9th,2019
//Tarjan求点双
#include<iostream>
#include<cstdio>
#include<vector>
#include<cstring>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=1000+10;
vector <int> e[N],e2[N];
int dfn[N],dfn_to,low[N],mstack[N],top,belong[N],cnt;
bool vis[N],InStack[N];
void Tarjan(int now,int father)
{
    vis[now]=InStack[now]=true;
    mstack[++top]=now;
    dfn[now]=low[now]=++dfn_to;
    for(int i=0;i<int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            Tarjan(e[now][i],now);
            low[now]=min(low[now],low[e[now][i]]);
        }
        else if(e[now][i]!=father and InStack[e[now][i]]==true)
            low[now]=min(low[now],dfn[e[now][i]]);
    if(low[now]==dfn[now])
    {
        cnt++;
        while(mstack[top+1]!=now)
            InStack[mstack[top]]=false,
            belong[mstack[top--]]=cnt;
    }
}
int Find(int now,int x)
{
    for(int i=0;i<int(e[now].size());i++)
        if(e[now][i]==x)
            return i;
    return -1;
}
void dfs2(int now)
{
    if(vis[now]==true) return;
    vis[now]=true;
    for(int i=0;i<int(e[now].size());i++)
        if(belong[e[now][i]]==belong[now])
        {
            printf("%d %d\n",now,e[now][i]);
            e[e[now][i]][Find(e[now][i],now)]=0;
            dfs2(e[now][i]);
        }
}
int n,m;
int main()
{
    for(int o=1;;o++)
    {
        n=read(),m=read();
        if(n==0 and m==0) break;

        for(int i=0;i<=n;i++)
            e[i].clear(),e2[i].clear();
        for(int i=1;i<=n;i++)
            e[i].reserve(4),e2[i].reserve(4);
        for(int i=1;i<=m;i++)
        {
            int s=read(),t=read();
            e[s].push_back(t);
            e[t].push_back(s);
        }

        memset(vis,0,sizeof vis);
        memset(mstack,0,sizeof mstack);
        dfn_to=cnt=0;
        Tarjan(1,0);

        printf("%d\n\n",o);
        memset(vis,0,sizeof vis);
        for(int i=1;i<=n;i++)
            dfs2(i);
        for(int i=1;i<=n;i++)
            for(int j=0;j<int(e[i].size());j++)
                if(belong[i]!=belong[e[i][j]] and e[i][j]!=0)
                    printf("%d %d\n",i,e[i][j]);
        printf("#\n");
    }
    return 0;
}

```