---
title: '[Luogu  P3455] [POI2007]ZAP-Queries'
tags:
  - 数学
id: '264'
categories:
  - - 反演
  - - 数学
date: 2019-03-04 13:31:43
updated: 2019-03-04 13:31:43
---

# 题面

传送门：[洛咕](https://www.luogu.org/problemnew/show/P3455)

* * *

# Solution

这题比[这题](https://www.cnblogs.com/GoldenPotato/p/10302839.html)不懂简单到哪里去了 好吧，我们来颓柿子。 **为了防止重名，以下所有柿子中的$x$既是题目中的$d$** **为了方便讨论，以下柿子均假设$b>=a$** **为了方便书写，以下除号均为向下取整** 题目要求的显然是： $\\large \\sum\_{i=1}^{a}\\sum\_{j=1}^{b}\[gcd(i,j)=x\]$ 根据套路，我们这里要先把这个$x$除掉 $\\large \\sum\_{i=1}^{a/x}\\sum\_{j=1}^{b/x}\[gcd(i,j)=1\]$ 再根据套路，根据莫比乌斯函数中$\[x=1\]=\\sum\_{dx}\\mu(d)$的性质，我们把这个$gcd(i,j)$略作转换： $\\large \\sum\_{i=1}^{a/x}\\sum\_{j=1}^{b/x}\\sum\_{dgcd(i,j)}\\mu(d)$ 再次根据套路，我们把$d$的和号改成枚举$d$的形式： $\\large \\sum\_{i=1}^{a/x}\\sum\_{j=1}^{b/x}\\sum\_{d=1}^{a/x}\\mu(d)\*\[dgcd(i,j)\]$ 显然，我们可以把$\\mu(d)$和它前面的和号提到前面去 $\\large \\sum\_{d=1}^{a/x}\\mu(d)\\sum\_{i=1}^{a/x}\\sum\_{j=1}^{b/x}\[dgcd(i,j)\]$ 显然，若要$\[dgcd(i,j)\]=1$，则$i,j$都必须为$d$的倍数 $\\large \\sum\_{d=1}^{a/x}\\mu(d)\\frac{a}{x\*d}\\frac{b}{x\*d}$ OK,到此为止，我们所有东西都可以算了。 前面那个$\\mu(d)$可以配上后面的两个和号用整除分块的方法前缀和计算即可。如果不是很清楚的话可以看一下代码。 时间复杂度$O(m\*\\sqrt n)$ 完结撒花✿✿ヽ(°▽°)ノ✿0

* * *

# Code

```cpp
//Luogu P3455 [POI2007]ZAP-Queries
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
const int N=50000+100;
const int M=50000;
int cnt_p,prime[N],mu[N];
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
long long pre_mu[N];
int main()
{
    GetPrime(M);
    for(int i=1;i<=M;i++)
        pre_mu[i]=pre_mu[i-1]+mu[i];

    int T=read();
    for(;T>0;T--)
    {
        long long a=read(),b=read(),x=read();

        long long ans=0;
        if(a>b) swap(a,b);
        a/=x,b/=x;
        for(int l=1,r;l<=a;l=r+1)
        {
            r=min(a/(a/l),b/(b/l));
            ans+=(pre_mu[r]-pre_mu[l-1])\*(a/l)\*(b/l);
        }

        printf("%lld\n",ans);
    }
    return 0;
}

```