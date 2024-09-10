---
title: '[Luogu P2522] [HAOI2011]Problem b'
tags:
  - 数学
id: '295'
categories:
  - - 反演
  - - 数学
date: 2019-03-05 14:58:14
updated: 2019-03-05 14:58:14
---

# 题面

传送门：[洛咕](https://www.luogu.org/problemnew/show/P2522)

* * *

# Solution

我怎么只会刷水题 [这题](https://www.cnblogs.com/GoldenPotato/p/10304040.html)的双倍经验题，不多说啥了。 啥？范围不一样？ 那根据我们写数位DP及二维前缀和的经验，我们容斥一下...... 然后就没有然后了。 时间复杂度$O(m\*\\sqrt n)$

* * *

# Code

人傻自带大常数,不开O2 T一个点 事实上这题可以把一些不必要的$longlong$改成$int$，刚好能过。可惜我太颓，不想改了

```cpp
//Luogu P2522 [HAOI2011]Problem b
//Jan,23rd,2019
//莫比乌斯函数双倍经验题
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
const int N=50000+1000;
const int M=50000;
int prime[N],p_cnt,mu[N];
bool noPrime[M];
void GetPrime(int n)
{
    noPrime[1]=true,mu[1]=1;
    for(int i=2;i<=n;i++)
    {
        if(noPrime[i]==false)
            prime[++p_cnt]=i,mu[i]=-1;
        for(int j=1;j<=p_cnt and i*prime[j]<=n;j++)
        {
            noPrime[i*prime[j]]=true;
            if(i%prime[j]==0)
            {
                mu[i*prime[j]]=0;
                break;
            }
            mu[i*prime[j]]=mu[i]*mu[prime[j]];
        }
    }
}
long long pre_mu[N];
long long GetAns(long long n,long long m,int K)
{
    long long t_ans=0;
    if(n>m) swap(n,m);
    n/=K,m/=K;
    for(int l=1,r;l<=n;l=r+1)
    {
        r=min(n/(n/l),m/(m/l));
        t_ans+=(pre_mu[r]-pre_mu[l-1])*(n/l)*(m/l);
    }
    return t_ans;
}
int main()
{
    GetPrime(M);
    for(int i=1;i<=M;i++)
        pre_mu[i]=pre_mu[i-1]+mu[i];

    int T=read();
    for(;T>0;T--)
    {
        long long a=read(),b=read(),c=read(),d=read(),K=read();
        long long ans=GetAns(b,d,K)-GetAns(a-1,d,K)-GetAns(b,c-1,K)+GetAns(a-1,c-1,K);
        printf("%lld\n",ans);
    }

    return 0;
}

```