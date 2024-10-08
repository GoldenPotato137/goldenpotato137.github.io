---
title: '[Luogu P3527] [POI2011]MET-Meteors'
tags:
  - 数据结构
id: '389'
categories:
  - - 数据结构
  - - 整体二分
  - - 线段树
date: 2019-03-27 15:38:06
updated: 2019-03-27 15:38:06
---

# 题面

[P3527 \[POI2011\]MET-Meteors](https://www.luogu.org/problemnew/show/P3527)

* * *

# Solution

我是一直奉行坚决不写树状数组只写线段树理论的，直到这题....... 这题是一道整体二分的模板题。 首先，我们考虑只有一个国家的情况。这不SB问题么 我们可以二分一个答案，然后我们用线段树暴力模拟，暴力Check,复杂度$O(mlogn)$。 显然，如果我们每一个国家都这么搞一轮，复杂度达到了惊人的$O(n\\cdot m logn)$，这显然是要T飞的。 因此，我们就得请出伟大的整体二分了。整体二分，顾名思义，就是把所有询问一起二分答案。 考虑这样做，一开始，所有询问的答案均在$1,m+1$这个范围内(+1是为了方便判断不可行的情况)。接下来，我们二分一个$mid$，然后把所有答案$<=mid$的询问丢到$1,mid$区间，把所有在mid时间内不能完成的询问丢到右区间。 怎么判断一个询问是否能在mid时间内完成呢？暴力啊。我们直接用线段树暴力做个mid次修改，然后再暴力看每个国家是否满足即可。 接下来，我们只需要把这个东西递归下去做，直到区间长度为$1$为止。 什么你说，这玩意复杂度很大？的确很大，$O(nlog^2n)$呢。 怎么证呢？显然，我们发现这个递归最多log层，每层我们会把所有的修改操作都会做一遍，因此总复杂度两个log。 再给一个参考数据，30w数据下，2个log算出来的东西接近两个亿。好了，我想你知道了要写什么数据结构了....... 啥？所以说你还是义无反馈的写了线段树？[来试试在这里交吧](https://www.luogu.org/problemnew/show/U66611),可以检验你的程序的正确性（以及究竟要跑个多久）。 就酱，我们就把这题给切掉啦(～￣▽￣)～ 你很有可能被卡常及卡空间，当然，这就是后话了

* * *

# Code

```cpp
//Luogu P3527 [POI2011]MET-Meteors
//Mar,27th,2019
//整体二分+树状数组
#include<iostream>
#include<cstdio>
#include<vector>
#include<cstring>
#include<ctime>
using namespace std;
int read()
{
    int x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=300000+100;
const int inf=0x3f3f3f3f;
int n,m,K,ans[N],w[N];//w[i]第i个国家需要多少个陨石
struct BIT
{
    long long t[N<<2];
    inline int lowbit(int &x){return x&(-x);}
    void Init()
    {
        memset(t,0,sizeof t);
    }
    long long Query(int x)
    {
        long long sum=0;
        while(x>0)
        {
            sum+=t[x];
            x-=lowbit(x);
        }
        return sum;
    }
    void update(int x,int dlt)
    {
        while(x<=m)
        {   
            t[x]+=dlt;
            x+=lowbit(x);
        }
    }
    void Change(int l,int r,int w)
    {
        update(r+1,-w);
        update(l,w);
    }
}bit;
struct OP
{
    int l,r,w;
}op[N];
vector <int> a[N];
struct DL
{
    int l,r;
    vector <int> c;
}mqueue[N+5000];
int main()
{
    //int t=clock();
    //freopen("3527.in","r",stdin);
    //freopen("3527.out","w",stdout);

    n=read(),m=read();
    for(int i=1;i<=m;i++)
        a[read()].push_back(i);
    for(int i=1;i<=n;i++)
        w[i]=read();
    K=read();
    for(int i=1;i<=K;i++)
        op[i].l=read(),op[i].r=read(),op[i].w=read();

    int front=0,tail=0,T=1;//T:当前执行到了T时刻
    mqueue[tail].l=1,mqueue[tail].r=K+1;
    for(int i=1;i<=n;i++)
        mqueue[tail].c.push_back(i);
    tail++;
    while(tail!=front)
    {
        if(mqueue[front].l==mqueue[front].r)
        {
            for(int i=0;i<int(mqueue[front].c.size());i++)
                ans[mqueue[front].c[i]]=mqueue[front].l;
            front=(front+1)%(K+5000);
            continue;
        }
        int mid=(mqueue[front].l+mqueue[front].r)/2;
        if(T>mid)
            T=1,bit.Init();
        for(;T<=mid;T++)
            if(op[T].l<=op[T].r)
                bit.Change(op[T].l,op[T].r,op[T].w);
            else
                bit.Change(op[T].l,m,op[T].w),
                bit.Change(1,op[T].r,op[T].w);
        vector <int> l,r;
        for(int i=0;i<int(mqueue[front].c.size());i++)
        {
            long long t_ans=0,now=mqueue[front].c[i];
            for(int j=0;j<int(a[now].size());j++)
            {
                t_ans+=bit.Query(a[now][j]);
                if(t_ans>inf) break;
            }
            if(t_ans>=w[now])
                l.push_back(now);
            else
                r.push_back(now);
        }
        mqueue[tail].l=mqueue[front].l,mqueue[tail].r=mid,mqueue[tail++].c=l;
        tail%=(K+5000);
        mqueue[tail].l=mid+1,mqueue[tail].r=mqueue[front].r,mqueue[tail++].c=r;
        tail%=(K+5000);
        front=(front+1)%(K+5000);
    }

    for(int i=1;i<=n;i++)
        if(ans[i]==K+1)
            printf("NIE\n");
        else
            printf("%d\n",ans[i]);
    //cerr<<clock()-t<<endl;
    return 0;
}

```