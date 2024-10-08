---
title: '[Luogu P3302] [SDOI2013]森林'
tags:
  - 数据结构
id: '340'
categories:
  - - 主席树
  - - 数据结构
date: 2019-03-07 11:34:51
updated: 2019-03-07 11:34:51
---

# 题面

[洛谷P3302](https://www.luogu.org/problemnew/show/P3302)

* * *

# Solution

拿到这道题，我们不妨先想一下静态的树上K大怎么搞。 静态树上K大有两种办法，一是树链剖分+平衡树，二是主席树做链前缀和。前者的复杂度是$O(log^2n)$的，而后者只有$O(logn)$。 我们考虑把数字全部离散化，然后开权值主席树，每颗主席树记录从它出发到根的路上每个数字出现了多少个。接下来，我们只需要找到LCA。因为数字出现个数满足可减性，因此，我们是可以“扣”出从这个点到LCA的路径的，我们把两条路径合并到一颗主席树上，做树上二分即可。 接下来考虑如何处理边合并的问题。考虑启发式暴力合并。我们把小的那棵树合并到大的那棵树上，暴力重构小的那棵树的每个点的主席树，也暴力重构每个点的depth，fa来计算LCA即可。 启发式合并中，每个点的重构次数期望为$logn$次，因此，我们的总复杂度为$O(nlog^2n)$ 就酱，这题我们就切掉啦(～￣▽￣)～

* * *

# Code

**细节繁多，请各位dalao小心**

```cpp
//Luogu P3302 [SDOI2013]森林
//Mar,6th,2019
//主席树启发式合并维护动态树树链K大
#include<iostream>
#include<cstdio>
#include<vector>
#include<cstring>
#include<algorithm>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=80000+2000;
struct SegmentTree
{
    #define mid ((now_l+now_r)>>1)
    static const int M=400*N;
    int son[M][2],size[M],to;
    inline void update(int now)
    {
        size[now]+=size[son[now][0]]+size[son[now][1]];
    }
    inline void Add(int now,int pre,int x,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            size[now]=size[pre]+1;
            return;
        }
        if(x<=mid) 
        {
            Add(son[now][0]=++to,son[pre][0],x,now_l,mid);
            son[now][1]=son[pre][1];
        }
        else
        {
            Add(son[now][1]=++to,son[pre][1],x,mid+1,now_r);
            son[now][0]=son[pre][0];
        }
        update(now);
    }
    int Query(int now1,int now2,int pre1,int pre2,int x,int now_l,int now_r)
    {
        if(now_l==now_r) return now_l;
        if(x<=size[son[now1][0]]-size[son[pre1][0]]+size[son[now2][0]]-size[son[pre2][0]])
            return Query(son[now1][0],son[now2][0],son[pre1][0],son[pre2][0],x,now_l,mid);
        else
            return Query(son[now1][1],son[now2][1],son[pre1][1],son[pre2][1],x-(size[son[now1][0]]-size[son[pre1][0]]+size[son[now2][0]]-size[son[pre2][0]]),mid+1,now_r);
    }
    void Print(int now,int now_l,int now_r)
    {
        cerr<<"no."<<now<<" l&r:"<<now_l<<" "<<now_r<<" sonl&r:"<<son[now][0]<<" "<<son[now][1]<<" size:"<<size[now]<<endl;
        if(now_l!=now_r)
            Print(son[now][0],now_l,mid),
            Print(son[now][1],mid+1,now_r);
    }
    #undef mid
}sgt;
vector <int> e[N];
int n,m,q,w[N],mmap[N];//mmap:离散值->真实值
int fa[N][21],size[N],depth[N],root[N];
bool vis[N];
void dfs(int now)
{
    for(int i=1;i<=20;i++)
        fa[now][i]=fa[fa[now][i-1]][i-1];
    vis[now]=true;
    for(int i=0;i<int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            depth[e[now][i]]=depth[now]+1;
            fa[e[now][i]][0]=now;
            root[e[now][i]]=++sgt.to;
            sgt.Add(root[e[now][i]],root[now],w[e[now][i]],1,m);
            //sgt.Print(root[e[now][i]],1,m);
            //cerr<<endl;
            dfs(e[now][i]);
        }
    vis[now]=false;
}
void Merge(int x,int y)
{
    if(size[fa[x][20]]>size[fa[y][20]]) swap(x,y);
    size[fa[y][20]]+=size[fa[x][20]];
    depth[x]=depth[y]+1;
    fa[x][0]=y;
    root[x]=++sgt.to;
    sgt.Add(root[x],root[y],w[x],1,m);
    //sgt.Print(root[x],1,m);
    //cerr<<endl;
    dfs(x);
    e[x].push_back(y);
    e[y].push_back(x);
}
int LCA(int x,int y)
{
    if(depth[x]<depth[y]) swap(x,y);
    for(int i=20;i>=0;i--)
        if(depth[fa[x][i]]>=depth[y])
            x=fa[x][i];
    if(x==y) return x;
    for(int i=20;i>=0;i--)
        if(fa[x][i]!=fa[y][i])
            x=fa[x][i],y=fa[y][i];
    return fa[x][0];
}
int Query(int x,int y,int K)
{
    if(depth[x]<depth[y]) swap(x,y);
    int lca=LCA(x,y);
    if(lca==y)
        return mmap[sgt.Query(root[x],0,(fa[lca][0]==lca?0:root[fa[lca][0]]),0,K,1,m)];
    else
        return mmap[sgt.Query(root[x],root[y],root[lca],(fa[lca][0]==lca?0:root[fa[lca][0]]),K,1,m)];
}
void Init()
{
    for(int i=0;i<=n;i++)
        e[i].reserve(4);
    for(int i=1;i<=n;i++)
    {
        root[i]=++sgt.to;
        sgt.Add(root[i],0,w[i],1,m);
    }
    for(int i=1;i<=n;i++)
        size[i]=1,depth[i]=1;
    for(int i=1;i<=n;i++)
        for(int j=0;j<=21;j++)
            fa[i][j]=i;
}
int m2;
int main()
{
    static int tmp[N];
    n=read(),n=read(),m2=read(),q=read();
    for(int i=1;i<=n;i++)
        tmp[i]=w[i]=read();

    sort(tmp+1,tmp+1+n);
    m=unique(tmp+1,tmp+1+n)-tmp-1;
    for(int i=1;i<=n;i++)
    {
        int temp=lower_bound(tmp+1,tmp+1+m,w[i])-tmp;
        mmap[temp]=w[i];
        w[i]=temp;
    }
    Init();

    for(int i=1;i<=m2;i++)
    {
        int x=read(),y=read();
        Merge(x,y);
    }

    int ans=0;
    char OP[5];
    for(int i=1;i<=q;i++)
    {
        scanf("%s",OP+1);
        if(OP[1]=='Q')
        {
            int x=read()^ans,y=read()^ans,K=read()^ans;
            printf("%d\n",ans=Query(x,y,K));
        }
        else
        {
            int x=read()^ans,y=read()^ans;
            Merge(x,y);
        }
        //ans=0;
    }
    return 0;
}

```