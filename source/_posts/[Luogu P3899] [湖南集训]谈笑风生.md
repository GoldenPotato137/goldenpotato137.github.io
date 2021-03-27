---
title: "[Luogu P3899] [湖南集训]谈笑风生"
date: 2019-02-22 09:27:54
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：<a href="https://www.luogu.org/problemnew/show/P3899" target="_blank"  rel="nofollow" >洛谷</a></p>
<hr />
<h1>Solution</h1>
<p>你们搞的这道题啊，excited！</p>
<p>.</p>
<p>这题真的很有意思。<br />
首先，我们可以先理解一下题面：固定一个a，找到一个b，c就是a与b的公共子树中的某个点。<br />
那么，我们显然可以把这个b分成两类，第一种是在a上面的，第二种在a下面的。</p>
<p>对于b在a上面的情况，<strong>显然，c一定是a的子树中的某个点，</strong>答案即为$min(K，depth[a])*size[a]$</p>
<p>对于b在a下面的情况，问题就会变得比较exciting了。<br />
我们可以考虑使用主席树来搞这个问题。<br />
考虑建一颗这样的主席树，<strong>以节点深度为主席树下标</strong>。<br />
对于一个节点，如果B在这个节点上，那么，它对答案的贡献显然是它的$size-1$。<br />
那么，我们把它的贡献插入到它对应的主席树中（以深度为下标）。<br />
每一个子节点开一颗主席树，记录到它为止所有的深度的答案和，有点类似前缀和，以dfs序为时间轴。<br />
这样，我们可以用一种类似树上差分的办法来“抠”出一颗子树对应的线段数（以深度为下标），这颗线段数中的$[depth[x]+1,depth[x]+K]$区间的sum就是这个x点的答案啦。</p>
<p>.</p>
<p>就酱，这题就被我们切掉啦٩(๑>◡&lt;๑)۶</p>
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
const long long N=300000+100;
const long long M=N*30;
struct SegmentTree
{
    #define mid ((now_l+now_r)&gt;&gt;1)
    long long son[M][2],cnt;
    long long sum[M];
    inline void update(long long now)
    {
        sum[now]=sum[son[now][0]]+sum[son[now][1]];
    }
    void Build(long long now,long long now_l,long long now_r)
    {
        if(now_l==now_r) 
            return;    
        Build(son[now][0]=++cnt,now_l,mid);
        Build(son[now][1]=++cnt,mid+1,now_r);
    }
    void Add(long long x,long long num,long long now,long long pre,long long now_l,long long now_r)
    {
        if(now_l==now_r)
        {
            sum[now]=sum[pre];
            sum[now]+=num;
            return ;
        }
        if(x&lt;=mid) son[now][1]=son[pre][1],Add(x,num,son[now][0]=++cnt,son[pre][0],now_l,mid);
        else son[now][0]=son[pre][0],Add(x,num,son[now][1]=++cnt,son[pre][1],mid+1,now_r);
        update(now);
    }
    long long Query(long long L,long long R,long long now,long long pre,long long now_l,long long now_r)
    {
        if(now_l&gt;=L and now_r&lt;=R)
            return sum[now]-sum[pre];
        long long ans=0;
        if(L&lt;=mid) ans+=Query(L,R,son[now][0],son[pre][0],now_l,mid);
        if(R&gt;mid) ans+=Query(L,R,son[now][1],son[pre][1],mid+1,now_r);
        return ans;
    }
    void Print(long long now,long long now_l,long long now_r)
    {
        cerr&lt;&lt;"no."&lt;&lt;now&lt;&lt;" now_l&amp;r:"&lt;&lt;now_l&lt;&lt;" "&lt;&lt;now_r&lt;&lt;" sonl&amp;r"&lt;&lt;son[now][0]&lt;&lt;" "&lt;&lt;son[now][1]&lt;&lt;" sum:"&lt;&lt;sum[now]&lt;&lt;endl;
        if(now_l!=now_r)
        {
            Print(son[now][0],now_l,mid);
            Print(son[now][1],mid+1,now_r);    
        }
    }    
    #undef mid
}sgt;
vector &lt;long long&gt; e[N];
long long n,m,depth[N],size[N],dfn[N],dfn_to,r[N];
void dfs2(long long now)
{
    dfn[now]=++dfn_to;
    size[now]=1;
    for(long long i=0;i&lt;(long long)(e[now].size());i++)
        if(depth[e[now][i]]==0)    
        {
            depth[e[now][i]]=depth[now]+1;
            dfs2(e[now][i]);
            size[now]+=size[e[now][i]];    
        }
}
void dfs(long long now)
{
    r[dfn[now]]=++sgt.cnt;
    sgt.Add(depth[now],size[now]-1,r[dfn[now]],r[dfn[now]-1],1,n);
    //sgt.Print(r[dfn[now]],1,n);
    //cerr&lt;&lt;endl;
    for(long long i=0;i&lt;(long long)(e[now].size());i++)
        if(depth[e[now][i]]&gt;depth[now])
            dfs(e[now][i]);
}
int main()
{
    n=read(),m=read();
    for(long long i=1;i&lt;=n;i++)
        e[i].reserve(4);
    for(long long i=1;i&lt;n;i++)
    {
        long long s=read(),t=read();
        e[s].push_back(t);
        e[t].push_back(s);    
    }

    sgt.Build(0,1,n);
    //sgt.Print(0,1,n);
    //cerr&lt;&lt;endl;
    depth[1]=1;
    dfs2(1);
    dfs(1);

    for(long long i=1;i&lt;=m;i++)
    {
        long long p=read(),K=read();
        long long ans=sgt.Query(depth[p]+1,depth[p]+K,r[dfn[p]+size[p]-1],r[dfn[p]-1],1,n);
        ans+=min(K,depth[p]-1)*(size[p]-1);
        printf("%lld\n",ans);
    }
    return 0;
}
</code></pre>
