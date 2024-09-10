---
title: '[Luogu P4147] 玉蟾宫'
tags:
  - DP
id: '105'
categories:
  - - 动态规划
  - - 网格DP
date: 2019-02-14 15:50:24
updated: 2019-02-14 15:50:24
---

* * *

# 题面

传送门:[洛谷](https://www.luogu.org/problemnew/show/P4147)

* * *

# Solution

裸的求极大子矩阵 感谢wzj dalao的教学 首先,有一个很显然但很重要的结论,那就是求极大子矩阵肯定要贴着边或一个障碍点,否则就会浪费 根据这个定理,我们可以考虑一种做法 我们可以枚举每一个可放置的点 我们可以很轻松的得知它与它左边的障碍点(或边界)的距离,也可以得知它上面与下面能扩展到哪里(即无障碍点最多能到哪里) 那这个点能扩出的长方形的最大面积就是它左边的上面与下面能扩展出来的距离的最小值\*它到左边障碍点的距离 然后取一个最大的面积就好

* * *

# Code

```cpp
//Luogu P4147 玉蟾宫
//May,9th,2018
//悬线法旋转90°求极大子矩阵
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
const int N=1000+10;
const int inf=0x3f3f3f3f;
int up[N][N],down[N][N],a[N][N],n,m;
int main()
{
    scanf("%d%d",&n,&m);
    char c[2];
    memset(a,0x80,sizeof a);
    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
        {
            scanf("%s",c+1);
            if(c[1]=='F') 
                a[i][j]=0;
        }

    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
            if(a[i][j]==0 and a[i-1][j]==0)
                up[i][j]=up[i-1][j]+1;
    for(int i=n;i>=1;i--)
        for(int j=1;j<=m;j++)
            if(a[i][j]==0 and a[i+1][j]==0)
                down[i][j]=down[i+1][j]+1;

    int ans=0,u=inf,d=inf,w=0;
    for(int i=1;i<=n;i++)
    {
        u=d=inf;
        w=0;
        for(int j=1;j<=m;j++)
            if(a[i][j]!=0)
            {
                u=d=inf;
                w=0;
            }
            else
            {
                u=min(u,up[i][j]);
                d=min(d,down[i][j]);
                ans=max(ans,(++w)*(u+d+1));
            }
    }
    printf("%d",ans*3);
    return 0;
}
```