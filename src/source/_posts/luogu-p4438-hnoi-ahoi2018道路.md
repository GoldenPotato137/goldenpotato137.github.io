---
title: '[Luogu P4438] [HNOI/AHOI2018]道路'
tags:
  - DP
id: '394'
categories:
  - - 动态规划
  - - 树形DP
date: 2019-03-28 15:28:09
updated: 2019-03-28 15:28:09
---

# 题面

[4438 \[HNOI/AHOI2018\]道路](https://www.luogu.org/problemnew/show/P4438)

* * *

# Solution

这是一道树形DP妙题。 平时，我们设计树形DP的状态一般都是以子树为基础来设计的。很不幸的是，这题因为那个蜜汁柿子无法化简，导致了极其严重的后效性。因此，我们这里并不能以子树为基础来设计状态了。 这题妙就妙在这个状态设计。这题我们考虑以链为基础来设计状态。 **设$f\[i\]\[j\]\[k\]$表示从根节点到达第$i$个点，一路上经过了$j$条没有修过的公路及$k$条没有修过的铁路，把它的孩子全部连同到根所需的最小代价**。 这样一来，我们惊讶的发现这样设就没有后效性问题，因为链的状态已经被我们包含在DP的状态里了。 转移非常简单，我们只需要**讨论一下是这个点是修铁路还是公路就好。** 即：$f\[i\]\[j\]\[k\]=min(f\[lson\]\[j\]\[k\]+f\[rson\]\[j\]\[k+1\],f\[lson\]\[j+1\]\[k\],f\[rson\]\[j\]\[k\])$ 但是这样做还有一个问题，就是我们的空间复杂度是$O(n\\cdot40\\cdot40)$的，开不下。 仔细观察后可以发现我们上面开辣么大的数组，很多地方是用不到的。因此，我们可以考虑只记录一个映射，然后把值记到一个统一的一维数组里面去。 时间复杂度$O(n\\cdot 40 \\cdot 40)$ 就酱，这题就被我们切掉啦(～￣▽￣)～

* * *

# Code

```cpp
//Luogu P4438 [HNOI/AHOI2018]道路
//Mar,27th,2019
//你从未见过的船新树形DP
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=20000*2+200;
const int M=40+3;
const long long inf=0x3f3f3f3f3f3f3f3fll;
int f[N][M][M],a[N],b[N],c[N];
long long ans[20000000];
int n,s[N],t[N],to;
long long dfs(int now,long long cnt_s,long long cnt_t)
{
    if(f[now][cnt_s][cnt_t]!=0) 
        return ans[f[now][cnt_s][cnt_t]];
    f[now][cnt_s][cnt_t]=++to;
    if(now>=n)
        return ans[f[now][cnt_s][cnt_t]]=c[now]*(a[now]+cnt_s)*(b[now]+cnt_t);
    long long t_ans=inf;
    t_ans=dfs(s[now],cnt_s,cnt_t)+dfs(t[now],cnt_s,cnt_t+1);
    t_ans=min(t_ans,dfs(s[now],cnt_s+1,cnt_t)+dfs(t[now],cnt_s,cnt_t));
    return ans[f[now][cnt_s][cnt_t]]=t_ans;
}
int main()
{
    n=read();
    for(int i=1;i<n;i++)
    {
        s[i]=read(),t[i]=read();
        if(s[i]<0) s[i]=-s[i]+n-1;
        if(t[i]<0) t[i]=-t[i]+n-1;
    }
    for(int i=1;i<=n;i++)
        a[i+n-1]=read(),b[i+n-1]=read(),c[i+n-1]=read();

    //int tim=clock();

    printf("%lld",dfs(1,0,0));
    //cerr<<endl<<clock()-tim;
    return 0;
}

```