---
title: '[Luogu P3233] [HNOI2014]世界树'
tags:
  - DP
id: '401'
categories:
  - - 动态规划
  - - 树形DP
  - - 虚树
date: 2019-04-01 09:09:42
updated: 2019-04-01 09:09:42
---

# 题面

[P3233 \[HNOI2014\]世界树](https://www.luogu.org/problemnew/show/P3233)

* * *

# Solution

这是一道虚树妙题。 我们不妨先考虑一下每一次$O(n)$计算的暴力怎么做。 $O(n\\cdot m)$的暴力肥肠简单，我们只需要做两遍dfs。**考虑设$f\[i\]$表示离$i$最近的聚居地是什么，$MIN\[i\]$表示$i$到最近的聚居地的距离。我们第一遍dfs先找出$i$到它子树内的聚居地的最小距离，之后再做一遍dfs来找$i$往祖先方向后头走能走到的最近聚居地的距离即可。** 观察数据范围后发现，$\\sum m<=300000$，因此考虑使用**虚树**。 建出来虚树之后，显然对于在虚树上的点，我们还是能直接暴力做，问题是怎么处理非虚树上的点。 我们会发现，**我们虚树上的一条边在原树种对应一条链(包括链上的子树)。**我们会发现，**这条链上的点上一定是上半部分的最近距离在上面那个点，下半部分的最近距离在下面那个点。**因此，我们考虑用倍增的思想来找出这个“分界点”，找到后计算一下上下分别贡献即可。 这里有个小细节，我们是在原树上做倍增的，因此**我们倍增过程中不应该使用跟DP有关的量**，这里理论上我们只需要使用上端点与下端点的$f,MIN$，以及每个点的深度，$fa$即可实现这个倍增。 时间复杂度$O(mlogn)$ 就酱，我们就把这题切掉啦(\*≧▽≦)

* * *

# Code

**本题细节较多，请各位dalao小心慢行** 直接两行泪就完事了 **数据生成器**

```cpp
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<ctime>
#include<cstring>
using namespace std;
const int N=50;
bool used[N+5];
int main()
{
    srand(time(NULL));
    freopen("3233.in","w",stdout);

    int n=N;
    cout<<n<<endl;
    for(int i=2;i<=n;i++)
        cout<<max(rand()%i,1)<<" "<<i<<endl;

    cout<<n<<endl;
    for(int i=1;i<=n;i++)
    {
        memset(used,0,sizeof used);
        int m=rand()%n+1;
        cout<<m<<endl;

        for(int j=1;j<=m;j++)
        {
            int t=rand()%n+1;
            while(used[t]==true)
                t=rand()%n+1;
            used[t]=true;
            cout<<t<<" ";
        }
        cout<<endl;
    }
    return 0;
}

```

**正解**

```cpp
//Luogu P3233 [HNOI2014]世界树
//Apr,1st,2019
//虚树+DP+倍增神题
#include<iostream>
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=300000+1000;
vector <int> e[N],e2[N];
int n,q,a[N],b[N];
int dfn[N],dfn_to,depth[N],fa[N][21],size[N];
void dfs(int now)
{
    dfn[now]=++dfn_to;
    size[now]=1;
    for(int i=1;i<=20;i++)
        fa[now][i]=fa[fa[now][i-1]][i-1];
    for(int i=0;i<int(e[now].size());i++)
        if(dfn[e[now][i]]==0)
        {
            depth[e[now][i]]=depth[now]+1;
            fa[e[now][i]][0]=now;
            dfs(e[now][i]);
            size[now]+=size[e[now][i]];
        }
}
int LCA(int x,int y)
{
    if(depth[x]<depth[y]) swap(x,y);
    for(int i=20;i>=0;i--)
        if(depth[x]-(1<<i)>=depth[y])
            x=fa[x][i];
    if(x==y) return x;
    for(int i=20;i>=0;i--)
        if(fa[x][i]!=fa[y][i])
            x=fa[x][i],y=fa[y][i];
    return fa[x][0];
}
int cmp(int x,int y)
{
    return dfn[x]<dfn[y];
}
bool sp[N];
int MIN[N],f[N],ans[N];
inline int GetDis(int x,int y)
{
    if(depth[x]<depth[y]) swap(x,y);
    return depth[x]-depth[y];
}
void dfs2(int now)
{
    if(sp[now]==true) 
        f[now]=now,MIN[now]=0;
    for(int i=0;i<int(e2[now].size());i++)
    {
        dfs2(e2[now][i]);
        if(MIN[e2[now][i]]+GetDis(e2[now][i],now) < MIN[now] 
        or (MIN[e2[now][i]]+GetDis(e2[now][i],now)==MIN[now] and f[now]>f[e2[now][i]]))
            f[now]=f[e2[now][i]],MIN[now]=MIN[e2[now][i]]+GetDis(e2[now][i],now);
    }
}
void dfs3(int now,int fa) 
{
    if(fa!=0)
    {
        if(MIN[fa]+GetDis(fa,now) < MIN[now] 
        or (MIN[fa]+GetDis(fa,now)==MIN[now] and f[now]>f[fa]))
            f[now]=f[fa],MIN[now]=MIN[fa]+GetDis(fa,now);
    }
    ans[f[now]]++;
    for(int i=0;i<int(e2[now].size());i++)
        dfs3(e2[now][i],now);
}
void GetSum(int x,int y,int &sum_x,int &sum_y)
{
    bool IsSwap=false;
    if(depth[x]<depth[y]) IsSwap=true,swap(x,y);
    int sx=x,dis_x=MIN[x];
    for(int i=20;i>=0;i--)
        if(dis_x+(1<<i) < MIN[y]+depth[x]-depth[y]-(1<<i))
            f[fa[x][i]]=f[x],
            x=fa[x][i],dis_x+=(1<<i);
    if(dis_x+1==MIN[y]+depth[x]-depth[y]-1 and f[x]<f[y])
        x=fa[x][0];
    sum_x=size[x]-size[sx];
    for(int i=20;i>=0;i--)
        if(depth[sx]-(1<<i)>depth[y])
            sx=fa[sx][i];
    sum_y=size[sx]-size[x];
    if(IsSwap==true)
        swap(sum_x,sum_y);
}
void dfs4(int now)
{
    int tmp=size[now]-1;
    for(int i=0;i<int(e2[now].size());i++)
    {
        int sum1,sum2;
        GetSum(now,e2[now][i],sum1,sum2);
        ans[f[now]]+=sum1,ans[f[e2[now][i]]]+=sum2;
        tmp-=(size[e2[now][i]]+sum1+sum2);
        dfs4(e2[now][i]);
    }
    ans[f[now]]+=tmp;
}
int main()
{
    freopen("3233.in","r",stdin);
    freopen("3233.out","w",stdout);

    n=read();
    for(int i=1;i<n;i++)
    {
        int s=read(),t=read();
        e[s].push_back(t);
        e[t].push_back(s);
    }

    fa[1][0]=1;
    dfs(1);

    q=read();
    for(int i=1;i<=q;i++)
    {
        int m=read();
        for(int j=1;j<=m;j++)
            b[j]=a[j]=read();

        sort(a+1,a+1+m,cmp);
        static int mstack[N],top,rec[N],cnt;
        cnt=0;
        mstack[top=1]=1;
        for(int j=(a[1]==1?2:1);j<=m;j++)
        {
            while(LCA(mstack[top],a[j])!=mstack[top])
            {
                int lca=LCA(mstack[top],a[j]);
                if(depth[lca]>depth[mstack[top-1]])
                {
                    e2[lca].push_back(mstack[top]);
                    rec[++cnt]=mstack[top],mstack[top]=lca;
                }
                else
                {
                    e2[mstack[top-1]].push_back(mstack[top]);
                    rec[++cnt]=mstack[top--];
                }
            }
            mstack[++top]=a[j];
        }
        while(top>1)
        {
            e2[mstack[top-1]].push_back(mstack[top]);
            rec[++cnt]=mstack[top--];
        }
        rec[++cnt]=1;

        for(int j=1;j<=m;j++)
            sp[a[j]]=true;
        for(int j=1;j<=cnt;j++)
            MIN[rec[j]]=0x3f3f3f3f,ans[rec[j]]=0;
        dfs2(1); 
        dfs3(1,0);
        dfs4(1);
        for(int j=1;j<=m;j++)
            printf("%d ",ans[b[j]]);
        printf("\n");

        for(int j=1;j<=m;j++)
            sp[a[j]]=false;
        for(int j=1;j<=cnt;j++)
            e2[rec[j]].clear(),ans[rec[j]]=0;
    }
    return 0;
}

```