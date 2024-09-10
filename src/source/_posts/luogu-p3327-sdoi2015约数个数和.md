---
title: '[Luogu P3327] [SDOI2015]约数个数和'
tags:
  - 数学
id: '294'
categories:
  - - 反演
  - - 数学
date: 2019-03-05 14:57:16
updated: 2019-03-05 14:57:16
---

# 题面：

传送门：[洛咕](https://www.luogu.org/problemnew/show/P3327)

* * *

# Solution

首先，我们需要一个结论： $\\large d(i,j)=\\sum\_{xi}\\sum\_{yj}\[gcd(x,y)=1\]$

> 证明 理性证明请看[这篇博客](https://www.luogu.org/blog/An-Amazing-Blog/mu-bi-wu-si-fan-yan-ji-ge-ji-miao-di-dong-xi)的例五 本蒟蒻提供一个感性证明的方法：如果$x\*y$是$i\*j$的因数，我们必须有$xi,yj$，而后面那个$gcd(x,y)$是用来去重的

有了这个柿子之后，我们之后的推导就比较套路了： **为了方便讨论，之后的柿子均默认$m>n$** **为了方便书写，之后的除法默认向下取整** 原式： $\\large \\sum\_{i=1}^n\\sum\_{j=1}^md(i\*j)$ 把我们上面的结论代进去 $\\large \\sum\_{i=1}^n\\sum\_{j=1}^m\\sum\_{xi}\\sum\_{yj}\[gcd(x,y)=1\]$ 根据套路，这里的$xi$与$yj$应该写成枚举的形式： $\\large \\sum\_{i=1}^n\\sum\_{j=1}^m\\sum\_{x=1}^{n}\[xi\]\\sum\_{y=1}^{m}\[yj\]\[gcd(x,y)=1\]$ 这里显然可以把$x,y$的和式写到最前面去： $\\large \\sum\_{x=1}^{n}\[xi\]\\sum\_{y=1}^{m}\[yj\]\\sum\_{i=1}^n\\sum\_{j=1}^m\[gcd(x,y)=1\]$ 然后就可以去掉$x,y$后面的那两个判断式啦 $\\large \\sum\_{x=1}^{n}\\sum\_{y=1}^{m}\\sum\_{i=1}^{n/x}\\sum\_{j=1}^{m/y}\[gcd(x,y)=1\]$ 然后我们再去套路后面那个$gcd$，根据莫比乌斯函数的性质$\[x=1\]=\\sum\_{dx}\\mu(d)$，我们就把$gcd$带入得 $\\large \\sum\_{x=1}^{n}\\sum\_{y=1}^{m}\\sum\_{i=1}^{n/x}\\sum\_{j=1}^{m/y}\\sum\_{dgcd(x,y)}\\mu(d)$ 然后去枚举$d$ $\\large \\sum\_{x=1}^{n}\\sum\_{y=1}^{m}\\sum\_{i=1}^{n/x}\\sum\_{j=1}^{m/y}\\sum\_{d=1}^{n}\\mu(d)\[dgcd(x,y)\]$ 套路地把$\\mu$的和式丢到最前面，化简一下就有： $\\large \\sum\_{d=1}^{n}\\mu(d)\\sum\_{x=1}^{n/d}\\sum\_{y=1}^{m/d}\\sum\_{i=1}^{n/(x\*d)}\\sum\_{j=1}^{m/(y\*d)}1$ 然后有 $\\large \\sum\_{d=1}^{n}\\mu(d)\\sum\_{x=1}^{n/d}\\sum\_{y=1}^{m/d}\\frac{n}{x\*d}\\frac{m}{y\*d}$ 移项整理一下： $\\large \\sum\_{d=1}^{n}\\mu(d)\\sum\_{x=1}^{n/d}\\frac{n}{x\*d}\\sum\_{y=1}^{m/d}\\frac{m}{y\*d}$ 好了，到这里我就不会推了 ![kAl7Ct.jpg](https://s2.ax1x.com/2019/01/23/kAl7Ct.jpg) 之后的内容感谢@Maxwei\_wzj的教学 事实上，这个柿子我们已经可以算了。 $\\large \\sum\_{d=1}^{n}\\mu(d)\\sum\_{x=1}^{n/d}\\frac{n}{x\*d}\\sum\_{y=1}^{m/d}\\frac{m}{y\*d}$ 首先，我们有一个结论(我不会证) $\\large x/(y\*z)=(x/y)/z$ (这里的除法**向下取整**) 我们的柿子就可以变为 $\\large \\sum\_{d=1}^{n}\\mu(d)\\sum\_{x=1}^{n/d}\\frac{\\frac{n}{d}}{x}\\sum\_{y=1}^{m/d}\\frac{\\frac{m}{d}}{y}$ 然后我们再设一个方程: $f(x)=\\sum\_{i=1}^x\\frac{x}{i}$ 我们柿子就可以写为： $\\large \\sum\_{d=1}^{n}\\mu(d)f(\\frac{n}{d})f(\\frac{m}{d})$ 诶？我们好像又能整除分块了。 没错，我们只需要先用$O(n\*\\sqrt n)$的整除分块预处理出$f$ 然后再每次$O(\\sqrt n)$整除分块算出那个柿子就好。 时间复杂度$O(n\*\\sqrt n)$ 完结撒花✿✿ヽ(°▽°)ノ✿

* * *

# Code

```cpp
//Luogu  P3327 [SDOI2015]约数个数和
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
const int N=50000+1000;
const int M=50000;
int prime[N],p_cnt,mu[N];
bool noPrime[N];
void GetPrime(int n)
{
    noPrime[1]=true,mu[1]=1;
    for(int i=2;i<=n;i++)
    {
        if(noPrime[i]==false)
            prime[++p_cnt]=i,mu[i]=-1;
        for(int j=1;j<=p_cnt and i\*prime[j]<=n;j++)
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
long long f[N],pre_mu[N];
int main()
{
    GetPrime(M);
    for(int i=1;i<=M;i++)
        for(int l=1,r;l<=i;l=r+1)
        {
            r=i/(i/l);
            f[i]+=(r-l+1)\*(i/l);
        }
    for(int i=1;i<=M;i++)
        pre_mu[i]=pre_mu[i-1]+mu[i];

    int T=read();
    for(;T>0;T--)
    {
        long long n=read(),m=read();
        if(n>m) swap(n,m);

        long long ans=0;
        for(int l=1,r;l<=n;l=r+1)
        {
            r=min(n/(n/l),m/(m/l));
            ans+=(pre_mu[r]-pre_mu[l-1])\*f[n/l]\*f[m/l];
        }

        printf("%lld\n",ans);
    }
    return 0;
}

```