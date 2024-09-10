---
title: '[Luogu P3174] [HAOI2009]毛毛虫'
tags:
  - DP
id: '348'
categories:
  - - 动态规划
  - - 数位DP
date: 2019-03-11 14:47:01
updated: 2019-03-11 14:47:01
---

# 题面

[洛谷P3174](https://www.luogu.org/problemnew/show/P3174)

* * *

# Solution

我们不难发现，一条“毛毛虫”一定是由一条主链外加主链的点所连到的点构成的。 那既然是一条链，它的形态无外乎以下两种： ![ACQopn.png](https://s2.ax1x.com/2019/03/11/ACQopn.png) 因此，我们可以**直接枚举最上面的那个点，他做为根会产生的最大的答案即为其孩子的最大答案与次大答案之和再减去多算的一小部分即可**。(具体转移可以看看代码，要做点简单的分类讨论) 时间复杂度$O(n)$ 就酱，这题我们就切掉啦(\*´ﾟ∀ﾟ｀)ﾉ

* * *

# Code

```cpp
//Luogu P3174 [HAOI2009]毛毛虫
//Mar,10th,2019
//树形DP
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
const int N=300000+100;
vector <int> e[N];
int n,m;
bool vis[N];
int f1[N],f2[N],ans;
void dfs(int now)
{
    vis[now]=true;
    f1[now]=e[now].size()+1;
    for(int i=0;i<int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            dfs(e[now][i]);
            if(f1[e[now][i]]+int(e[now].size())-1 >= f1[now])
            {
                f2[now]=f1[now];
                f1[now]=f1[e[now][i]]+e[now].size()-1;
            }
            else if(f1[e[now][i]]+int(e[now].size())-1 > f2[now])
                f2[now]=f1[e[now][i]]+e[now].size()-1;
        }

    if(f2[now]==0)
        ans=max(ans,f1[now]);
    else
        ans=max(ans,f1[now]+f2[now]-int(e[now].size())-1);
}
int main()
{
    n=read(),m=read();
    for(int i=1;i<=n;i++)
        e[i].reserve(4);
    for(int i=1;i<=m;i++)
    {
        int s=read(),t=read();
        e[s].push_back(t);
        e[t].push_back(s);
    }

    dfs(1);

    printf("%d",ans);
    return 0;
}

```