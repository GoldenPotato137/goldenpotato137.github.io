---
title: '[Luogu P2831] 愤怒的小鸟'
tags:
  - DP
id: '176'
categories:
  - - 动态规划
  - - 状压DP
date: 2019-02-22 17:21:20
updated: 2019-02-22 17:21:20
---

题面：[洛谷](https://www.luogu.org/problemnew/show/P2831)

* * *

## Solution

首先，我们可以先康一康题目的数据范围：n<=18，应该是状压或者是搜索。 事实上，这题搜索和状压DP都是能做的。 （因为搜索在我心中留下了阴影（斗地主），所以在这里，我讲状压DP的做法） 根据我们以往设计状压DP的经验，我们可以很轻松地设计这一题的状态： **设f\[i\]表示打下的猪猪的状态为i的方案数**，（状态在这里用二进制方式来表示，例如：00101表示打下了第1和第3只猪） 那么有： $f\[i\] = min(f\[j\])+1$ （j为i的子集） 这里用到一个枚举子集的技巧，对于一个状态i，它可以这样枚举子集： for(int j=i;j>=0;j=(j-1)&i) (至于证明，你可以在草稿纸上画画，很快就会发现它的精妙了) 那我们怎么判断能否从状态 j 转移到 i 呢？ 首先，根据数学常识，**我们需要3个x不一样的点才能确定一条抛物线。这题已经固定了原点了，所以我们还需要两个点来确定一条抛物线**。 **如果j与i只有一个或两个x不同的点 是不同的，那显然是可以转移的。** 对于有两个以上的点，**我们可以用前两个点通过解二元一次方程来计算函数的a与b，然后再去一个一个判断每个不同的点是否在这条抛物线上**。 对于如何解二元一次方程..........（这应该是数学常识吧） 复杂度$O(3^n\*n\*T)$ 显然TLE，事实上，这样做只能得60分。 那怎么优化复杂度呢？ 刚刚的枚举子集显然是不可行了，那我们可以换个思路。 我们可以枚举点。 **对于某一种状态，我们肯定可以枚举两个（或一个）没有用过的点去构成新的抛物线从而更新其他的状态。** 这样子，我们成功地把复杂度降为了 $O(2^n\*n^2\*T)$ 依然过不了，事实上，这样做能得85分。 上一个作法已经和正解很接近了。 我们可以考虑这样优化方程： ![AaUqhT.png](https://s2.ax1x.com/2019/03/27/AaUqhT.png) 这样子，我们复杂度就降为了$ O(2^n\*n\*T)$ 就酱，我们就可以把这道题切掉啦(´▽\`)ﾉ

* * *

## Code

```cpp
//Luogu P2831 愤怒的小鸟
//Sep,19th,2018
//状压DP
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
using namespace std;
const int N=18+2;
const double eps=1e-7;
struct node
{
    double x,y;
}nd[N];
long long f[1<<N];
int n,POW[N],g[N][N];
inline double pf(double x)
{
    return x*x;
}
bool solve(node A,node B,double &a,double &b)
{
    if(fabs(A.x-B.x)<=eps) return false;
    a=(B.x*A.y-A.x*B.y)/(pf(A.x)*B.x-pf(B.x)*A.x);
    b=(pf(B.x)*A.y-pf(A.x)*B.y)/(pf(B.x)*A.x-pf(A.x)*B.x);
    if(a>=0) return false;
    return true;
}
double fun(double x,double a,double b)
{
    return a*pf(x)+b*x;
}
int main()
{
    POW[0]=1;
    for(int i=1;i<N;i++)
        POW[i]=POW[i-1]*2;
    int T,tt;
    scanf("%d",&T);
    for(;T>0;T--)
    {
        memset(g,0,sizeof g);
        scanf("%d%d",&n,&tt);
        for(int i=1;i<=n;i++)
            scanf("%lf%lf",&nd[i].x,&nd[i].y);

        for(int i=1;i<=n;i++)
            for(int j=i+1;j<=n;j++)
            {
                double a=0,b=0;
                bool OK=solve(nd[i],nd[j],a,b);
                if(OK==false) continue;
                for(int k=1;k<=n;k++)
                    if(fabs(fun(nd[k].x,a,b)-nd[k].y)<=eps)
                        g[i][j]+=POW[k-1];
            }

        memset(f,0x3f,sizeof f);
        f[0]=0;
        int to=(1<<n)-1,used[N];
        for(int i=0;i<to;i++)
        {
            memset(used,0,sizeof used);
            int temp=i,j;
            for(j=n-1;j>=0;j--)
                if(temp-POW[j]>=0)
                {
                    temp-=POW[j];
                    used[j+1]=true;
                }
            for(j=1;j<=n;j++)
                if(used[j]==false) 
                    break;
            f[iPOW[j-1]]=min(f[iPOW[j-1]],f[i]+1);
            for(int k=j+1;k<=n;k++)
                if(used[k]==false and g[j][k]!=0)
                        f[ig[j][k]]=min(f[ig[j][k]],f[i]+1);
        }

        printf("%lld\n",f[to]);
    }
    return 0;
}
```