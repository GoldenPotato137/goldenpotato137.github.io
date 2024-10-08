---
title: '[Luogu P2157][SDOI2009]学校食堂'
tags:
  - DP
id: '352'
categories:
  - - 动态规划
  - - 状压DP
date: 2019-03-12 10:09:07
updated: 2019-03-12 10:09:07
---

# 题面

[SDOI2009 学校食堂](https://www.luogu.org/problemnew/show/P2157)

* * *

# Solution

这是一道状压妙题。 首先，因为后面的东西能提到前面来做，导致了严重的后效性。为了消除这个后效性，考虑用状压来处理这个问题。 我们可以发现最多的提前量很小，只有7，考虑这样设： 设$f\[i\]\[j\]\[k\]$表示$\[1,i-1\]$已经完成了，从$i$开始往后7个的完成状态为$j$，上一个完成的相对$i-1$的位置为k。 转移比较正常：我们枚举一下下一个选哪一个，如果$\[i,x\]$会被新完成，就进到下$x$位。 我们这里的枚举要非常小心：这里枚举下一个能不能选有一个重要的限制：一路过来，一定要检查当前要选的这个能否提到没有选过的前面。 就酱，我们及就可以在理论上 AC这道题啦(\*´ﾟ∀ﾟ｀)ﾉ 当然，我们很有可能因为众多的细节调个半天

* * *

# Code

**本题细节较多，具体细节请看代码**

```cpp
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
const int N=1000+10;
const int M=(1<<8)+50;
const int K=20;
int f[N][M][K],a[N],b[N];
int n,m;
inline int real(int x,int k)
{
    return x+k-8;
}
inline int w(int x,int y)
{
    if(x==0) return 0;//要专门特判第一个转移
    return a[x]^a[y];
}
int main()
{
    //freopen("2157.in","r",stdin);
    //freopen("2157.out","w",stdout);
    //freopen("err.out","w",stderr);

    int T=read();
    for(;T>0;T--)
    {
        memset(f,0x3f,sizeof f);

        n=read();
        for(int i=1;i<=n;i++)
            a[i]=read(),b[i]=read();

        m=1<<8;
        b[0]=0x3f3f3f3f;
        f[1][0][8]=0;
        for(int i=1;i<=n;i++)
            for(int j=0;j<m;j++)
                for(int k=0;k<=16;k++)
                    if(real(i-1,k)>=0)
                    {
                        //cerr<<i-1<<" "<<j<<" "<<k-8<<" :"<<f[i][j][k]<<endl;
                        int t=b[i];
                        for(int o=1;o<=1+t and i-1+o<=n;o++)
                        {
                            if((j>>(o-1))%2==1) continue;//这个已经选过了
                            t=min(t,o-1+b[i-1+o]);//限制能取到的最大范围
                            int tmp=j+(1<<(o-1)),py=0;
                            while(tmp%2==1) tmp/=2,py++;//计算进位
                            /*if(f[i+py][tmp][8+o-py]>f[i][j][k]+w(real(i-1,k),i-1+o))
                                cerr<<i+py-1<<" "<<tmp<<" "<<o-py<<" "<<f[i][j][k]+w(real(i-1,k),i-1+o)<<endl;*/
                            f[i+py][tmp][8+o-py]=min(f[i+py][tmp][8+o-py],f[i][j][k]+w(real(i-1,k),i-1+o));
                        }
                        //cerr<<endl;
                    }

        int ans=0x3f3f3f3f;
        for(int i=0;i<=16;i++)
            ans=min(ans,f[n+1][0][i]);
        printf("%d\n",ans);
    }
    return 0;
}

```