---
title: '[Luogu P4297] [NOI2006]网络收费'
tags:
  - DP
id: '396'
categories:
  - - 动态规划
  - - 树形DP
  - - 状压DP
date: 2019-03-29 08:03:37
updated: 2019-03-29 08:03:37
---

# 题面

[P4297 \[NOI2006\]网络收费](https://www.luogu.org/problemnew/show/P4297)

* * *

# Solution

这题喵啊。 首先，我们会发现统计两个点互相的贡献是一个极其困难的问题。 但是，仔细观察那张收费表格后会发现，我们可以重新定义一下这个收费：**我们假设路由器节点的颜色为叶子中数目较多的颜色，当一个叶子结点颜色与路由器节点颜色相同的时候不收钱，否则收一份钱。**我们可以惊讶的发现，这样做之后我们的新收费做法就与原来题目要求的重合了，而且**贡献由点对转到了点上**。 接下来，我们就可以统计每个叶子节点对每个路由器产生的贡献了。**我们设$F\[i\]\[j\]$表示第$i$个叶子节点在LCA为$j$情况下产生的贡献。**这个非常好搞，我们只需要枚举点对+计算LCA即可，复杂度$O(n^2logn)$ 我们会发现一个问题，一个叶子节点的贡献值与它到根路径上所有的路由器的颜色息息相关。因此，我们传统的基于子树的树形DP做法已经行不通了，我们现在需要一个基于某个点到根路径的DP做法。 考虑：**设$f\[i\]\[j\]\[k\]$表示目前填到了第$i$个点，它到根的路径一路上的颜色为$j$(状压形式表现),它的孩子(叶子节点)要填$k$个颜色B。** 转移非常简单，对于路由器节点，我们只需要像背包一样枚举左右孩子分别分配多少个颜色B即可；对于叶子节点，我们只需要直接一路算上去它的贡献即可。 但是我们会发现一个问题，这样子我们直接设的空间复杂度是$O(2^{3n})$的，并开不下。 我们观察后发现，对于任何一个节点，假设它的深度为$k$,那么，它的叶子节点最多有$2^{(n-k)}$个，它到父亲的距离的情况最多有$2^k$种。**因此，我们发现这两位的状态可以直接压在一起（即放到一起存储，需要时再分开），空间复杂度就可以优化至$O(2^{2n})$**,可以通过这一题。 时间复杂度$O(2^{2n}\\cdot n)$ 就酱，这题就被我们切掉啦=￣ω￣=

* * *

# Code

**数据生成器**

```cpp
#include<iostream>
#include<cstdio>
#include<ctime>
#include<cstdlib>
using namespace std;
const int N=4;
const int MAX=5000;
int main()
{
    srand(time(NULL));
    freopen("4297.in","w",stdout);

    int n=N,m=1<<n;
    cout<<n<<endl;

    for(int i=1;i<=m;i++)
        cout<<rand()%2<<" ";
    cout<<endl;
    for(int i=1;i<=m;i++)
        cout<<rand()%MAX+1<<" ";
    cout<<endl;

    for(int i=1;i<=m;i++)
    {
        for(int j=i+1;j<=m;j++)
            cout<<rand()%MAX+1<<" ";  
        cout<<endl;
    }
    return 0;
}

```

**正解**

```cpp
//Luogu P4297 [NOI2006]网络收费
//Mar,28th,2019
//树形DP+状压DP妙题
#include<iostream>
#include<cstdio>
#include<cmath>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=2100;
const long long inf=0x3f3f3f3f3f3f3f3fll;
int n,m,a[N],c[N],w[N][N];
long long F[N][N],depth[N],fa[N][21];//F[i][j]:i在LCA=j的时候的贡献
void dfs(int now)
{
    for(int i=1;i<=20;i++)
        fa[now][i]=fa[fa[now][i-1]][i-1];
    if(now<(1<<n))
    {
        fa[now*2][0]=now,depth[now*2]=depth[now]+1,dfs(now*2);
        fa[now*2+1][0]=now,depth[now*2+1]=depth[now]+1,dfs(now*2+1);
    }
}
int LCA(int x,int y)
{
    if(depth[y]>depth[x]) swap(x,y);
    for(int i=20;i>=0;i--)
        if(depth[x]-(1<<i)>=depth[y])
            x=fa[x][i];
    if(x==y) return x;
    for(int i=20;i>=0;i--)
        if(fa[x][i]!=fa[y][i])
            x=fa[x][i],y=fa[y][i];
    return fa[x][0];
}
long long f[N][N*2];
long long dfs2(int now,int K)
{
    if(f[now][K]!=0) return f[now][K];
    int c_now=0,cnt_B=K/(1<<depth[now]),cnt_A=(1<<(n-depth[now]))-cnt_B;
    if(cnt_B<0 or cnt_A<0) return f[now][K]=inf;
    if(cnt_B>cnt_A) c_now=1;
    if(now>=(1<<n))
    {
        int tmp=K%(1<<depth[now]),t_now=now/2;
        for(;t_now>0;t_now/=2,tmp/=2)
            f[now][K]+=(c_now!=(tmp%2))*F[now][t_now];
        f[now][K]+=c[now]*(a[now]!=c_now);
        return f[now][K];
    }
    long long t_ans=inf;
    for(int i=0;i<=cnt_B;i++)
    {
        long long L=dfs2(now*2,((K%(1<<depth[now]))<<1)+c_now+i*(1<<depth[now*2]));
        long long R=dfs2(now*2+1,((K%(1<<depth[now]))<<1)+c_now+(cnt_B-i)*(1<<depth[now*2+1]));
        t_ans=min(t_ans,L+R);
    }
    return f[now][K]=t_ans;
}
int main()
{
    n=read();
    m=(1<<(n+1))-1;
    for(int i=(1<<n);i<=m;i++)
        a[i]=read();
    for(int i=(1<<n);i<=m;i++)
        c[i]=read();
    for(int i=(1<<n);i<=m;i++)
        for(int j=i+1;j<=m;j++)
            w[i][j]=w[j][i]=read();

    dfs(1);
    for(int i=(1<<n);i<=m;i++)
        for(int j=i+1;j<=m;j++)
            F[i][LCA(i,j)]+=w[i][j],
            F[j][LCA(i,j)]+=w[i][j];

    long long ans=inf;
    for(int i=0;i<=(1<<n);i++)
        ans=min(ans,dfs2(1,i));

    printf("%lld",ans);
    return 0;
}

```