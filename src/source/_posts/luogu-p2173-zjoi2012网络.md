---
title: '[Luogu P2173] [ZJOI2012]网络'
tags:
  - 数据结构
id: '350'
categories:
  - - LCT
  - - 数据结构
date: 2019-03-11 18:58:09
updated: 2019-03-11 18:58:09
---

# 题面

[洛谷P2173](https://www.luogu.org/problemnew/show/P2173)

* * *

# Solution

首先，我们可以发现颜色总数特别的少，再考虑到有改变边的颜色的操作，可以考虑用LCT来解决。 我们建$c$颗LCT，每颗LCT存每个颜色对应的边，splay记录每颗splay的MAX\_w。 对于修改权值，考虑直接暴力修改每个颜色的LCT里对应的点的权值 对于修改颜色，我们可以暴力在每一颗LCT里面枚举来找一下有没有这条边，有的话就断掉，然后在对应的LCT里面连上。 对于查询，我们只需要把对应的LCT中对应的链split出来，然后直接输出MAX即可。 对于每个点同色连边不超过2，我们可以直接记录每个点每种颜色连了多少条边，在link和cut中维护一下即可。 时间复杂度$O(c\\cdot n \\cdot logm)$ 就酱，这题我们就切掉啦٩(๑>◡<๑)۶

* * *

# Code

```cpp
//Luogu P2173 [ZJOI2012]网络
//Mar,11th,2019
//LCT暴力题
#include<iostream>
#include<cstdio>
using namespace std;
long long read()
{
    long long x=0,f=1;char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=10000+100;
const int M=10+2;
int cnt[N][M];//记录每个点连出去的边的颜色数
struct LCT
{
    int son[N][2],fa[N],lazy[N],MAX[N],w[N];
    inline void update(int x)
    {
        MAX[x]=max(MAX[son[x][0]],MAX[son[x][1]]);
        MAX[x]=max(MAX[x],w[x]);
    }
    inline void mirror(int x)
    {
        lazy[x]=!lazy[x];
        swap(son[x][0],son[x][1]);
    }
    inline void pushdown(int x)
    {
        if(lazy[x]==true)
        {
            mirror(son[x][0]);
            mirror(son[x][1]);
            lazy[x]=false;
        }
    }
    inline bool isRoot(int x)
    {
        return (x!=son[fa[x]][0] and x!=son[fa[x]][1]);
    }
    inline void rotate(int x,int type)
    {
        int y=fa[x],z=fa[y];
        fa[x]=z;
        if(isRoot(y)==false)
            son[z][y==son[z][1]]=x;
        fa[son[x][type]]=y,son[y][!type]=son[x][type];
        fa[y]=x,son[x][type]=y;
        update(y),update(x);
    }
    int mstack[N],top;
    void splay(int x)
    {
        mstack[top=1]=x;
        for(int i=x;isRoot(i)==false;i=fa[i])
            mstack[++top]=fa[i];
        for(int i=top;i>=1;i--)
            pushdown(mstack[i]);
        while(isRoot(x)==false)
        {
            if(isRoot(fa[x])==false and x==son[fa[x]][fa[x]==son[fa[fa[x]]][1]])
                rotate(fa[x],x==son[fa[x]][0]);
            rotate(x,x==son[fa[x]][0]);
        }
    }
    void Access(int x)
    {
        for(int t=0;x!=0;t=x,x=fa[x])
            splay(x),son[x][1]=t,fa[t]=x,update(x);
    }
    int GetRoot(int x)
    {
        Access(x),splay(x);
        while(son[x][0]!=0) x=son[x][0];
        return x;
    }
    void MakeRoot(int x)
    {
        Access(x),splay(x);
        mirror(x);
    }
    int Link(int x,int y,int c)
    {
        if(cnt[x][c]==2 or cnt[y][c]==2) return 2;
        if(GetRoot(x)==GetRoot(y)) return 1;
        cnt[x][c]++,cnt[y][c]++;
        MakeRoot(x);
        fa[x]=y;
        return 0;
    }
    void split(int x,int y)//y做原根，x作为LCT根
    {
        MakeRoot(y);
        Access(x);
        splay(x);
    }
    int Cut(int x,int y,int w)
    {
        split(x,y);
        if(y!=son[x][0] or fa[y]!=x or son[y][1]!=0) return 1;
        son[x][0]=fa[y]=0;
        update(x);
        cnt[x][w]--,cnt[y][w]--;
        return 0;
    }
    void Change(int x,int num)
    {
        split(x,x);
        w[x]=MAX[x]=num;
    }
    int Query(int x,int y)
    {
        split(x,y);
        if(GetRoot(x)!=y) return -1;
        return MAX[x];
    }
}lct[M]; 
int n,m,c,K;
void Change1(int x,int num)
{
    for(int i=0;i<c;i++)
        lct[i].Change(x,num);
}
int Change2(int x,int y,int w)
{
    int statu=3;
    for(int i=0;i<c;i++)
        if(lct[i].Cut(x,y,i)==0)
        {
            statu=lct[w].Link(x,y,w);
            if(statu!=0)
                lct[i].Link(x,y,i);
            break;
        }
    return statu;
}
int Query(int x,int y,int w)
{
    return lct[w].Query(x,y);
}
int main()
{
    //freopen("2173.in","r",stdin);
    //freopen("2173.out","w",stdout);

    n=read(),m=read(),c=read(),K=read();
    for(int i=1;i<=n;i++)
        Change1(i,read());
    for(int i=1;i<=m;i++)
    {
        int x=read(),y=read(),w=read();
        lct[w].Link(x,y,w);
    }

    for(int i=1;i<=K;i++)
    {
        int op=read();
        if(op==0)
        {
            int x=read(),num=read();
            Change1(x,num);
        }
        else if(op==1)
        {
            int x=read(),y=read(),w=read(),t=Change2(x,y,w);
            if(t==0)
                printf("Success.\n");
            else if(t==1)
                printf("Error 2.\n");
            else if(t==2)
                printf("Error 1.\n");
            else
                printf("No such edge.\n");
        }
        else
        {
            int w=read(),x=read(),y=read();
            printf("%d\n",Query(x,y,w));
        }
    }
    return 0;
}

```