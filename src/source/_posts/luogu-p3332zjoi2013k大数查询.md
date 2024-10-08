---
title: '[Luogu P3332][ZJOI2013]K大数查询'
tags:
  - 数据结构
id: '391'
categories:
  - - 数据结构
  - - 整体二分
  - - 线段树
date: 2019-03-27 15:55:12
updated: 2019-03-27 15:55:12
---

# 题面

[P3332 \[ZJOI2013\]K大数查询](https://www.luogu.org/problemnew/show/P3332)

* * *

# Solution

这是一道不辣么模板的整体二分题。 首先，我们先来假设一下只有一个询问应该怎么搞。 考虑这样做：我们先二分一个答案，修改中，如果所要修改的数大于mid，则在这段区间中每个数加上1。否则什么都不做。这样一来，最后我们只需要看一下询问的区间的区间和是否大于$K$即可。 接下来，我们考虑如何把所有询问一起来二分。 同样还是二分一个答案，把所有答案大于mid的询问丢到右边，其余丢到左边即可。 接下来，我们可以显然发现对于右边的区间，**所有修改$<=mid$的修改都是没有意义的。因此，我们考虑把所有$<=mid$的修改放在右边，其余的放在左边**。显然，**对于左边所有的询问，$>=mid$的修改一定会对左边造成影响，因此还要把所有在左边的询问减上对应的值**（在这个询问之前所有的操作对它产生的1的数量）。 对于询问和修改的顺序问题：我们分别保证询问与修改按照时间有序，用两个指针分开处理，每次询问之前把在它之前的修改全部做了即可。这样就可以保证时间合法。 时间复杂度$O(nlog^2n)$ 就酱，我们又切掉一道题啦(ﾉ≧∀≦)ﾉ

* * *

# Code

**数据生成器**

```cpp
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<ctime>
using namespace std;
const int N=500;
const int NUM=1000;
int cnt[N+5];
int main()
{
    srand(time(NULL));
    freopen("3332.in","w",stdout);

    int n=N,m=N;
    cout<<n<<" "<<m<<endl;
    for(int i=1;i<=m;i++)
    {
        int op=rand()%2,a=rand()%n+1,b=rand()%n+1,c=rand()%NUM+1;
        if(a>b) swap(a,b);
        if(op==1)
        {
            cout<<op<<" "<<a<<" "<<b<<" "<<c<<endl;
            for(int j=a;j<=b;j++)
                cnt[j]++;
        }
        else
        {
            int mtot=0;
            for(int j=a;j<=b;j++)
                mtot+=cnt[j];
            if(mtot==0)
            {
                i--;
                continue;
            }   
            c=rand()%mtot+1;
            cout<<"2"<<" "<<a<<" "<<b<<" "<<c<<endl;
        }
    }
    return 0;
}

```

**正解**

```cpp
//Luogu P3332 [ZJOI2013]K大数查询 
//Mar,27th,2019
//整体二分+线段树
#include<iostream>
#include<cstdio>
#include<cstring>
#include<vector>
#include<cmath>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=50000+100;
struct SegmentTree
{
    #define lson (now<<1)
    #define rson (now<<11)
    #define mid ((now_l+now_r)>>1)
    long long sum[N<<2];
    int lazy[N<<2];
    inline void update(int now)
    {
        sum[now]=sum[lson]+sum[rson];
    }
    inline void pushdown(int now,int now_l,int now_r)
    {
        if(now_l!=now_r)
        {
            sum[lson]+=lazy[now]*(mid-now_l+1),sum[rson]+=lazy[now]*(now_r-mid);
            lazy[lson]+=lazy[now],lazy[rson]+=lazy[now];
        }
        lazy[now]=0;
    }
    void Change(int l,int r,int w,int now,int now_l,int now_r)
    {
        pushdown(now,now_l,now_r);
        if(now_l>=l and now_r<=r)
        {
            lazy[now]=w,sum[now]+=1ll*w*(now_r-now_l+1);
            return;
        }
        if(l<=mid) Change(l,r,w,lson,now_l,mid);
        if(r>mid) Change(l,r,w,rson,mid+1,now_r);
        update(now);
    }
    long long Query(int l,int r,int now,int now_l,int now_r)
    {
        pushdown(now,now_l,now_r);
        if(now_l>=l and now_r<=r)
            return sum[now];
        long long t_ans=0;
        if(l<=mid) t_ans+=Query(l,r,lson,now_l,mid);
        if(r>mid) t_ans+=Query(l,r,rson,mid+1,now_r);
        return t_ans;
    }
    #undef lson
    #undef rson
    #undef mid
}sgt;
struct OP
{
    int l,r,no;
    long long w;
}op1[N],op2[N];//op1:询问，op2:修改
struct DL
{
    int l,r;
    vector <OP> op,qur;
}mqueue[2*N];
int n,m,q,p,K=2*N-20;//q:询问，p:修改
int ans[N];
int main()
{
    freopen("3332.in","r",stdin);
    freopen("3332.out","w",stdout);

    n=read(),m=read();
    for(int i=1;i<=m;i++)
    {
        int op=read();
        if(op==1)
            op2[++p].l=read(),op2[p].r=read(),op2[p].w=read(),op2[p].no=i;
        else
            op1[++q].l=read(),op1[q].r=read(),op1[q].w=read(),op1[q].no=i;
    }

    int front=0,tail=0;
    mqueue[tail].l=-N,mqueue[tail].r=N;
    for(int i=1;i<=p;i++)
        mqueue[tail].op.push_back(op2[i]);
    for(int i=1;i<=q;i++)
        mqueue[tail].qur.push_back(op1[i]);
    tail++;
    memset(ans,0x3f,sizeof ans);

    while(front!=tail)
    {
        //cerr<<front<<" "<<tail<<" "<<mqueue[front].l<<" "<<mqueue[front].r<<endl;
        if(mqueue[front].l==mqueue[front].r)
        {
            for(int i=0;i<int(mqueue[front].qur.size());i++)
                ans[mqueue[front].qur[i].no]=mqueue[front].l;
        }
        else if(mqueue[front].qur.size()>0)
        {
            int mid=int(floor((mqueue[front].l+mqueue[front].r)/2.0)),T=0;
            DL L,R;
            for(int i=0;i<int(mqueue[front].qur.size());i++)
            {
                for(;T<int(mqueue[front].op.size()) and mqueue[front].qur[i].no>mqueue[front].op[T].no;T++)
                {
                    sgt.Change(mqueue[front].op[T].l,mqueue[front].op[T].r,mqueue[front].op[T].w>mid,1,1,n);
                    if(mqueue[front].op[T].w>mid)
                        R.op.push_back(mqueue[front].op[T]);
                    else
                        L.op.push_back(mqueue[front].op[T]);
                }
                long long t=sgt.Query(mqueue[front].qur[i].l,mqueue[front].qur[i].r,1,1,n);
                if(t>=mqueue[front].qur[i].w)
                    R.qur.push_back(mqueue[front].qur[i]);
                else
                {
                    mqueue[front].qur[i].w-=t;
                    L.qur.push_back(mqueue[front].qur[i]);
                }
            }
            for(int i=0;i<T;i++)
                sgt.Change(mqueue[front].op[i].l,mqueue[front].op[i].r,-(mqueue[front].op[i].w>mid),1,1,n);
            L.l=mqueue[front].l,L.r=mid;
            R.l=mid+1,R.r=mqueue[front].r;
            mqueue[tail]=L,tail=(tail+1)%K;
            mqueue[tail]=R,tail=(tail+1)%K;
        }
        front=(front+1)%K;
    }

    for(int i=1;i<=m;i++)
        if(ans[i]!=0x3f3f3f3f)
            printf("%d\n",ans[i]);
    return 0;
}

```