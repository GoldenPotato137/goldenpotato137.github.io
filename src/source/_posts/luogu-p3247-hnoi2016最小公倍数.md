---
title: '[Luogu P3247] [HNOI2016]最小公倍数'
tags:
  - 数据结构
id: '356'
categories:
  - - 分块
  - - 并查集
  - - 数据结构
date: 2019-03-19 19:24:56
updated: 2019-03-19 19:24:56
---

# 题面

[P3247 \[HNOI2016\]最小公倍数](https://www.luogu.org/problemnew/show/P3247)

* * *

# Solution

这是一道妙题。 首先，根据常识，题面要我们求的是找一条从s,t的路径，使得路径上$max\\ a=a,max\\ b=b$。 这咋求呢？我们会发现，**我们要求的路径本质上是一个连通块，连通块可以考虑用并查集处理**。 接下来考虑对a分块，先把所有的边按照$a$来排序，再分块，每个块里连的所有边保证$<=a\_{\[size\*x\]}$，如下图所示： ![AuMb0e.png](https://s2.ax1x.com/2019/03/19/AuMb0e.png) 接下来，我们考虑把所有询问按照**b从小到大**排序去一个个计算，每计算一个询问之前，把$b<=q\[i\].b$的边全部都对应地塞到联通快里面去(根据我们之前分块的定义，每条边说要塞入的并查集一定为从某个连通块开始一直往后到最后一个块为止)。 接下来，我们对应的去找最大的$a<=q\[i\].a$的连通块，然后把一些还零散在外面的边全部塞到那个连通块里面，**这个连通块里面所有的边一定能保证$a<=q\[i\].a,b<=q\[i\].b$**，我们只需要对应的看看$u,v$是否联通，它们所在的连通块的$max\_a,max\_b$是否满足要求即可。 我们每做完一个操作后，必须把之前连的零散的边给撤销掉。因此，我们这里必须用**按秩合并的并查集**。我们可以通过用一个栈/队列记录所有的修改操作(改fa/改max)一个一个改回去即可。 因为我们这里有$\\sqrt m$个块，每次操作的零散边不超过$\\sqrt m$条，因此总复杂度应该是$O(n \\cdot \\sqrt m)$

* * *

# Code

**数据生成器**

```cpp
#include<iostream>
#include<cstdio>
#include<ctime>
#include<cstdlib>
using namespace std;
const int N=30;
const int M=80;
const int MAX=16;
bool vis[MAX+50];
long long mrand()
{
    long long temp=(1ll*rand()*rand())%MAX,op=rand()%4;
    /*while(op==0 and vis[temp]==false)
        temp=(1ll*rand()*rand())%MAX;*/
    vis[temp]=true;
    return temp;
}
int main()
{
    srand(time(NULL));
    freopen("3247.in","w",stdout);

    int n=N,m=M;
    cout<<n<<" "<<m<<endl;
    for(int i=1;i<=m;i++)
        cout<<rand()%n+1<<" "<<rand()%n+1<<" "<<mrand()<<" "<<mrand()<<endl;

    int q=N;
    cout<<q<<endl;
    for(int i=1;i<=q;i++)
        cout<<rand()%n+1<<" "<<rand()%n+1<<" "<<mrand()<<" "<<mrand()<<endl;
    return 0; 
}

```

**请注意特判(0,0)边构成自环的情况，我因为这个破事WA了半天**

```cpp
#include<iostream>
#include<cstdio>
#include<cmath>
#include<cstdlib>
#include<algorithm>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=50000+100;
const int M=100000+100;
const int Q=1000;
struct line
{
    int s,t,a,b,ans,no;
    friend bool operator < (line x,line y)
    {
        return x.a<y.a;
    }
}l[M],l2[M],q[N];
bool cmp1(line x,line y)
{
    return x.a<y.a;
}
bool cmp2(line x,line y)
{
    return x.b<y.b;
}
bool cmp3(line x,line y)
{
    return x.no<y.no;
}
int n,m,K,block[Q];//block[i]:记录第i块的a值
struct SIT
{
    int type,x,num1,num2;//type=0:改fa;type=1:改max
}mstack[N];
int top;
struct UnF
{
    int fa[N],size[N],MAX_a[N],MAX_b[N];
    int FindFather(int x)
    {
        if(fa[x]==0) return x;
        return FindFather(fa[x]);
    }
    void Merge(int x,int y,int a,int b,bool type)//type=0：不撤回;=1:要撤回
    {
        int fa1=FindFather(x),fa2=FindFather(y);
        if(size[fa1]>size[fa2]) 
            swap(x,y),swap(fa1,fa2);
        if(type==1)
            mstack[++top].type=1,mstack[top].x=fa2,
            mstack[top].num1=MAX_a[fa2],mstack[top].num2=MAX_b[fa2];
        MAX_a[fa2]=max(max(MAX_a[fa2],MAX_a[fa1]),a);
        MAX_b[fa2]=max(max(MAX_b[fa2],MAX_b[fa1]),b);   
        if(fa1==fa2)
            return;
        if(type==1)
            mstack[++top].type=0,mstack[top].x=fa1,mstack[top].num1=fa2,mstack[top].num2=size[fa1];
        fa[fa1]=fa2,size[fa2]+=size[fa1];
    }
    void Undo()
    {
        for(;top>0;top--)
        {
            if(mstack[top].type==0)
                fa[mstack[top].x]=0,size[mstack[top].num1]-=mstack[top].num2;
            else
                MAX_a[mstack[top].x]=mstack[top].num1,
                MAX_b[mstack[top].x]=mstack[top].num2;
        }
    }
    int Query(int x,int y,int a,int b)
    {
        if(x==y and a==0 and b==0)
            return size[FindFather(x)]!=1;
        int fa=FindFather(x);
        if(FindFather(x)!=FindFather(y)) return false;
        if(MAX_a[fa]!=a or MAX_b[fa]!=b)
            return false;
        return true;
    }
}unf[Q];
int main()
{
    int t=clock();
    n=read(),m=read();
    for(int i=1;i<=m;i++)
    {
        l[i].s=read(),l[i].t=read(),l[i].a=read(),l[i].b=read();
        l2[i]=l[i];
    }
    K=read();
    for(int i=1;i<=K;i++)
        q[i].s=read(),q[i].t=read(),q[i].a=read(),q[i].b=read(),q[i].no=i;

    int size=int(sqrt(m*20)),cnt=m/size;        
    for(int i=0;i<=cnt;i++)
        for(int j=1;j<=n;j++)
            unf[i].size[j]=1;
    sort(l+1,l+1+m,cmp1);
    sort(l2+1,l2+1+m,cmp1);
    for(int i=0;i<=m/size;i++)
        block[i]=l[i*size].a;
    sort(q+1,q+1+K,cmp2);
    sort(l+1,l+1+m,cmp2);

    int to=1;//记录当前执行到第to条边
    for(int i=1;i<=K;i++)
    {
        //cerr<<i<<endl;
        for(;l[to].b<=q[i].b and to<=m;to++)
        {
            int begin=lower_bound(block,block+1+cnt,l[to].a)-block;
            for(int j=begin;j<=cnt;j++)
                unf[j].Merge(l[to].s,l[to].t,l[to].a,l[to].b,0);
        }
        int t=upper_bound(block,block+1+cnt,q[i].a)-block-1;
        line tmp;tmp.a=block[t];
        for(int j=upper_bound(l2+1,l2+1+m,tmp)-l2;j<=m and l2[j].a<=q[i].a;j++)
            if(l2[j].b<=q[i].b)
                unf[t].Merge(l2[j].s,l2[j].t,l2[j].a,l2[j].b,1);
        q[i].ans=unf[t].Query(q[i].s,q[i].t,q[i].a,q[i].b);
        unf[t].Undo();
    }

    sort(q+1,q+1+K,cmp3);
    for(int i=1;i<=K;i++)
        if(q[i].ans==1)
            printf("Yes\n");
        else
            printf("No\n");
    cerr<<clock()-t<<endl;
    return 0;
}

```