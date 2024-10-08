---
title: '[Luogu P2257] YY的GCD'
tags:
  - 数学
id: '262'
categories:
  - - 反演
  - - 数学
date: 2019-03-04 13:28:35
updated: 2019-03-04 13:28:35
---

# 题面

传送门：[洛咕](https://www.luogu.org/problemnew/show/P2257)

* * *

# Solution

推到自闭，我好菜啊 显然，这题让我们求： $\\large \\sum\_{i=1}^{n}\\sum\_{j=1}^{m}\[gcd(i,j)\\in prime\]$ 根据套路，我们可以把判断是否为质数改为枚举这个质数，有： 为了方便枚举，我们在这里假设有$m>n$ $\\large \\sum\_{i=1}^{n}\\sum\_{j=1}^{m}\\sum\_{k\\in prime}^{n}\[gcd(i,j)= k\]$ 显然，要让$gcd(i,j)=k$，必须要有$i,j$均为$k$的倍数，因此有： $\\large \\sum\_{k\\in prime}^{n}\\sum\_{i=1}^{n/k}\\sum\_{j=1}^{m/k}\[gcd(i,j)= 1\]$ (在这里除号指向下取整) 根据套路，我们要去掉这里的判断符号。因为我们的莫比乌斯函数有这个性质：$\[x=1\]=\\sum\_{dx}\\mu(d)$，我们这里可以直接把$gcd(i,j)$作为$x$带入这个性质里面，有： $\\large \\sum\_{k\\in prime}^{n}\\sum\_{i=1}^{n/k}\\sum\_{j=1}^{m/k}\\sum\_{dgcd(i,j)}\\mu(d)$ 然后根据套路，我们直接枚举这里的$d$，有： $\\large \\sum\_{k\\in prime}^{n}\\sum\_{i=1}^{n/k}\\sum\_{j=1}^{m/k}\\sum\_{d=1}^{n/k}μ(d)\[dgcd(i,j)\]$ （因为前面$i,j$中最小的是$n/k$,所以说我们这里$d$的最大值也为$n/k$） 然后我们这里的$\\sum\_{d=1}^{n/k}$显然可以直接往前提 $\\large \\sum\_{k\\in prime}^{n}\\sum\_{d=1}^{n/k}\\sum\_{i=1}^{n/k}\\sum\_{j=1}^{m/k}μ(d)\[dgcd(i,j)\]$ 这时候$\\mu(d)$显然也可以往前提 $\\large \\sum\_{k\\in prime}^{n}\\sum\_{d=1}^{n/k}μ(d)\\sum\_{i=1}^{n/k}\\sum\_{j=1}^{m/k}\[dgcd(i,j)\]$ 这时候，我们可以发现后面那个判断式为1当且仅当$i,j$均为$d$的倍数，所以我们可以直接把那两个$\\sum$简化掉 $\\large \\sum\_{k\\in prime}^{n}\\sum\_{d=1}^{n/k}μ(d)\\frac{n}{k\*d}\\frac{m}{k\*d}$ 这时候，我们已经可以在$O(logn\*\\sqrt n)$的时间内算一次答案了（这里的$log$为质数个数），很可惜，这样的复杂度并不能通过这一题。 事实上，我们还有一个常见的套路来优化这里： 我们可以设$T=k\*d$，于是我们有： $\\large \\sum\_{k\\in prime}^{n}\\sum\_{d=1}^{n/k}μ(\\frac{T}{k})\\frac{n}{T}\\frac{m}{T}$ 然后可以把后面那个和式提前，枚举T，有： $\\large \\sum\_{T=1}^{n}\\frac{n}{T}\\frac{m}{T}\\sum\_{(k\\in prime,kT)}μ(\\frac{T}{k})$ 搞定，到这里为止，我们一切东西都可以算了。 前面的$\\frac{n}{T}\\frac{m}{T}$可以整除分块来搞，后面那个$μ$可以在$O(n)$的时间预处理，然后算的时候前缀和一搞就ok啦。 如何预处理呢？我们可以考虑这样做：我们先枚举每一个质数$x$，再考虑这个$x$对它的整数倍$t$的贡献为$\\mu(t)$ 酱紫，我们就可以在$O(\\sqrt n)$的时间内处理每一个询问了。 完结撒花✿✿ヽ(°▽°)ノ✿

* * *

# Code

```cpp
//Luogu P2257 YY的GCD
//Jan,22ed,2019
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
int mu[N],prime[N],cnt_p;
bool noPrime[N];
void GetPrime(int n)
{
    mu[1]=1;
    noPrime[1]=true;
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
long long f[N],pre_f[N];
int main()
{
    int t=clock();
    GetPrime(M);
    for(int i=1;i<=cnt_p;i++)
        for(int j=1;prime[i]\*j<=M;j++)
            f[prime[i]\*j]+=mu[j];
    for(int i=1;i<=M;i++)
        pre_f[i]=pre_f[i-1]+f[i];

    int T=read();
    for(;T>0;T--)
    {
        long long n=read(),m=read();
        if(n>m) swap(n,m);

        int l=1,r=1;
        long long ans=0;
        for(;l<=n;l=r+1)
        {
            r=min(n/(n/l),m/(m/l));
            ans+=(pre_f[r]-pre_f[l-1])\*(n/l)\*(m/l);
        }

        printf("%lld\n",ans);
    }
    cerr<<clock()-t;
    return 0;
}

```