---
title: '[Luogu P3810] 【模板】三维偏序（陌上花开）'
tags:
  - 数据结构
id: '386'
categories:
  - - CDQ分治
  - - 数据结构
  - - 线段树
date: 2019-03-27 15:11:54
updated: 2019-03-27 15:11:54
---

# 题面

[P3810 【模板】三维偏序（陌上花开）](https://www.luogu.org/problemnew/show/P3810)

* * *

# Solution

这是一道CDQ分治的模板题。 题目要我们求的是$(a,b,c)$这样的三维“顺序对”的数量。 **考虑我们把所有的数按照以$a$为第一关键字，以$b$为第二关键字，以$c$为第三关键字来排序。** 这样子，我们就可以保证**有可能与某个数形成“顺序对”的数一定在它的左边**。 我们都知道，归并排序能用来求逆序对的数量，在这里，也能用类似的方法来求“顺序对”的数量。 接下来我们考虑对这个排好序的序列做**以$b$为关键字的类似于归并排序的操作**。 这里我们要排序的是$\[1,n\]$这一整个序列，我们要递归下去做，假设我们现在已经求出了$\[1,mid\]$与$\[mid+1,n\]$这两个区间内部的“顺序对”的数量，现在我们考虑求出跨越两个区间的顺序对。**因为我们归并排序前已经按照$a$排序过了，所以这里有且只有可能右边块的数会与左边块的数会形成“顺序对”。** 接下来，我们考虑维护左右两个块两个指针，**保证右边块当前看的数的$b$比左边指针扫到的所有的数的$b$都大。**这时候，**我们只需要找到左边扫到的数的$c$有多少个比当前扫到的数的$c$小。**这个东西我们用一个树状数组/线段树直接维护即可。 最后我们以$b$为关键字排序这两个区间，递归回去继续做即可。 注意一点细节：有可能会有几个元素的$(a,b,c)$全部相等，我们在刚刚的计算中是不会算到它们的相互影响的。对于这种情况，我们要提前为他们算上内部的影响，然后去重做即可。 在这里，我们在第二维上做的类似于归并排序的操作即为CDQ分治。 时间复杂度$O(nlog^2n)$ 就酱，我们又切掉一道题啦(๑¯∀¯๑)

* * *

# Code

```cpp
//Luogu P3810 【模板】三维偏序（陌上花开） 
//Mar,26th,2019
//CDQ分治+线段树+排序维护三维偏序
#include<iostream>
#include<cstdio>
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
const int M=200000+100;
struct node
{
    int x,y,z,no,ans;
    friend bool operator == (node a,node b)
    {
        return (a.x==b.x and a.y==b.y and a.z==b.z);
    }
}nd[N];
bool cmp(node a,node b)
{
    if(a.x==b.x)
    {
        if(a.y==b.y)
            return a.z<b.z;
        return a.y<b.y;
    }
    return a.x<b.x;
}
bool cmp2(node a,node b)
{
    if(a.y==b.y) return a.z<b.z;
    return a.y<b.y;
}
struct SegmentTree
{
    #define lson (now<<1)
    #define rson (now<<11)
    #define mid ((now_l+now_r)>>1)
    int sum[M<<2];
    inline void update(int now)
    {
        sum[now]=sum[lson]+sum[rson];
    }
    void Add(int x,int v,int now,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            sum[now]+=v;
            return ;
        }
        if(x<=mid)  Add(x,v,lson,now_l,mid);
        else Add(x,v,rson,mid+1,now_r);
        update(now);
    }
    int Query(int l,int r,int now,int now_l,int now_r)
    {
        if(now_l>=l and now_r<=r)
            return sum[now];
        int t_ans=0;
        if(l<=mid) t_ans+=Query(l,r,lson,now_l,mid);
        if(r>mid) t_ans+=Query(l,r,rson,mid+1,now_r);
        return t_ans;
    }
    #undef lson
    #undef rson
    #undef mid
}sgt;
int n,m,K,belong[N],cnt[N],ans[N],cnt_belong,ans2[N];
void CDQ(int l,int r)
{
    if(l==r) return;
    int mid=(l+r)/2;
    CDQ(l,mid);
    CDQ(mid+1,r);

    int ptl=l;
    for(int i=mid+1;i<=r;i++)
    {
        for(;nd[ptl].y<=nd[i].y and ptl<=mid;ptl++)
            sgt.Add(nd[ptl].z,cnt[nd[ptl].no],1,0,K);
        ans[nd[i].no]+=sgt.Query(0,nd[i].z,1,0,K);
    }
    ptl=l;
    for(int i=mid+1;i<=r;i++)
        for(;nd[ptl].y<=nd[i].y and ptl<=mid;ptl++)
            sgt.Add(nd[ptl].z,-cnt[nd[ptl].no],1,0,K);

    sort(nd+l,nd+r+1,cmp2);
}
int main()
{
    n=read(),K=read();
    for(int i=1;i<=n;i++)
        nd[i].x=read(),nd[i].y=read(),nd[i].z=read(),nd[i].no=i;

    sort(nd+1,nd+1+n,cmp);
    for(int i=1;i<=n;i++)
        if(nd[i]==nd[i-1])
            cnt[cnt_belong]++,belong[nd[i].no]=cnt_belong;
        else
            cnt[++cnt_belong]++,belong[nd[i].no]=cnt_belong;
    for(int i=1;i<=cnt_belong;i++)
        ans[i]+=cnt[i]-1;
    m=unique(nd+1,nd+1+n)-nd-1;
    for(int i=1;i<=m;i++)
        nd[i].no=i;

    CDQ(1,m);

    for(int i=1;i<=n;i++)
        ans2[ans[belong[i]]]++;
    for(int i=0;i<n;i++)
        printf("%d\n",ans2[i]);
    return 0;
}

```