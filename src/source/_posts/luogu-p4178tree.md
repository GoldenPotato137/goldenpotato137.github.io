---
title: '[Luogu P4178]Tree'
tags:
  - 数据结构
id: '201'
categories:
  - - splay
  - - 数据结构
  - - 点分治
date: 2019-02-25 18:08:21
updated: 2019-02-25 18:08:21
---

# 题面

传送门：[洛谷](https://www.luogu.org/problemnew/show/P4178)

* * *

# Solution

首先，长成这样的题目一定是淀粉质跑不掉了。 考虑到我们不知道K的大小，我们可以开一个splay来统计比某个数小的数的数量。 [不会点分治的戳我](https://www.goldenpotato.cn/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%B7%80%E7%B2%89%E8%B4%A8%E7%82%B9%E5%88%86%E6%B2%BB-%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/)

* * *

# Code

```cpp
#include<iostream>
#include<vector>
#include<cstdio>
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=40000+100;
struct road
{
    long long w;
    int to;
    road(int A,long long B)
    {
        to=A,w=B;
    }
};
struct SBT
{
    #define root son[0][1]
    int fa[N],son[N][2],w[N],size[N],cnt[N],to;
    inline void update(int now)
    {
        size[now]=size[son[now][0]]+size[son[now][1]]+cnt[now];
    }
    inline void rotate(int x,int type)
    {
        int y=fa[x],z=fa[y];
        fa[x]=z,son[z][y==son[z][1]]=x;
        son[y][!type]=son[x][type],fa[son[x][type]]=y;
        son[x][type]=y,fa[y]=x;
        update(y),update(x);
    }
    void splay(int now,int to)
    {
        while(fa[now]!=to)
        {
            if(now==son[fa[now]][fa[now]==son[fa[fa[now]]][1]] and fa[fa[now]]!=to)
                rotate(fa[now],now==son[fa[now]][0]),
                rotate(now,now==son[fa[now]][0]);
            else
                rotate(now,now==son[fa[now]][0]);
        }
    }
    inline void InitTree()
    {
        root=to=0;
    }
    inline void init(int now)
    {
        fa[now]=son[now][0]=son[now][1]=size[now]=w[now]=0;
    }
    void Insert(int num)
    {
        if(root==0)
        {
            root=++to;
            init(root);
            fa[root]=0;
            w[root]=num,cnt[root]=1,update(root);
            return;
        }
        int now=root,last=root;
        while(now!=0)
        {
            if(w[now]==num)
            {
                cnt[now]++;
                splay(now,0);
                return;
            }
            last=now,now=son[now][num>w[now]];
        }
        now=++to,init(now);
        w[now]=num,cnt[now]=1;
        fa[now]=last,son[last][num>w[last]]=now;
        update(now),splay(now,0);
    }
    int Query(int num)
    {
        int now=root,t_ans=0;
        while(now!=0)
        {
            if(num>=w[now])
            {
                if(w[now]>=w[t_ans]) t_ans=now;
                now=son[now][1];
            }
            else
                now=son[now][0];
        }    
        if(t_ans==0) return 0;
        splay(t_ans,0);
        return size[son[root][0]]+cnt[root];
    }
    #undef root
}sbt;
vector <road> e[N];
long long n,K;
bool vis[N],t_vis[N],done[N];
int size[N],cnt,root;
int GetSize(int now)
{
    t_vis[now]=true;
    size[now]=1;
    for(int i=0;i<int(e[now].size());i++)
        if(t_vis[e[now][i].to]==false and vis[e[now][i].to]==false)
            size[now]+=GetSize(e[now][i].to); 
    t_vis[now]=0;
    return size[now];
}
void GetRoot(int now)
{
    t_vis[now]=true,size[now]=1;
    bool OK=true;
    for(int i=0;i<int(e[now].size());i++)
        if(t_vis[e[now][i].to]==false and vis[e[now][i].to]==false)
        {
            GetRoot(e[now][i].to); 
            size[now]+=size[e[now][i].to];
            if(size[e[now][i].to]>cnt/2)
                OK=false;
        }
    if(cnt-size[now]>cnt/2)    OK=false;
    if(OK==true)    root=now;
    t_vis[now]=0;
}
int ans;
void dfs2(int now,long long dis,int type)
{
    t_vis[now]=true;
    if(type==1 and K-dis>=0)
        ans+=sbt.Query(K-dis);
    else if(type==2)
        sbt.Insert(dis);
    for(int i=0;i<int(e[now].size());i++)
        if(t_vis[e[now][i].to]==false and done[e[now][i].to]==true)
            dfs2(e[now][i].to,dis+e[now][i].w,type);
    t_vis[now]=false;
}
void dfs(int now)
{
    //cerr<<now<<endl;
    vis[now]=true;
    vector <road> son;
    for(int i=0;i<int(e[now].size());i++)
        if(vis[e[now][i].to]==false)
        {
            cnt=GetSize(e[now][i].to);
            GetRoot(e[now][i].to);
            son.push_back(e[now][i]);
            dfs(root);
        }
    sbt.InitTree();
    sbt.Insert(0);
    for(int i=0;i<int(son.size());i++)
        dfs2(son[i].to,son[i].w,1),dfs2(son[i].to,son[i].w,2);
    done[now]=true;
}
int main()
{
    freopen("4178.in","r",stdin);

    n=read();
    for(int i=1;i<=n;i++)
        e[i].reserve(4);
    for(int i=1;i<n;i++)
    {
        long long s=read(),t=read(),w=read();
        e[s].push_back(road(t,w));
        e[t].push_back(road(s,w));
    }
    K=read();

    cnt=GetSize(1);
    GetRoot(1);
    dfs(root);

    printf("%d",ans);
    return 0;
}
```