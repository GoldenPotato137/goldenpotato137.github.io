---
title: '[Luogu P2824] [HEOI2016/TJOI2016]排序'
tags:
  - 数据结构
id: '184'
categories:
  - - 数据结构
  - - 线段树
date: 2019-02-22 17:33:55
updated: 2019-02-22 17:33:55
---

# 题面

传送门：[洛谷](https://www.luogu.org/problemnew/show/P2824)

* * *

# Solution

这题极其巧妙。 首先，如果直接做m次排序，显然会T得起飞。 注意一点：我们只需要找到一个数。 所以说，我们可以考虑一个绝妙的想法：**我们可以用二分答案的方法缩小要找的数的区间**。 考虑二分一个值，判定p位置的数排序之后，p位置上的数是否>=mid 如果>=mid,则向右找，否则向左找。 怎么判定p位置的数排序之后是否>=mid呢？ 考虑这样做：**扫描一遍原数组，>=mid的数赋值为1，<mid的数赋值为0。** 这样子，题目就变成了一个01序列排序。 这就很可做了，我们直接线段树维护之即可，我们只需要实现区间查询与区间赋值。 对于一个01区间排序，我们只需要知道这个区间有多少个0，多少个1，然后区间修改即可。 时间复杂度$O(m\*logn^2)$ . 就酱，这题就可以切掉啦(ﾉ´▽｀)ﾉ♪

* * *

# Code

```cpp
//Luogu  P2824 [HEOI2016/TJOI2016]排序
//Oct,19th,2018
//二分答案缩小范围+线段树妙题
#include<iostream>
#include<cstdio>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-')f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=30000+100;
int a[N],w[N];
struct SegmentTree
{
    #define lson (now<<1)
    #define rson (now<<11)
    #define mid ((now_l+now_r)>>1)
    static const int M=N<<2;
    int sum[M][2],lazy[M];
    inline void update(int now)
    {
        sum[now][0]=sum[lson][0]+sum[rson][0];
        sum[now][1]=sum[lson][1]+sum[rson][1];
    }
    inline void pushdown(int now,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            lazy[now]=2;
            return;
        }
        lazy[lson]=lazy[rson]=lazy[now];
        sum[lson][lazy[now]]=mid-now_l+1,sum[lson][!lazy[now]]=0;
        sum[rson][lazy[now]]=now_r-mid,sum[rson][!lazy[now]]=0;
        lazy[now]=2;
    }
    void Build(int now,int now_l,int now_r)
    {
        sum[now][0]=sum[now][1]=0;
        lazy[now]=2;
        if(now_l==now_r)
        {
            sum[now][w[now_l]]++;
            return;
        }
        Build(lson,now_l,mid);
        Build(rson,mid+1,now_r);
        update(now);
    }
    void Change(int L,int R,int x,int now,int now_l,int now_r)
    {
        if(L>R) return;
        if(lazy[now]!=2) pushdown(now,now_l,now_r);
        if(now_l>=L and now_r<=R)
        {
            sum[now][x]=now_r-now_l+1,sum[now][!x]=0;
            lazy[now]=x;
            return;
        }
        if(L<=mid) Change(L,R,x,lson,now_l,mid);
        if(R>mid) Change(L,R,x,rson,mid+1,now_r);
        update(now);
    }
    int Query(int L,int R,int x,int now,int now_l,int now_r)
    {
        if(lazy[now]!=2) pushdown(now,now_l,now_r);
        if(now_l>=L and now_r<=R)
            return sum[now][x];
        int ans=0;
        if(L<=mid) ans+=Query(L,R,x,lson,now_l,mid);
        if(R>mid) ans+=Query(L,R,x,rson,mid+1,now_r);
        return ans;
    }
    #undef lson
    #undef rson
    #undef mid
}sgt;
struct OP
{
    int type,L,R;
}op[N];
int n,m,p;
bool Check(int x)
{
    for(int i=1;i<=n;i++)
        if(a[i]>=x) w[i]=1;
        else w[i]=0;
    sgt.Build(1,1,n);
    for(int i=1;i<=m;i++)
    {
        int cnt0=sgt.Query(op[i].L,op[i].R,0,1,1,n),cnt1=op[i].R-op[i].L+1-cnt0;
        if(op[i].type==0)
            sgt.Change(op[i].L,op[i].L+cnt0-1,0,1,1,n),
            sgt.Change(op[i].L+cnt0,op[i].R,1,1,1,n);
        else
            sgt.Change(op[i].L,op[i].L+cnt1-1,1,1,1,n),
            sgt.Change(op[i].L+cnt1,op[i].R,0,1,1,n);
    }
    if(sgt.Query(p,p,1,1,1,n)==1) return true;
    return false;
}
int main()
{
    n=read(),m=read();
    for(int i=1;i<=n;i++)
        a[i]=read();
    for(int i=1;i<=m;i++)
        op[i].type=read(),op[i].L=read(),op[i].R=read();
    p=read();

    int L=0,R=n+100,ans=0;
    while(L<=R)
    {
        int mid=(L+R)/2;
        if(Check(mid)==true)
            ans=max(ans,mid),L=mid+1;
        else
            R=mid-1;
    }

    printf("%d",ans);
    return 0;
}
```