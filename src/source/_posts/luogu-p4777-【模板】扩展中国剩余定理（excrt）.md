---
title: '[Luogu P4777] 【模板】扩展中国剩余定理（EXCRT）'
tags:
  - 数学
id: '231'
categories:
  - - 同余
  - - 数学
date: 2019-02-25 19:26:33
---

# 题面

传送门：[洛咕](https://www.luogu.org/problemnew/show/P4777)

* * *

# Solution

真\*扩展中国剩余定理模板题。我怎么老是在做模板题啊 但是这题与之前不同的是不得不写龟速乘了。 还有两个重点

*   我们在求LCM的时候，记得先/gcd再去乘另外那个数，直接乘会乘爆的
*   我们在做龟速乘之前，要保证要乘的两个数>=0,如果<0的话，龟速乘会爆掉的，我们传进去之间记得膜一下

int128:你说啥？这里风太大，我听不见。

* * *

# Code

```cpp
//Luogu  P4777 【模板】扩展中国剩余定理（EXCRT）
//Jan,15th,2019
//中国剩余定理
#include<iostream>
#include<cstdio>
using namespace std;
long long read()
{
    long long x=0,f=1;char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
long long take(long long A,long long B,long long poi)
{
    if(B==0) return 0;
    long long temp=take(A,B/2,poi)*2%poi;
    if(B%2==1) temp=(temp+A)%poi;
    return temp;
}
long long exgcd(long long A,long long B,long long &x,long long &y)
{
    if(B==0)
    {
        x=1,y=0;
        return A;
    }
    long long t_ans=exgcd(B,A%B,x,y),tx=x;
    x=y,y=tx-(A/B)*y;
    return t_ans;
}

const int N=100000+100;
int n;
long long a[N],p[N];
int main()
{
    n=read();
    for(int i=1;i<=n;i++)
        p[i]=read(),a[i]=read();

    long long A=a[1],P=p[1];
    for(int i=2;i<=n;i++)
    {
        long long x,y,gcd=exgcd(P,p[i],x,y);
        long long t_P=P,t=p[i]/gcd;
        x=(x%t+t)%t,x=take(x,(((a[i]-A)/gcd)%t+t)%t,t);
        P=P*(p[i]/gcd),A=(A+take(t_P,x,P))%P;
    }

    printf("%lld",(A%P+P)%P);
    return 0;
}

```