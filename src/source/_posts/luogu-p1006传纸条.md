---
title: '[Luogu P1006]传纸条'
tags:
  - DP
id: '96'
categories:
  - - 动态规划
  - - 网格DP
date: 2019-02-12 23:52:12
updated: 2019-02-12 23:52:12
---

# 题面

传送门:[洛咕](https://www.luogu.org/problemnew/show/P1006)

* * *

# Solution

挺显然但需要一定理解的网络(应该是那么叫吧)DP 首先有一个显然但重要的结论要发现:从左上走到右下再从右下走回左上=从左上走两次到右下 那么接下来可以考虑: 设f\[i\]\[j\]\[k\]\[l\]为第一次走到了(i,j)第二次走到了(k,l) 在路径不交错为前提下的能取到的最大友好值 转移方程也挺好写的 考虑这种情况能从哪里转移过来就好(i,j)可以从(i-1,j)或(i,j-1)转移过来,(k,l)可以从(k-1,l)或(k-1,l-1)转移过来 排列组合一下,总共4种可能性,取个最大值再加上a\[i\]\[j\]和a\[k\]\[l\]就好 当然(i==k and j==l) 即两个点重合的情况直接continue,因为f 的意义是之前的不重合,当前的也不能重合 预处理整个f设为0就好 时间复杂度O(n^4) n=50,显然能过 接下来我们可以考虑一个优化 因为我们两次从左上到右下是一起走的 就有这么一个推论: i+j = k+l (画个图就好,挺好发现的) 既然这两个相等,也就意味着我们可以通过总和与i,k推算出j,l 然后我们的方程就可以优化成这样的: f\[i\]\[j\]\[k\]的意思为:走了i步,第一次走到了第j行,第二次走到了第k行 它们的橫坐标分别为:i-j+2,i-k+2 转移同理 这样,时间复杂度就可以优化为O(n^3),相较之前的可以称为巨大的飞跃 然后就OjbK了

* * *

# Code

```cpp
//Luogu P1006 传纸条
//May,4th,2018
//网格DP
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=50+5;
int f[2*N][N][N],a[N][N],n,m;
int main()
{
    m=read(),n=read();
    for(int i=1;i<=m;i++)
        for(int j=1;j<=n;j++)
            a[i][j]=read();

    int MAX=n+m;
    for(int i=1;i<=MAX;i++)
        for(int j=1;j<=m;j++)
            for(int k=1;k<=m;k++)
            {
                int X1=i-j+2,Y1=j,X2=i-k+2,Y2=k;
                if((X1==X2 and Y1==Y2)==false and X1>0 and X2>0 and X1<=n and X2<=n)
                {
                    f[i][j][k]=max(f[i][j][k],f[i-1][j][k]);
                    f[i][j][k]=max(f[i][j][k],f[i-1][j-1][k]);
                    f[i][j][k]=max(f[i][j][k],f[i-1][j][k-1]);
                    f[i][j][k]=max(f[i][j][k],f[i-1][j-1][k-1]);
                    f[i][j][k]+=a[j][X1]+a[k][X2];
                }
            }

    printf("%d",max(f[m+n-3][m][m-1],f[m+n-3][m-1][m]));
    return 0;
}
```

* * *

# 后记

当时想为什么不会交叉的时候考虑了挺久的,这种类型的网格DP还是得多学习一个,我啊,太naive了 我太弱了