---
title: '[Luogu P1829] [国家集训队]Crash的数字表格 / JZPTAB'
tags:
  - 数学
id: '296'
categories:
  - - 反演
  - - 数学
date: 2019-03-05 15:02:01
updated: 2019-03-05 15:02:01
---

# 题面

传送门：[洛咕](https://www.luogu.org/problemnew/show/P1829)

* * *

# Solution

调到自闭，我好菜啊 **为了方便讨论，以下式子$m>=n$** **为了方便书写，以下式子中的除号均为向下取整** 我们来颓柿子吧qwq 显然，题目让我们求： $\\large \\sum\_{i=1}^n\\sum\_{j=1}^m lcm(i,j)$ $lcm$没法玩，我们转到$gcd$形式： $\\large \\sum\_{i=1}^n\\sum\_{j=1}^m \\frac{i\*j}{gcd(i,j)}$ 根据套路，我们去枚举$gcd$ $\\large \\sum\_{i=1}^n\\sum\_{j=1}^m\\sum\_{d=1}^{n} \\frac{i\*j}{d}\[gcd(i,j)=d\]$ 然后可以把$d$的和号移到前面去 $\\large \\sum\_{d=1}^{n}\\sum\_{i=1}^n\\sum\_{j=1}^m \\frac{i\*j}{d}\[gcd(i,j)=d\]$ 要让$gcd(i,j)=d$，$i,j$都必须要为$d$的倍数，我们可以将原来的$i\*d,j\*d$映射为$i,j$,有： $\\large \\sum\_{d=1}^{n}\\sum\_{i=1}^{n/d}\\sum\_{j=1}^{m/d} {i\*j}\*d\[gcd(i,j)=1\]$ 把$d$移到前面去 $\\large \\sum\_{d=1}^{n}d\\sum\_{i=1}^{n/d}\\sum\_{j=1}^{m/d} {i\*j}\[gcd(i,j)=1\]$ 然后我们可以套路地根据$\[x=1\]=\\sum\_{dx}\\mu(d)$这个柿子把$gcd(i,j)$处理掉： $\\large \\sum\_{d=1}^{n}d\\sum\_{i=1}^{n/d}\\sum\_{j=1}^{m/d} {i\*j}\\sum\_{kgcd(i,j)}\\mu(k)$ 根据套路，我们把这种奇奇怪怪的和式变为枚举的形式 $\\large \\sum\_{d=1}^{n}d\\sum\_{i=1}^{n/d}\\sum\_{j=1}^{m/d} {i\*j}\\sum\_{k=1}^{n/d}\[kgcd(i,j)\]\\mu(k)$ 然后就可以把$k$往前提了 $\\large \\sum\_{d=1}^{n}d\\sum\_{k=1}^{n/d}\\sum\_{i=1}^{n/d}\\sum\_{j=1}^{m/d} {i\*j}\*\[kgcd(i,j)\]\\mu(k)$ 要有$kgcd(i,j)$，$i,j$一定要为$k$的倍数 $\\large \\sum\_{d=1}^{n}d\\sum\_{k=1}^{n/d}\\sum\_{i=1}^{\\frac{n}{d\*k}}\\sum\_{j=1}^{\\frac{m}{d\*k}} {i\*j\*k^2}\*\\mu(k)$ 然后我们简单的移一下项方便处理 $\\large \\sum\_{d=1}^{n}d\\sum\_{k=1}^{n/d}\*\\mu(k)\*k^2\\sum\_{i=1}^{\\frac{n}{d\*k}}i\\sum\_{j=1}^{\\frac{m}{d\*k}} j$ 后面的$j$与$i$没有半毛钱关系，我们可以把它分离开来 $\\large \\sum\_{d=1}^{n}d\\sum\_{k=1}^{n/d}\*\\mu(k)\*k^2(\\sum\_{i=1}^{\\frac{n}{d\*k}}i)(\\sum\_{j=1}^{\\frac{m}{d\*k}} j)$ 搞定，到这里为止，我们所有东西都可以求了。 对于前面的$d$的和式，我们可以发现当$n/d,m/d$不变的时候，后面的柿子计算出来的结果是一样的，因此我们可以$O(\\sqrt n)$来整除分块掉前面那个和式。 后面的那个柿子我们可以再来一次整数除法来计算：最后面的两个和式都是等差数列，前面的$\\mu(k)\*k^2$可以前缀和直接计算。 总复杂度$O(\\sqrt n \* \\sqrt n)=O(n)$ 但是这题还有一个$O(\\sqrt n)$的做法，蒟蒻太菜了不会，就不说了

* * *

# Code

**这题细节繁多，请注意多膜以防乘爆** **预处理中的$i^2$会爆int，请注意**

```cpp
//Luogu  P1829 [国家集训队]Crash的数字表格 / JZPTAB
//Jan,23rd,2019
//莫比乌斯反演
#include<iostream>
#include<cstdio>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x\*10+c-'0';c=getchar();}
    return x\*f;
}
const int N=10000000+1000;
const int M=10000000;
const int poi=20101009;
int prime[N],cnt_p,mu[N];
bool noPrime[N];
void GetPrime(int n)
{
    noPrime[1]=true,mu[1]=1;
    for(int i=2;i<=n;i++)
    {
        if(noPrime[i]==false)
            prime[++cnt_p]=i,mu[i]=-1;
        for(int j=1;j<=cnt_p and i\*prime[j]<=n;j++)
        {
            noPrime[i\*prime[j]]=true;
            if(i%prime[j]==0)
            {
                mu[i\*prime[j]]=0;
                break;
            }
            mu[i\*prime[j]]=mu[i]\*mu[prime[j]];
        }
    }
}
long long n,m,pre_mu[N];
long long f(int d)
{
    long long t_ans=0;
    for(long long l=1,r;l<=n/d;l=r+1)
    {
        r=min((n/d)/((n/d)/l),(m/d)/((m/d)/l));
        t_ans=(t_ans+(pre_mu[r]-pre_mu[l-1])\*(((1+n/d/l)\*(n/d/l)/2)%poi)%poi\*(((1+m/d/l)\*(m/d/l)/2)%poi))%poi;
    }
    return (t_ans%poi+poi)%poi;
}
int main()
{
    n=read(),m=read();
    if(n>m) swap(n,m);

    GetPrime(m);
    for(long long i=1;i<=m;i++)
        pre_mu[i]=((pre_mu[i-1]+mu[i]\*i\*i)%poi+poi)%poi;
    long long ans=0;
    for(long long l=1,r;l<=n;l=r+1)
    {
        r=min(n/(n/l),m/(m/l));
        ans=((ans+(l+r)\*(r-l+1)/2%poi\*f(l))%poi+poi)%poi;
    }

    printf("%lld",ans);
    return 0;
}

```