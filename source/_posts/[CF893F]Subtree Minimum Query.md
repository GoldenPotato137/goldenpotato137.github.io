---
title: "[CF893F]Subtree Minimum Query"
date: 2019-02-22 09:30:47
tags: "迁移"
---
<h1>题面：</h1>
<p>传送门：<a href="http://codeforces.com/problemset/problem/893/F" target="_blank"  rel="nofollow" >Codeforces</a></p>
<p>题目大意：给你一颗有根树，点有权值，问你每个节点的子树中距离其不超过k的点的权值的最小值。（边权均为1，强制在线）</p>
<hr />
<h1>Solution</h1>
<p>这题很有意思。</p>
<p>我们一般看到这种距离不超过k的题目，第一反应一般是建以深度为下标，以dfs序为时间轴的的主席树。<br />
很不幸，区间最小值并不能通过减去历史状态得出某个子树的状态。</p>
<p>所以说，这题妙在思想的转换。<br />
考虑以dfs序为下标，<strong>以深度为时间轴建一颗主席树</strong>。<br />
我们可以bfs，按深度一层层地把点加进去。<br />
这样子，我们就可以查询对应深度之内的这颗子树的最小权值啦。</p>
<p>就酱，我们就可以把这题切掉啦ヽ(￣▽￣)ﾉ</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
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
const int N=100000+1000;
const int M=30*N;
int n,r,a[N];
vector &lt;int&gt; e[N];
struct SegmentTree
{
    static const int inf=0x3f3f3f3f;
    #define mid ((now_l+now_r)&gt;&gt;1)
    int MIN[M],son[M][2],cnt;
    inline void update(int now)
    {
        MIN[now]=min(MIN[son[now][0]],MIN[son[now][1]]);    
    }
    void Build(int now,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            MIN[now]=inf;
            return;    
        }
        Build(son[now][0]=++cnt,now_l,mid);
        Build(son[now][1]=++cnt,mid+1,now_r);
        update(now);
    }
    void Change(int x,int num,int now,int pre,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            MIN[now]=num;
            return;
        }
        if(x&lt;=mid) son[now][1]=son[pre][1],Change(x,num,son[now][0]=++cnt,son[pre][0],now_l,mid);
        else son[now][0]=son[pre][0],Change(x,num,son[now][1]=++cnt,son[pre][1],mid+1,now_r);
        update(now);
    }
    int Query(int L,int R,int now,int now_l,int now_r)
    {
        if(now_l&gt;=L and now_r&lt;=R)
            return MIN[now];
        int ans=inf;
        if(L&lt;=mid) ans=min(ans,Query(L,R,son[now][0],now_l,mid));
        if(R&gt;mid) ans=min(ans,Query(L,R,son[now][1],mid+1,now_r));
        return ans;
    }
    void Print(int now,int now_l,int now_r)
    {
        cerr&lt;&lt;"no."&lt;&lt;now&lt;&lt;" now_l&amp;r:"&lt;&lt;now_l&lt;&lt;" "&lt;&lt;now_r&lt;&lt;" sonl&amp;r"&lt;&lt;son[now][0]&lt;&lt;" "&lt;&lt;son[now][1]&lt;&lt;" MIN:"&lt;&lt;MIN[now]&lt;&lt;endl;
        if(now_l!=now_r)
        {
            Print(son[now][0],now_l,mid);
            Print(son[now][1],mid+1,now_r);    
        }
    }    
    #undef mid
}sgt;
int dfn[N],depth[N],dfn_to,size[N],depth_MAX;
void dfs(int now)
{
    depth_MAX=max(depth_MAX,depth[now]);
    dfn[now]=++dfn_to;
    size[now]=1;
    for(int i=0;i&lt;int(e[now].size());i++)
        if(dfn[e[now][i]]==0)
        {
            depth[e[now][i]]=depth[now]+1;
            dfs(e[now][i]);
            size[now]+=size[e[now][i]];    
        }
}
int dl[N],front,tail,root[N];
void bfs()
{
    dl[tail++]=r;
    int depth_now=0;
    while(tail&gt;front)
    {
        int now=dl[front];
        int temp=root[depth_now];
        if(depth[now]!=depth_now)
        {
            depth_now=depth[now];
            temp=root[depth_now-1];
        }
        root[depth_now]=++sgt.cnt;
        sgt.Change(dfn[now],a[now],root[depth_now],temp,1,n);
        //sgt.Print(root[depth_now],1,n);
        //cerr&lt;&lt;endl;
        for(int i=0;i&lt;int(e[now].size());i++)
            if(depth[e[now][i]]&gt;depth[now])
                dl[tail++]=e[now][i];
        front++;    
    }
}
int main()
{
    n=read(),r=read();
    for(int i=1;i&lt;=n;i++)
        a[i]=read();
    for(int i=1;i&lt;n;i++)
    {
        int s=read(),t=read();
        e[s].push_back(t);
        e[t].push_back(s);
    }

    depth[r]=1;
    dfs(r);
    sgt.Build(0,1,n);
    //sgt.Print(0,1,n);
    //cerr&lt;&lt;endl;
    bfs();

    int m=read(),lans=0;
    for(int i=1;i&lt;=m;i++)
    {
        int x=read(),K=read();
        x=((x+lans)%n)+1,K=(K+lans)%n;
        int temp=min(depth[x]+K,depth_MAX);
        lans=sgt.Query(dfn[x],dfn[x]+size[x]-1,root[temp],1,n);
        printf("%d\n",lans);    
    }
    return 0;
}
</code></pre>
