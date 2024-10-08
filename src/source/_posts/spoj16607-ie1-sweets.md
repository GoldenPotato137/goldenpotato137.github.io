---
title: SPOJ16607 IE1 - Sweets
tags:
  - 数学
id: '222'
categories:
  - - 容斥
  - - 数学
  - - 组合数学
date: 2019-02-25 19:21:15
updated: 2019-02-25 19:21:15
---

# 题面

传送门：

*   [洛咕](https://www.luogu.org/problemnew/show/SP16607)
*   [SPOJ](https://www.spoj.com/problems/IE1/)

* * *

# Solution

这题的想法挺妙的。 . 首先，对于这种区间求答案的问题，我们一般都可以通过类似前缀和的思想一减来消去a，**即求\[a,b\]的答案可以转化为求\[1,b\]-\[1,a-1\]** 接下来我们可以先考虑一下每个物品数量不限制的做法。我们可以把这个问题类比为放球问题：我们要在n个相同的盒子里放x个球，这个问题可以用隔板法解决，**显然答案为$C\_{x+n-1}^{n-1}$** 因为我们的n特别小，而且p为合数，所以可以用分解质因数的方法来算这个组合数。 . 接下来，我们可以考虑一下如何处理多计算的答案，考虑用容斥定理来解决这个问题。 不了解容斥定理的同志可以先看一下[这篇文章](https://blog.csdn.net/m0_37286282/article/details/78869512) 我们要求的是至少有一个物品不满足要求的方案总数，即求所有不满足要求的方案的并。 根据容斥定理，这个并的值为 $\\sum有一个物品不满足要求-有两个物品不满足要求+有三个物品不满足要求-...$ 所以说，我们只需要强制某些物品先选$m\_i+1$个，再按照上面的放球问题的公式来计算就可以得出有若干个物品不满足要求的方案数。 答案即为总方案数-不满足要求的方案数的并 时间复杂度$O(2^n\*log\_{max(a,b)})$ 这个问题就被我们切掉啦ヽ(￣▽￣)ﾉ . 如果有不清楚的地方可以看一下代码。

* * *

# Code

```cpp
//Luogu SP16607 IE1 - Sweets
//Jan,14th,2019
//容斥原理的应用
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
const int poi=2004;
const int N=15;
int prime[6]={-1,2,3,5,7};
long long C(long long x,long long y)//x为底，y为指
{
    if(y>x) return 0;
    int cnt[6]={0};
    long long t_ans=1;
    for(long long i=x-y+1;i<=x;i++)
    {
        long long t_num=i;
        for(int j=1;j<=4;j++)
            while(t_num%prime[j]==0)
            {
                t_num/=prime[j];
                cnt[j]++;
            }
        t_ans=(t_ans*t_num)%poi;
    }
    for(long long i=1;i<=y;i++)
    {
        long long t_num=i;
        for(int j=1;j<=4;j++)
            while(t_num%prime[j]==0)
            {
                t_num/=prime[j];
                cnt[j]--;
            }
        }
    for(int i=1;i<=4;i++)
        while(cnt[i]>0)
            t_ans=(t_ans*prime[i])%poi,cnt[i]--;
    return t_ans;
}
int m[N],n,a,b;
long long t_ans2,t_x;
bool used[N];
void dfs(int now)
{
    if(now==n+1)
    {
        long long t_cnt=0,tot=0;
        for(int i=1;i<=n;i++)
            if(used[i]==true)
                t_cnt+=m[i]+1,tot++;
        if(t_cnt>t_x) return;
        long long f=(tot%2==1?-1:1);
        t_ans2+=f*C(t_x-t_cnt+n,n);
        t_ans2=(t_ans2%poi+poi)%poi;
        return;
    }
    for(int i=0;i<=1;i++)
        used[now]=i,dfs(now+1);
}
long long Calc(long long x)
{
    t_ans2=0,t_x=x;
    dfs(1);
    return t_ans2;
}
int main()
{
    n=read(),a=read(),b=read();
    for(int i=1;i<=n;i++)
        m[i]=read();

    printf("%lld",((Calc(b)-Calc(a-1))%poi+poi)%poi);
    return 0;
}

```