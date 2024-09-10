---
title: '[Luogu P2617] Dynamic Rankings'
tags:
  - 数据结构
id: '406'
categories:
  - - 数据结构
  - - 整体二分
  - - 线段树
date: 2019-04-07 16:46:47
updated: 2019-04-07 16:46:47
---

# 题面

[P2617 Dynamic Rankings](https://www.luogu.org/problemnew/show/P2617)

* * *

# Solution

这题需要一个比较妙的操作。 首先，我们阅读题面，发现题目要求我们处理区间K大带单点修改的问题。我们考虑用整体二分来解决这个问题。 总所周知，**整体二分中的修改只能以“添加”的形式进行，而不能以“覆盖”的方式进行。但这里，我们修改一个位置的数之后，新的数会把原来的数覆盖掉。**如果我们不能处理好这个问题，整体二分一定会错。 因此，我们考虑添加一个“删除”操作来解决这个问题。**我们可以把这里的修改变为：删除原有的数+加入一个新的数。这样子，我们就把原来的“覆盖”问题转换为了“添加”问题**。 接下来，我们的操作就很套路了。我们直接二分一个$mid$,把所有$ans>mid$的询问和添加后的数$>mid$的修改丢到右边，其他的丢到左边。**对于删除操作，如果删除之前的数$<=mid$，我们就把删除操作丢到左边，否则丢到右边。** （因为我们会在二分的时候计算右边对左边的贡献，如果改前的数$<=mid$，有可能导致我们重复计算贡献，因此必须把这个删除操作丢到左边防止重复计算） 时间复杂度$O(nlogn^2)$ 就酱，这题就被我们切掉啦(～￣▽￣)～

* * *

# Code

### 数据生成器

```cpp
#include<iostream>
#include<cstdio>
#include<ctime>
#include<cstdlib>
using namespace std;
const int N=100000;
const int MAX=2000;
int main()
{
    freopen("2617.in","w",stdout);
    srand(time(NULL));

    int n=N,m=N;
    cout<<n<<" "<<m<<endl;
    for(int i=1;i<=n;i++)
        cout<<rand()%MAX+1<<" ";
    cout<<endl;

    for(int i=1;i<=m;i++)
    {
        int op=rand()%2;
        if(op==1)
        {
            int r=rand()%n+1,l=rand()%r+1,k=rand()%(r-l+1)+1;
            cout<<"Q "<<l<<" "<<r<<" "<<k<<endl;
        }
        else
            cout<<"C "<<rand()%n+1<<" "<<rand()%MAX+1<<endl;
    }
    return 0;
}

```

### 正解

```cpp
//Luogu P2617 Dynamic Rankings
//Apr,7th,2019
//整体二分妙题
#include<iostream>
#include<cstdio>
#include<vector>
#include<queue>
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
const int N=100000+100;
const int inf=0x3f3f3f3f;
int a[N],n;
struct BitTree
{
    int sum[N*400];
    int lowbit(int x)
    {
        return x&(-x);
    }
    void Add(int x,int w)
    {
        int tmp=Query(x,x);
        if(tmp==1 and w==0) w=-1;
        if(tmp==1 and w==1) w=0;
        for(;x<=n;x+=lowbit(x))
            sum[x]+=w;
    }
    int query(int x)
    {
        int t_ans=0;
        for(;x>=1;x-=lowbit(x))
            t_ans+=sum[x];
        return t_ans;
    }
    int Query(int l,int r)
    {
        return query(r)-query(l-1);
    }
}bit;
struct OP
{
    int l,r,x,no;
}op[N];
struct OP2
{
    int l,r;
    vector <OP> op;
};
queue <OP2> dl;
int m,q,ans[N];
int main()
{
    freopen("2617.in","r",stdin);
    freopen("2617.out","w",stdout);

    int TIME=clock();
    n=read(),m=read();
    for(int i=1;i<=n;i++)
        a[i]=read();
    char type[5];
    for(int i=1;i<=m;i++)
    {
        scanf("%s",type+1);
        if(type[1]=='Q')
            op[i].l=read(),op[i].r=read(),op[i].x=op[i].r-op[i].l+2-read(),op[i].no=++q;
        else
            op[i].l=read(),op[i].r=read();
    }

    OP2 now;
    now.l=1,now.r=inf;
    OP temp;
    for(int i=1;i<=n;i++)
        temp.l=i,temp.r=a[i],temp.no=0,
        now.op.push_back(temp);
    for(int i=1;i<=m;i++)
    {
        temp.no=-1,temp.l=op[i].l;
        if(op[i].no==0)
            now.op.push_back(temp);
        now.op.push_back(op[i]);
    }
    dl.push(now);

    while(dl.empty()==false)
    {
        now=dl.front();
        dl.pop();

        if(now.op.size()==0)
            continue;
        if(now.l==now.r)
        {
            for(int i=0;i<int(now.op.size());i++)
                ans[now.op[i].no]=now.l;
            continue;
        }
        int mid=(now.l+now.r)/2;
        OP2 L,R;
        for(int i=0;i<int(now.op.size());i++)
            if(now.op[i].no==0)
            {
                bit.Add(now.op[i].l,now.op[i].r>mid);
                if(now.op[i].r>mid)
                    R.op.push_back(now.op[i]);
                else
                    L.op.push_back(now.op[i]);
            }
            else if(now.op[i].no==-1)
            {
                if(bit.Query(now.op[i].l,now.op[i].l)==1)
                    R.op.push_back(now.op[i]);
                else
                    L.op.push_back(now.op[i]);
                bit.Add(now.op[i].l,0);
            }
            else
            {
                int tmp=bit.Query(now.op[i].l,now.op[i].r);
                if(tmp>=now.op[i].x)
                    R.op.push_back(now.op[i]);
                else
                {
                    now.op[i].x=now.op[i].x-tmp;
                    L.op.push_back(now.op[i]);
                }
            }

        for(int i=now.op.size()-1;i>=0;i--)
            if(now.op[i].no==0)
                bit.Add(now.op[i].l,0);
        L.l=now.l,L.r=mid;
        R.l=mid+1,R.r=now.r;
        dl.push(R);
        dl.push(L);
    }

    for(int i=1;i<=q;i++)
        printf("%d\n",ans[i]);
    cerr<<clock()-TIME<<endl;
    return 0;
}

```