---
title: '[Luogu P4173]残缺的字符串'
tags:
  - 数学
id: '253'
categories:
  - - FFT/NTT
  - - 卷积
  - - 数学
date: 2019-03-01 14:55:43
---

# 题面

传送门：[洛咕](https://www.luogu.org/problemnew/show/P4173)

* * *

# Solution

这题我写得脑壳疼，我好菜啊 好吧，我们来说正题。 这题.....emmmmmmm 显然KMP类的字符串神仙算法在这里没法用了。 那咋搞啊（或者说这题和数学有半毛钱关系啊） 我们考虑把两个字符相同强行变为一个数学关系，怎么搞呢？ 考虑这题是带通配符的，我们可以这样设: $C(x,y)=(A\[x\]-B\[y\])^2\*A\[x\]\*B\[y\]$ 因此，我们可以看出两个字符一样当且仅当$C(x,y)=0$ 因此，我们再设一个函数$P(x)$表示$B$串以第$x$项为结尾的长度为$m$的子串是否与$A$串匹配，显然有： $P(x)=\\sum\_{i=0}^{m-1}C(i,x-m+i+1)$ $P(x)=\\sum\_{i=0}^{m-1}(A\[i\]-B\[x-m+i+1\])^2\*A\[i\]\*B\[x-m+i+1\]$ 后面那个式子写的太蛋疼了，我们把$x-m+i+1$设为$j$吧。 $P(x)=\\sum\_{i=0}^{m-1}(A\[i\]-B\[j\])^2\*A\[i\]\*B\[j\]$ 大力展开得： $P(x)=\\sum\_{i=0}^{m-1}A\[i\]^3B\[j\]-2A\[i\]^2B\[j\]^2+A\[i\]B\[j\]^3$ 然后.....我们试着把$\\sum$展开？ $P(x)=\\sum\_{i=0}^{m-1}A\[i\]^3B\[j\]-\\sum\_{i=0}^{m-1}2A\[i\]^2B\[j\]^2+\\sum\_{i=0}^{m-1}A\[i\]B\[j\]^3$ 还是没法算啊...... 诶，等下，如果我们把$A$串反转为$A'$,肯定有$A\[i\]=A'\[m-i-1\]$ 然后这个$(m-i-1)+(j)$......不就是等于$x$嘛。 所以说我们马上就有： $P(x)=\\sum\_{i+j=x}A\[i\]^3B\[j\]-\\sum\_{i+j=x}2A\[i\]^2B\[j\]^2+\\sum\_{i+j=x}A\[i\]B\[j\]^3$ 哦豁，卷积，搞定~ 时间复杂度$O(nlogn)$ 搞定个鬼，这题要做7次FFT，常数爆大，我卡不进去 还请各位dalao赐教。

* * *

# Code

```cpp
#include<iostream>
#include<cstdio>
#include<complex>
#include<cmath>
#include<algorithm>
using namespace std;
const int M=300000+100;
const int N=M*4;
const double PI=acos(-1);
const double eps=1e-1;
typedef complex <double> cp;
cp omega(int K,int n)
{
    return cp(cos(2*PI*K/n),sin(2*PI*K/n));
}
inline void FFT(cp a[],int n,bool type)
{
    static int tmp[N],num=n-1,len;
    while(num!=0) num/=2,len++;
    for(int i=0,j;i<=n;i++)
    {
        for(j=0,num=i;j<len;j++)
            tmp[j]=num%2,num/=2;
        reverse(tmp,tmp+len);
        for(j=0,num=0;j<len;j++)
            num+=tmp[j]*(1<<j);
        if(i<num) swap(a[i],a[num]);
    }
    for(int l=2;l<=n;l*=2)
    {
        int m=l/2;
        cp x0=omega(1,l);
        if(type==true) x0=conj(x0);
        for(int i=0;i<n;i+=l)
        {
            cp x(1,0);
            for(int j=0;j<m;j++,x*=x0)
            {
                cp temp=x*a[i+j+m];
                a[i+j+m]=a[i+j]-temp;
                a[i+j]=a[i+j]+temp;
            }
        }
    }
}
char A[N],B[N];
int m,n,t=1;
cp S1[N],S2[N],S3[N],B1[N],B2[N],B3[N];
bool OK[N];
int main()
{
    scanf("%d%d%s%s",&m,&n,A,B);

    while(t<=(n+m)) t*=2;
    reverse(A,A+m); 
    for(int i=0;i<t;i++)
        A[i]=(A[i]=='*' or A[i]<'a' or A[i]>'z'?0:A[i]-'a'+1),
        B[i]=(B[i]=='*' or B[i]<'a' or B[i]>'z'?0:B[i]-'a'+1);
    for(int i=0;i<t;i++)
        S1[i]=A[i],S2[i]=A[i]*A[i],S3[i]=A[i]*A[i]*A[i];
    for(int i=0;i<t;i++)
        B1[i]=B[i],B2[i]=B[i]*B[i],B3[i]=B[i]*B[i]*B[i];
    FFT(S1,t,false);
    FFT(S2,t,false);
    FFT(S3,t,false);
    FFT(B1,t,false);
    FFT(B2,t,false);
    FFT(B3,t,false);

    for(int i=0;i<t;i++)
        S3[i]*=B1[i];
    for(int i=0;i<t;i++)
        S1[i]*=B3[i];
    for(int i=0;i<t;i++)
        S2[i]*=B2[i];
    for(int i=0;i<t;i++)
        S3[i]+=S1[i]-2.0*S2[i];
    FFT(S3,t,true);
    int cnt=0;
    for(int i=m-1;i<n;i++)
        if(fabs(S3[i].real()/t)<eps)
            OK[i]=true,cnt++;

    printf("%d\n",cnt);
    for(int i=m-1;i<n;i++)
        if(OK[i]==true)
            printf("%d ",i-m+1+1);
    return 0;
}

```