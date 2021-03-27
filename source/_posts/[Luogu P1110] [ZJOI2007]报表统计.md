---
title: "[Luogu P1110] [ZJOI2007]报表统计"
date: 2019-03-08 04:08:55
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P1110" target="_blank"  rel="nofollow" >洛谷P1110</a></p>
<hr />
<h1>Solution</h1>
<p>我们看到这道题，我们不妨想<strong>把处于同一个点的骑士全部丢到一个小根堆左偏树里面</strong>。这样子，我们<strong>从下往上合并，合并完就去检查一下根是否满足当前城市的要求，一直弹根弹到满足要求为止</strong>。</p>
<p>至于能力的变化，这里的操作要求无外乎乘法和加法。因此，我们可以像线段树II那道题那样做两个标记，处理一下即可。每次合并、弹根之前都pushdown标记。</p>
<p>就酱，这题就被我们切掉啦~(*´ﾟ∀ﾟ｀)ﾉ<br />
时间复杂度$O(nlogm)$</p>
<hr />
<h1>Code</h1>
<h3>数据生成器</h3>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;ctime&gt;
#include&lt;cstdlib&gt;
const int N=10;
const int MAX=10;
using namespace std;
int mrand()
{
    int x=rand()%MAX,f=rand()%2;
    if(f==0) f=-1;
    return x*f;
}
int main()
{
    srand(time(NULL));
    freopen("3261.in","w",stdout);

    int n=N,m=N;
    cout&lt;&lt;n&lt;&lt;" "&lt;&lt;m&lt;&lt;endl;
    for(int i=1;i&lt;=n;i++)
        cout&lt;&lt;mrand()&lt;&lt;" ";
    cout&lt;&lt;endl;

    for(int i=2;i&lt;=n;i++)
    {
        int to=rand()%i;
        if(to==0) to=1;
        cout&lt;&lt;to&lt;&lt;" "&lt;&lt;rand()%2&lt;&lt;" "&lt;&lt;mrand()&lt;&lt;endl;
    }

    for(int i=1;i&lt;=m;i++)
        cout&lt;&lt;mrand()&lt;&lt;" "&lt;&lt;rand()%n+1&lt;&lt;endl;
    return 0;
}

</code></pre>
<h3>正解</h3>
<p><del>我因为用了vector存边常数爆大，不吸氧会T一个点</del></p>
<pre><code class="language-cpp ">//Luogu P3261 [JLOI2015]城池攻占
//Mar,8th,2019
//左偏树+线段树II
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;vector&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=300000+1000;
struct LST
{
    long long plus[N],take[N],w[N],son[N][2],dis[N],fa[N];
    inline void pushdown(int x)//传入位置
    {
        if(son[x][0]!=0)
        {
            w[son[x][0]]=w[son[x][0]]*take[x]+plus[x];
            take[son[x][0]]*=take[x];
            plus[son[x][0]]=plus[son[x][0]]*take[x]+plus[x];
        }
        if(son[x][1]!=0)
        {
            w[son[x][1]]=w[son[x][1]]*take[x]+plus[x];
            take[son[x][1]]*=take[x];
            plus[son[x][1]]=plus[son[x][1]]*take[x]+plus[x];
        }
        plus[x]=0,take[x]=1;
    }
    int findFather(int x)//传入位置
    {
        if(fa[x]==0) return x;
        return fa[x]=findFather(fa[x]);
    }
    int Merge(int x,int y)//传入根的位置
    {
        if(x==0 or y==0) return x+y;
        if(w[x]&lt;w[y]) swap(x,y);
        pushdown(x),pushdown(y);
        son[y][1]=Merge(x,son[y][1]),fa[son[y][1]]=y;
        if(dis[son[y][0]]&lt;dis[son[y][1]]) 
            swap(son[y][0],son[y][1]);
        dis[y]=dis[son[y][1]]+1;    
        return y;
    }
    int Pop(int x)//返回新的根的位置
    {
        pushdown(x);
        fa[x]=Merge(son[x][0],son[x][1]);
        return fa[x];
    }
    void Mark(int x,long long ntake,long long nplus)
    {
        pushdown(x);
        w[x]=w[x]*ntake+nplus;
        take[x]=ntake,plus[x]=nplus;
    }
}lst;
int n,m,root[N],ans1[N],ans2[N];
long long a[N],v[N],h[N];
vector &lt;int&gt; e[N];
int depth[N],from[N];
void dfs(int now)
{
    for(int i=0;i&lt;int(e[now].size());i++)
    {
        depth[e[now][i]]=depth[now]+1;
        dfs(e[now][i]);
        if(root[e[now][i]]!=0)
            root[now]=lst.Merge(root[now],root[e[now][i]]);
    }
    while(root[now]!=0 and lst.w[root[now]]&lt;h[now])
    {
        ans1[now]++,ans2[root[now]]=depth[from[root[now]]]-depth[now];
        root[now]=lst.Pop(root[now]);
    }
    if(v[now]==0)
        lst.Mark(root[now],1,a[now]);
    else
        lst.Mark(root[now],a[now],0);
}
int main()
{
    int t=clock();
    freopen("3261.in","r",stdin);
    freopen("3261.out","w",stdout);

    int size = 256 &lt;&lt; 20;
    char *p = (char*)malloc(size) + size;
    __asm__("movl %0, %%esp\n" :: "r"(p));

    n=read(),m=read();
    for(int i=1;i&lt;=n;i++)
        h[i]=read();
    for(int i=1;i&lt;=n;i++)
        e[i].reserve(4);
    for(int i=2;i&lt;=n;i++)
    {
        e[read()].push_back(i);
        v[i]=read(),a[i]=read();
    }
    for(int i=1;i&lt;=m;i++)
    {
        long long w=read();
        from[i]=read();
        lst.w[i]=w;
        if(root[from[i]]==0)
            root[from[i]]=i;
        else
            root[from[i]]=lst.Merge(root[from[i]],i);
    }

    depth[1]=1;
    dfs(1);
    while(root[1]!=0)
    {
        ans2[root[1]]=depth[from[root[1]]];
        root[1]=lst.Pop(root[1]);
    }

    for(int i=1;i&lt;=n;i++)
        printf("%d\n",ans1[i]);
    for(int i=1;i&lt;=m;i++)
        printf("%d\n",ans2[i]);
    cerr&lt;&lt;clock()-t;
    return 0;
}


</code></pre>
