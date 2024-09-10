---
title: '[BZOJ 3744] Gty的妹子序列'
tags:
  - 数据结构
id: '364'
categories:
  - - 主席树
  - - 分块
  - - 数据结构
  - - 线段树
date: 2019-03-20 22:41:38
updated: 2019-03-20 22:41:38
---

# 题面

[3744: Gty的妹子序列](https://www.lydsy.com/JudgeOnline/problem.php?id=3744)

* * *

# Solution

这是一道分块妙题。 区间逆序对......log数据结构应该是没法搞的。 因此，我们考虑用分块解决这个问题。 **设$f\[i\]\[j\]$表示第$i$块与第$j$块的所有元素的逆序对个数** 这个东西我们可以考虑用线段树/树状数组直接搞，我们把所有数字从大到小插入，(数字相同的时候一起插入),每插入一种数字，我们可以直接计算它到其他所有块会新产生的逆序对数：即那个块的大小-已经填好的数字的个数。 上面的东西可以$O(n\\cdot \\sqrt n \\cdot logn)$预处理出来。 接下来，我们用**$g\[i\]\[j\]$表示从第$i$块开始，第$i$块与$i-j$块中所有元素的逆序对个数**，这个可以利用$f$直接前缀和计算。 那么$l,r$中所有元素的逆序对个数即为里面整块的$\\sum g\[i\]\[br\]+$零散的值的逆序对个数。对于零散的值的个数，我们可以直接用主席树求即可。 时间复杂度$O(n\\cdot \\sqrt n \\cdot logn)$,卡常。 如果您BZOJ卡不过的话，可以来luogu[这道题试试](https://www.luogu.org/problemnew/show/U66042) 就酱，我们又切掉一道题啦ヾ(o´∀｀o)ﾉ

* * *

# Code

```cpp
//[BZOJ 3744] Gty的妹子序列
//Mar,20th,2019
//分块求区间逆序对数
#include<iostream>
#include<cstdio>
#include<algorithm>
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
const int Q=225+20;
struct SegmentTree
{
    #define lson (now<<1)
    #define rson (now<<11)
    #define mid ((now_l+now_r)>>1)
    int sum[N<<2];
    inline void update(int now)
    {
        sum[now]=sum[lson]+sum[rson];
    }
    void Add(int x,int num,int now,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            sum[now]+=num;
            return;
        }
        if(x<=mid) Add(x,num,lson,now_l,mid);
        else Add(x,num,rson,mid+1,now_r);
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
struct PresidentTree
{
    #define mid ((now_l+now_r)>>1)
    static const int M=(50000<<2)*30;
    int sum[M],son[M][2],to;
    inline void update(int now)
    {
        sum[now]=sum[son[now][0]]+sum[son[now][1]];
    }
    void Add(int x,int now,int pre,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            sum[now]=sum[pre]+1;
            return;
        }
        if(x<=mid)
        {
            Add(x,son[now][0]=++to,son[pre][0],now_l,mid);
            son[now][1]=son[pre][1];
        }
        else
        {
            Add(x,son[now][1]=++to,son[pre][1],mid+1,now_r);
            son[now][0]=son[pre][0];
        }
        update(now);
    }
    int Query(int l,int r,int now,int pre,int now_l,int now_r)
    {
        if(now_l>=l and now_r<=r) return sum[now]-sum[pre];
        int t_ans=0;
        if(l<=mid)  t_ans+=Query(l,r,son[now][0],son[pre][0],now_l,mid);
        if(r>mid)   t_ans+=Query(l,r,son[now][1],son[pre][1],mid+1,now_r);
        return t_ans;
    } 
    #undef mid
}prt;
struct NUM
{
    int w,no;
}num[N];
bool cmp(NUM x, NUM y)
{
    return x.w>y.w;
}
bool cmp2(NUM x,NUM y)
{
    return x.no<y.no;
}
int t_a[N],n,m,block,cnt[Q],msize[Q];
int f[Q][Q],g[Q][Q],root[N];
int main()
{
    int t=clock();
    freopen("3744.in","r",stdin);
    freopen("3744.out","w",stdout);

    n=read();
    for(int i=1;i<=n;i++)
        num[i].w=t_a[i]=read(),num[i].no=i;

    sort(t_a+1,t_a+1+n);
    m=unique(t_a+1,t_a+1+n)-t_a-1;
    for(int i=1;i<=n;i++)
        num[i].w=lower_bound(t_a+1,t_a+1+m,num[i].w)-t_a;

    sort(num+1,num+1+n,cmp);
    int size=int(sqrt(n));
    msize[0]=size-1,msize[n/size]=n%size+1;
    for(int i=1;i<n/size;i++)
        msize[i]=size;
    block=n/size;
    for(int i=1;i<=n;i++)
        sgt.Add(i,1,1,1,n);
    static int mqueue[N],tail;
    int to=0;
    while(to<n)
    {
        tail=0;
        for(to++;to<=n;to++)
        {
            sgt.Add(num[to].no,-1,1,1,n);
            cnt[num[to].no/size]++;
            mqueue[tail++]=to;
            if(num[to+1].w != num[to].w)
                break;
        }
        for(int t=0;t<tail;t++)
        {
            int i=mqueue[t];
            for(int j=num[i].no/size+1;j<=block;j++)
                f[j][num[i].no/size]+=msize[j]-cnt[j],
                f[num[i].no/size][j]+=msize[j]-cnt[j];
            f[num[i].no/size][num[i].no/size]+=sgt.Query(num[i].no,(num[i].no/size+1)*size-1,1,1,n);
        }
    }
    for(int i=0;i<=block;i++)
    {
        g[i][i]=f[i][i];
        for(int j=i+1;j<=block;j++)
            g[i][j]+=g[i][j-1]+f[i][j];
    }

    /*for(int i=0;i<=block;i++)
    {
        for(int j=i;j<=block;j++)
            cerr<<g[i][j]<<" ";
        cerr<<endl;
    }*/

    sort(num+1,num+1+n,cmp2);
    for(int i=1;i<=n;i++)
    {
        root[i]=++prt.to;
        prt.Add(num[i].w,root[i],root[i-1],0,m);
    }
    int q=read(),ans=0;
    for(int i=1;i<=q;i++)
    {
        int l=read()^ans,r=read()^ans;
        ans=0;
        for(int j=l;j<min((l/size+1)*size,r+1);j++)//左散块
            ans+=prt.Query(0,num[j].w-1,root[r],root[j],0,m);
        if(l/size+1 <= r/size-1)//中间块
            for(int j=l/size+1;j<=r/size-1;j++)
                ans+=g[j][r/size-1];
        if(l/size!=r/size)//右散块
            for(int j=r/size*size;j<=r;j++)
            {
                ans+=prt.Query(0,num[j].w-1,root[r],root[j],0,m);
                if(l/size+1 <= r/size-1)
                    ans+=prt.Query(num[j].w+1,m,root[r/size*size-1],root[(l/size+1)*size-1],0,m);
            }
        printf("%d\n",ans);
    }

    cerr<<clock()-t;
    return 0;
}

```