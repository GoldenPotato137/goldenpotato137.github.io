---
title: '[Luogu P2827] 蚯蚓'
tags:
  - 模拟
id: '168'
categories:
  - - 模拟
date: 2019-02-22 12:44:49
updated: 2019-02-22 12:44:49
---

# 题面：

传送门：[洛谷](https://www.luogu.org/problemnew/show/P2827)

* * *

# Solution

看到这题，我们肯定会有一个大胆想法。 那就是直接用堆模拟这个过程。 对于q，我们只需要在堆中多维护一个T,记录每个点插入的时间，在新的元素插入时直接计算所比较的点的当前长度就可以完成插入了。 时间复杂度$O(M\*log(M))$ 这样的做法只能获得65-70分，因为后面的数据非常大。 所以说，我们要另寻他路。 首先，我们经过看题解手玩可以发现一个很显然但是很重要的结论： 在$q=0$的时候，一条线段所分裂出来的两条线段肯定要比它更小的线段分裂出来的对应的两条线段更大。

> 证明十分简单，设x，y为两条分裂前的线段，且x>y 那么，较长的那一条(假设p>0.5(p为分割点)) 为： px 与 py， 显然，px>py 同理可证另一条线段也有这种关系。

根据这个关系，我们可以考虑这样的做法： 我们开三个队列，**第一个队列放入排好序的原序列，第二个队列放每次分裂出来的较长的蚯蚓，第三个放每次分裂出来的较短的蚯蚓。** 那么根据刚才的证明，我们可以得出，**第二个与第三个队列一定是有序的**，因为我们每次取的蚯蚓一定比之前取的更短，所以分裂出来的肯定比比之前分裂出来的蚯蚓更短。 这样子，我们就可以模拟这个过程，每次取三个队列中最大的那一个，并把分裂出来的对应放到第二第三个队列的末尾就好。 事实上，对于$q>0$的情况，这个推论也是成立的。

> 首先，我们可以假设出来x与y分别是分裂前的线段长度且x>y,假设x与y之间间隔的时间为T 那么，在y分裂的时刻，x分裂出来的线段较长的长度为(假设p>0.5)： $px+T_q $ y分裂出来的较长的线段长度为： $(y+T_q)_p = py + T_q_p $ 显然 $ px+T_q > py + T_q_p$

所以说，我们刚刚的结论在这里也是成立的。 对于在某一时刻的线段的具体长度，**我们可以通过在队列中多记录一个插入时间，这样就可以算出某一时刻的某条线段的具体长度了。** 时间复杂度$O（nlogn+m）$ . 就酱，我们就可以AC这道题啦(≧ω≦)/

* * *

# Code

```cpp
//Luogu P2827 蚯蚓
//Sep,9th,2018
//巧妙的三个队列
#include<iostream>
#include<cstdio>
#include<queue>
#include<algorithm>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=100000+100;
const int M=7000000+N+2000;
int a[M],n,m,q,u,v,t;
struct worm
{
    int T,s;
    worm (int A,int B)
    {
        s=A,T=B;
    }
    inline int GetLen(int mtime)//mtime秒后的长度
    {
        return s+(mtime-T)*q;
    }
};
queue <worm> A,B,C;
double p;
bool cmp(int x,int y)
{
    return x>y;
}
int main()
{
    n=read(),m=read(),q=read(),u=read(),v=read(),t=read();
    for(int i=1;i<=n;i++)
        a[i]=read();

    sort(a+1,a+1+n);
    for(int i=n;i>=1;i--)
        A.push(worm(a[i],0));
    for(int i=1;i<=m;i++)
    {
        long long from=-1,t_MAX=-0x3f3f3f3f;
        if(A.empty()==false) from=1,t_MAX=A.front().GetLen(i-1);
        if(B.empty()==false and B.front().GetLen(i-1)>t_MAX) from=2,t_MAX=B.front().GetLen(i-1);
        if(C.empty()==false and C.front().GetLen(i-1)>t_MAX) from=3,t_MAX=C.front().GetLen(i-1);
        int px=(t_MAX*u)/v,t_px=t_MAX-px;
        B.push(worm(max(px,t_px),i));
        C.push(worm(min(px,t_px),i));
        if(from==1) A.pop();
        else if(from==2) B.pop();
        else C.pop();
        if(i%t==0)
            printf("%lld ",t_MAX);
    }
    printf("\n");

    n=0;
    while(A.empty()==false)
        a[++n]=A.front().GetLen(m),A.pop();
    while(B.empty()==false)
        a[++n]=B.front().GetLen(m),B.pop();
    while(C.empty()==false)
        a[++n]=C.front().GetLen(m),C.pop();
    sort(a+1,a+1+n,cmp);
    for(int i=1;i<=n;i++)
        if(i%t==0)
            printf("%d ",a[i]);
    return 0;
}
```