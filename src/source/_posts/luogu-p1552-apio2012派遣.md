---
title: '[Luogu P1552] [APIO2012]派遣'
tags:
  - 数据结构
id: '353'
categories:
  - - 左偏树
  - - 数据结构
date: 2019-03-15 09:47:38
updated: 2019-03-15 09:47:38
---

# 题面

[APIO2012 派遣](https://www.luogu.org/problemnew/show/P1552)

* * *

# Solution

这是一道左偏树的模板题，不会左偏树的可以[戳这里](https://www.goldenpotato.cn/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%8F%AF%E5%B9%B6%E5%A0%86%E5%B7%A6%E5%81%8F%E6%A0%91%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/) 显然，我们可以发现对于同一颗子树，我们想让取的人尽可能便宜，如果说目前为止取的价格超过$C$,就优先把贵的人先丢掉。 因此，我们考虑用左偏树来维护这个东西。对于每一个人，我们都建一颗以价格为关键字的大根堆。考虑从下往上合并，一旦堆的元素总和超过$C$就不断弹栈，弹到合法为止。然后我们算一下$l\_i\*sum$，取个最大值就好。 时间复杂度$O(nlogn)$ 就酱，我们就可以把这道题切掉啦(ﾉﾟ∀ﾟ)ﾉ

* * *

# Code

```cpp
//Luogu  P1552 [APIO2012]派遣
//Mar,15th,2019
//左偏树水题
#include<iostream>
#include<cstdio>
#include<vector>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1; c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=100000+100;
struct LST
{
    int fa[N],son[N][2],dis[N],w[N];
    long long sum[N],size[N];
    inline void update(int x)
    {
        sum[x]=sum[son[x][0]]+sum[son[x][1]]+w[x];
        size[x]=size[son[x][0]]+size[son[x][1]]+1;
    }
    int Merge(int x,int y)
    {
        if(x==0 or y==0) return x+y;
        if(w[x]>w[y]) swap(x,y);
        son[y][1]=Merge(x,son[y][1]);
        fa[son[y][1]]=y;
        update(y);
        if(dis[son[y][1]]>dis[son[y][0]]) 
            swap(son[y][0],son[y][1]);
        dis[y]=dis[son[y][1]]+1;
        return y;
    }
    int Pop(int x)
    {
        fa[x]=Merge(son[x][0],son[x][1]);
        return fa[x];
    }
}lst;
vector <int> e[N];
long long c[N],l[N],size[N],n,m;
int root[N];
long long ans;
void dfs(int now)
{
    for(int i=0;i<int(e[now].size());i++)
    {
        dfs(e[now][i]);
        root[now]=lst.Merge(root[now],root[e[now][i]]);
    }
    while(root[now]!=0 and lst.sum[root[now]]>m)
        root[now]=lst.Pop(root[now]);
    if(root[now]!=0)
        ans=max(ans,l[now]*lst.size[root[now]]);
}
int main()
{
    n=read(),m=read();
    for(int i=1;i<=n;i++)
        e[i].reserve(4);
    for(int i=1;i<=n;i++)
    {
        int fa=read();c[i]=read(),l[i]=read();
        e[fa].push_back(i);
        size[i]=1;
        root[i]=i,lst.w[i]=lst.sum[i]=c[i],lst.size[i]=1;
    }

    dfs(1);

    printf("%lld",ans);
    return 0;
}

```