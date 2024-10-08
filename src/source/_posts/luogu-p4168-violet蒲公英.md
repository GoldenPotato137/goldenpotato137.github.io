---
title: '[Luogu P4168] [Violet]蒲公英'
tags:
  - 数据结构
id: '297'
categories:
  - - 分块
  - - 数据结构
date: 2019-03-05 15:04:20
updated: 2019-03-05 15:04:20
---

# 题面

[洛咕](https://www.luogu.org/problemnew/show/P4168)

* * *

# Solution

题目要求求出区间众数，强制在线。 区间众数是一个比较尴尬的问题，我们无法用区间数据结构来处理这个问题，因为我们没法很好的合并区间众数的答案。 既然区间数据结构解决不了这个问题，我们可以考虑一下使用基于分块的算法，例如莫队。 这题用莫队非常好处理，不幸的是，这题要求强制在线。 因此我们考虑使用分块算法。 分块算法的核心在于把一整个块的信息压缩起来以便快速处理。 我们要查询一段区间的众数，我们可以考虑这样搞：**对于这个区间内连续的块，我们先快速地查询这个连续的块中的众数，然后我们暴力处理这个区间剩余的左右两个零散的点，开一个桶暴力维护这些零散的点每个颜色出现的次数，每新加入一个点，就与整个区间的答案比较一下，如果更优就替换答案。** 为了实现上面那个思路，我们必须要实现两点：**快速求出一段连续块的每个颜色出现的次数，快速求出一段连续块的众数。** 对于第一个问题，解决方法很简单，我们暴力做**前缀和**即可，复杂度$O(n\*\\sqrt n)$

```cpp
for(int i=1;i<=cnt_block;i++)
    {
        for(int j=1;j<=to;j++)
            pre[i][j]=pre[i-1][j];
        for(int j=(i-1)*size;j<i*size;j++)
            pre[i][a[j]]++;
    }
```

对于第二个问题，我们可以考虑把每段连续块的答案预处理出来。 具体做法是：我们枚举每个块，然后我们暴力往后扫描，每扫到一个块的结尾就记录答案。

```cpp
cnt[0]=-0x3f3f3f3f;
    for(int i=1;i<=cnt_block;i++)
    {
        int t_ans=0;
        for(int j=(i-1)*size;j<=n;j++)
        {
            cnt[a[j]]++;
            if(cnt[a[j]]>cnt[t_ans] or (cnt[a[j]]==cnt[t_ans] and a[j]<t_ans)) 
                t_ans=a[j];
            if((j+1)%size==0)
                f[i][j/size+1]=t_ans;
        }
        memset(cnt,0,sizeof cnt);
        cnt[0]=-0x3f3f3f3f;
    }
```

就酱，这题就被我们搞定啦~ 复杂度$O(n\\sqrt n)$

* * *

# Code

**本题实现上有较多细节要处理，请小心**

```cpp
//Luogu P4168 [Violet]蒲公英
//Feb,4th,2019
//分块套路题
#include<iostream>
#include<cstdio>
#include<cmath>
#include<algorithm>
#include<cstring>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=40000+1000;
const int M=200+10;
int n,m,a[N],b[N],t_n,mmap[N];//离散值->原值
int f[M][M],pre[M][N];//f[i][j]:块i到j的众数，pre[i][j]：到i块为止，颜色j的出现次数前缀和
int cnt[N];//零时记录每个元素出现次数
int main()
{
    //freopen("testdata.in","r",stdin);
    //freopen("4168.out","w",stdout);

    t_n=n=read(),m=read();
    for(int i=1;i<=n;i++)
        a[i]=read(),b[i]=a[i];

    sort(b+1,b+1+n);
    int to=0,last=0;
    for(int i=1;i<=n;i++)
        if(b[i]!=last)
            last=b[i],b[++to]=b[i];
    for(int i=1;i<=n;i++)
    {
        int t=lower_bound(b+1,b+1+to,a[i])-b;
        mmap[t]=a[i],a[i]=t;
    }
    int size=int(sqrt(n)),cnt_block=n/size+1;
    n=cnt_block*size-1;
    for(int i=1;i<=cnt_block;i++)
    {
        for(int j=1;j<=to;j++)
            pre[i][j]=pre[i-1][j];
        for(int j=(i-1)*size;j<i*size;j++)
            pre[i][a[j]]++;
    }
    cnt[0]=-0x3f3f3f3f;
    for(int i=1;i<=cnt_block;i++)
    {
        int t_ans=0;
        for(int j=(i-1)*size;j<=n;j++)
        {
            cnt[a[j]]++;
            if(cnt[a[j]]>cnt[t_ans] or (cnt[a[j]]==cnt[t_ans] and a[j]<t_ans)) 
                t_ans=a[j];
            if((j+1)%size==0)
                f[i][j/size+1]=t_ans;
        }
        memset(cnt,0,sizeof cnt);
        cnt[0]=-0x3f3f3f3f;
    }

    int ans=0;
    for(int i=1;i<=m;i++)
    {
        int l=read(),r=read();
        l=(l+ans-1)%t_n+1,r=(r+ans-1)%t_n+1;
        if(l>r) swap(l,r);

        int bl=l/size+1,br=r/size+1;
        ans=0;
        if(bl+1<=br-1)
            ans=f[bl+1][br-1];
        for(int j=l;j<bl*size and j<=r;j++)
        {
            cnt[a[j]]++;
            int tmp1=cnt[a[j]],tmp2=cnt[ans];
            if(bl+1<=br-1)
                tmp1+=pre[br-1][a[j]]-pre[bl][a[j]],
                tmp2+=pre[br-1][ans]-pre[bl][ans];
            if(tmp1>tmp2 or (tmp1==tmp2 and a[j]<ans)) 
                ans=a[j];
        }
        if(bl!=br)
            for(int j=(br-1)*size;j<=r;j++)
            {
                cnt[a[j]]++;
                int tmp1=cnt[a[j]],tmp2=cnt[ans];
                if(bl+1<=br-1)
                    tmp1+=pre[br-1][a[j]]-pre[bl][a[j]],
                    tmp2+=pre[br-1][ans]-pre[bl][ans];
                if(tmp1>tmp2 or (tmp1==tmp2 and a[j]<ans)) 
                    ans=a[j];
            }

        for(int j=l;j<bl*size and j<=r;j++)
            cnt[a[j]]--;
        if(bl!=br)
            for(int j=(br-1)*size;j<=r;j++)
                cnt[a[j]]--;
        cnt[0]=-0x3f3f3f3f;

        ans=mmap[ans];
        printf("%d\n",ans);
    }
    return 0;
}

```