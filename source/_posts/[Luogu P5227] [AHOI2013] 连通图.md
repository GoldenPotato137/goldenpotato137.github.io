---
title: "[Luogu P5227] [AHOI2013] 连通图"
date: 2019-03-22 03:50:13
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P5227" target="_blank"  rel="nofollow" >P5227 [AHOI2013]连通图</a></p>
<hr />
<h1>Solution</h1>
<p>这题可以离线，因此我们可以考虑用线段树分治维护动态图连通性来搞。</p>
<p>这题我们考虑离线下来搞。离线之后，我们会发现，<strong>某条边会在某些询问区间中出现。</strong><br />
考虑<strong>以询问的编号为下标建线段树，我们把每一条边出现的时间段全部加到线段树里面去。</strong><br />
接下来，直接<strong>在线段树上跑dfs</strong>,每到一个区间，就把这个区间里面存的边通通在并查集中连上；每完成一个区间，就把这个区间连上的边通通取消（类似于回溯）。<br />
这样搞，我们每次到一个叶子节点的时候，这个叶子节点代表的询问上所要连的边一定已经全部连上了，直接在并查集中查询任意节点的父亲的$size$是否为$n$即可。</p>
<p>因为我们这里有撤销（回溯）操作，因此必需使用**按秩合并的并查集。我们只需要开一个栈，把每次修改的fa的节点记录下来即可完成撤销的操作。</p>
<p>时间复杂度$O(nlog^2n)$ 吗？<br />
问题是这玩意能过啊.....<br />
<del>因此，我们的时间复杂度是$O($能过$)$</del></p>
<p>就酱，我们又切掉一道题啦。(ﾉﾟ∀ﾟ)ﾉ</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//Luogu P5227 [AHOI2013]连通图
//Mar,22ed,2019
//线段树分治离线维护动态图连通性
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;vector&gt;
#include&lt;ctime&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=100000+100;
const int M=200000+100;
int n,m,q,p,ans[M];
struct UnF
{   
    int size[N],fa[N],mstack[M],top;
    void Init()
    {
        for(int i=1;i&lt;=n;i++)
            size[i]=1;
    }
    int FindFather(int x)
    {
        if(fa[x]==0) return x;
        return FindFather(fa[x]);
    }
    void Link(int x,int y)
    {
        mstack[++top]=0;
        int fa_x=FindFather(x),fa_y=FindFather(y);
        if(size[fa_x]&gt;size[fa_y]) 
            swap(x,y),swap(fa_x,fa_y);
        if(fa_x==fa_y) return;
        mstack[top]=fa_x;   
        fa[fa_x]=fa_y,size[fa_y]+=size[fa_x];
    }
    int Query()
    {
        if(size[FindFather(1)]==n) return true;
        return false;
    }
    void Undo()
    {
        if(mstack[top]==0)
        {
            top--;
            return;
        }
        size[fa[mstack[top]]]-=size[mstack[top]];
        fa[mstack[top]]=0;
        top--;
    }
}unf;
struct OP
{
    int s,t,id;
}op2[M*20],e[M];
struct SegmentTree
{
    #define mid ((now_l+now_r)&gt;&gt;1)
    #define lson (now&lt;&lt;1)
    #define rson (now&lt;&lt;1|1)
    vector &lt;int&gt; w[M&lt;&lt;2];
    void Insert(int l,int r,int x,int now,int now_l,int now_r)
    {
        if(now_l&gt;=l and now_r&lt;=r)
        {
            w[now].push_back(x);
            return;
        }
        if(l&lt;=mid) Insert(l,r,x,lson,now_l,mid);
        if(r&gt;mid) Insert(l,r,x,rson,mid+1,now_r);
    }
    void dfs(int now,int now_l,int now_r)
    {
        if(now_l&gt;now_r) return;
        for(int i=0;i&lt;int(w[now].size());i++)
            unf.Link(e[w[now][i]].s,e[w[now][i]].t);
        if(now_l==now_r)
            ans[now_l]=unf.Query();
        else
        {
            dfs(lson,now_l,mid);
            dfs(rson,mid+1,now_r);
        }
        for(int i=0;i&lt;int(w[now].size());i++)
            unf.Undo();
    }
    #undef mid
    #undef lson
    #undef rson
}sgt;
int last[M],K;
int main()
{
    n=read(),m=read();
    for(int i=1;i&lt;=m;i++)
        e[i].s=read(),e[i].t=read(),last[i]=1;
    K=read();
    for(int i=1;i&lt;=K;i++)
    {
        int c=read();
        for(int j=1;j&lt;=c;j++)
        {
            int x=read();
            op2[++p].s=last[x],op2[p].t=i-1,op2[p].id=x;
            last[x]=i+1;
        }
    }
    for(int i=1;i&lt;=m;i++)
        if(last[i]!=K+1)
            op2[++p].s=last[i],op2[p].t=K,op2[p].id=i;

    for(int i=1;i&lt;=p;i++)
        if(op2[i].s&lt;=op2[i].t)
        {
            sgt.Insert(op2[i].s,op2[i].t,op2[i].id,1,1,K);
            //cerr&lt;&lt;op2[i].s&lt;&lt;" "&lt;&lt;op2[i].t&lt;&lt;" "&lt;&lt;op2[i].id&lt;&lt;endl;
        }

    unf.Init();
    sgt.dfs(1,1,K);

    for(int i=1;i&lt;=K;i++)
        if(ans[i]==0)
            printf("Disconnected\n");
        else
            printf("Connected\n");
    return 0;
}

</code></pre>
