---
title: '[Luogu P4606] [SDOI2018]战略游戏'
tags:
  - DP
  - 图论
id: '409'
categories:
  - - 动态规划
  - - 图论
  - - 圆方树
  - - 缩点/强连通分量
  - - 虚树
date: 2019-04-08 18:09:27
updated: 2019-04-08 18:09:27
---

## 题面

[P4606 [SDOI2018]战略游戏](https://www.luogu.org/problemnew/show/P4606)


## Solution

> 圆方树上圆方果， 圆方树下你和我。 圆方树前建虚树， 欢乐多又多。

[![A5PwBd.th.png](https://s2.ax1x.com/2019/04/08/A5PwBd.th.png)](https://imgchr.com/i/A5PwBd) 

好吧，我们来说正题。 这题就比较强。根据常识，如果我们爆掉的点能影响这个图的连通性，**那么，这个点一定是割点**。 

因此，我们要先对原图做**Tarjan求点双**。接下来，我们考虑用圆方树来解决一个问题。 

我们先考虑暴力怎么做，我们先对原图求出圆方树。

接下来，我们发现，**对答案有贡献的点一定是孩子有被选定的点的圆点，并且这个点的总共被选定的孩子数不等于总共被选定数**（因为如果这个点被割掉了，其被选定的孩子一定会与其他点断开来，且方点代表一个点双，并不能割）。 

因此，我们可以直接统计一下有多少个有贡献的点即可。 

接下来，我们进一步观察数据，发现$\sum S$非常小，因此考虑用虚树来解决这个问题。 

我们对每个询问的点构建虚树。这时候，我们就要多计算边的贡献，考虑一条边的贡献，即为这条边上有多少个原点，随便转移一下就好了。

好个鬼啊，细节比较多，大家注意一下 

时间复杂度$O(\sum S \cdot log n)$ 

就酱，这题就被我们切掉啦ヾ(●´∀｀●)


## Code

### 数据生成器

```cpp
#include<iostream>
#include<cstdio>
#include<ctime>
#include<cstdlib>
#include<cstring>
using namespace std;
const int N=10;
const int M=15;
bool vis[N+5];
int main()
{
    srand(time(NULL));
    freopen("4606.in","w",stdout);

    cout<<"1\n";
    int n=N,m=M;
    cout<<n<<" "<<m<<endl;
    for(int i=2;i<=n;i++)
        cout<<i<<" "<<max(rand()%i,1)<<endl; 
    for(int i=n;i<=m;i++)
    {
        int s=rand()%n+1,t=rand()%n+1;
        cout<<s<<" "<<t<<endl;
    }

    int q=N;
    cout<<endl<<q<<endl;
    for(int i=1;i<=q;i++)
    {
        int t=max(2,rand()%n+1);
        memset(vis,0,sizeof vis);
        cout<<t<<" ";
        for(int j=1;j<=t;j++)
        {
            int tmp=rand()%n+1;
            while(vis[tmp]==true) 
                tmp=rand()%n+1;
            vis[tmp]=true;
            cout<<tmp<<" ";
        }
        cout<<endl;
    }
    return 0;
}

```

### 正解

```cpp
//Luogu P4606 [SDOI2018]战略游戏
//Apr,8th,2019
//圆方树+虚树
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
const int N=2*100000+100;
vector <int> e[N],e2[N],e3[N];
int n,m,q;
int low[N],cnt,dfn_to,dfn[N],mstack[N],top;
bool vis[N];
void Tarjan(int now,int father)
{
    vis[now]=true;
    dfn[now]=low[now]=++dfn_to;
    mstack[++top]=now;
    for(int i=0;i<int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            Tarjan(e[now][i],now);
            low[now]=min(low[now],low[e[now][i]]);
            if(low[e[now][i]]>=dfn[now])
            {
                e2[now].push_back(n+(++cnt));
                while(mstack[top+1]!=e[now][i])
                    e2[n+cnt].push_back(mstack[top--]);
            }
        }
        else if(e[now][i]!=father)
            low[now]=min(low[now],dfn[e[now][i]]);
}
int fa[N][21],depth[N],pre[N];
void dfs(int now,int father)
{
    fa[now][0]=father,depth[now]=depth[father]+1;
    dfn[now]=++dfn_to;
    pre[now]=pre[father]+(now<=n);
    for(int i=1;i<=20;i++)
        fa[now][i]=fa[fa[now][i-1]][i-1];
    for(int i=0;i<int(e2[now].size());i++)
        if(e2[now][i]!=father)
                dfs(e2[now][i],now);
}
int LCA(int x,int y)
{
    if(depth[x]<depth[y]) swap(x,y);
    for(int i=20;i>=0;i--)
        if(depth[x]-(1<<i) >= depth[y])
            x=fa[x][i];
    if(x==y) return x;
    for(int i=20;i>=0;i--)
        if(fa[x][i]!=fa[y][i])
            x=fa[x][i],y=fa[y][i];
    return fa[x][0];
}
bool cmp(int x,int y)
{
    return dfn[x]<dfn[y];
}
bool sp[N];
int f[N];
int GetSum(int x,int y) //(x,y)
{
    return pre[y]-pre[x]-(y<=n);
}
int dfs2(int now)
{
    f[now]=0;
    for(int i=0;i<int(e3[now].size());i++)
        f[now]+=dfs2(e3[now][i])+GetSum(now,e3[now][i]);
    if(sp[now]==false and now<=n and (now!=1 or e3[now].size()!=1))
        f[now]++;
    if(now==1 and e3[now].size()==1 and sp[now]==false)
        f[now]-=GetSum(now,e3[now][0]);
    return f[now];
}
int main()
{
    freopen("4606.in","r",stdin);
    freopen("4606.out","w",stdout);

    int T=read();

    for(;T>0;T--)
    {
        n=read(),m=read();

        for(int i=1;i<=2*n;i++)
            e[i].clear(),e2[i].clear(),e3[i].clear();
        memset(vis,0,sizeof vis);
        dfn_to=0;

        for(int i=1;i<=m;i++)
        {
            int s=read(),t=read();
            e[s].push_back(t);
            e[t].push_back(s);
        }

        Tarjan(1,1);
        dfn_to=0;
        dfs(1,1);

        q=read();
        static int a[N],rec[N];
        for(int i=1;i<=q;i++)
        {
            m=read();
            for(int j=1;j<=m;j++)
                a[j]=read();

            sort(a+1,a+1+m,cmp);
            mstack[top=1]=1,cnt=0;
            for(int j=(a[1]==1?2:1);j<=m;j++)
            {
                while(LCA(mstack[top],a[j])!=mstack[top])
                {
                    int lca=LCA(mstack[top],a[j]);
                    if(depth[lca] > depth[mstack[top-1]])
                    {
                        e3[lca].push_back(mstack[top]);
                        rec[++cnt]=mstack[top],mstack[top]=lca;
                    }
                    else
                    {
                        e3[mstack[top-1]].push_back(mstack[top]);
                        rec[++cnt]=mstack[top--];
                    }
                }
                mstack[++top]=a[j];
            }
            while(top>1)
            {
                e3[mstack[top-1]].push_back(mstack[top]);
                rec[++cnt]=mstack[top--];
            }
            rec[++cnt]=1;

            for(int i=1;i<=m;i++)
                sp[a[i]]=true;

            printf("%d\n",dfs2(1));

            for(int i=1;i<=cnt;i++)
                sp[rec[i]]=false,e3[rec[i]].clear();
        }
    }
    return 0;
}

```