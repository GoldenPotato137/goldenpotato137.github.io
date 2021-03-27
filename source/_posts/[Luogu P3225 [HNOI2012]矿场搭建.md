---
title: "[Luogu P3225 [HNOI2012]矿场搭建"
date: 2019-04-08 13:44:54
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P3225" target="_blank"  rel="nofollow" >P3225 [HNOI2012]矿场搭建</a></p>
<hr />
<h1>Solution</h1>
<p>这题比较妙。</p>
<p>首先，<del>根据常识</del>，<strong>如果一个点爆了，当且仅当它是割点的时候才会影响整个图的连通性</strong>。<br />
因此，我们考虑把这道题往点双那方面想。</p>
<p>接下来我们思考这个问题：对于一个点双，我们什么时候需要在它这里面放置逃生通道：<br />
1. 如果与它相连的点双块只有一个：<strong>如果爆的是割点，则必须在当前块中的任意点建一个通道；如果爆的是普通点，则当前块则可以与别的块照样联通。</strong><br />
2. 如果与它相连的点双块有两个以上：<strong>无论爆的是割点还是普通点，都不影响它里面的其他点到其他块去逃生</strong>。<br />
综上，<strong>我们发现我们只需要在只与1个其他点双块连接的点双块防止逃生通道即可</strong>。我在这里暂时称这种块为“叶子点双”。<br />
如下图：我们只需要在紫色的点双块中每一个都放置一个逃生通道即可。<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wgy1g1vjp7xbxmj30el0bt0tm.jpg" alt="" /></p>
<p>因此，要放置的逃生通道的总数为“叶子点双”的个数，总方案为$\prod (size[x]-1)$ （x为叶子点双）（由乘法原理可得）。</p>
<p>但是，我们要小心一个细节：就是我们在做Tarjan的时候，无论如何都会把1号节点认为是割点。但是，1号节点有可能并不是割点。<strong>1号节点有可能属于某一个点双块，而且这个点双块是有可能为叶子节点的</strong>，我们做的时候要小心判断一下。</p>
<p>接下来，实现的话就随便写就好。<del>笔者写的时候脑袋有点犯二，就用了圆方树来实现</del><br />
时间复杂度$O(\sum n)$<br />
就酱，这道题就被我们切掉啦(～￣▽￣)～</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//Luogu P3225 [HNOI2012]矿场搭建
//Apr,8th,2019
//Tarjan求点双+圆方树
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;vector&gt;
#include&lt;cstring&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=2*500+20;
vector &lt;int&gt; e[N],e2[N];
int n,m;
int dfn[N],low[N],dfn_to,mstack[N],top,cnt,size[N];
bool vis[N];
void Tarjan(int now,int father)
{
    vis[now]=true;
    low[now]=dfn[now]=++dfn_to;
    mstack[++top]=now;
    for(int i=0;i&lt;int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            Tarjan(e[now][i],now);
            low[now]=min(low[now],low[e[now][i]]);
            if(low[e[now][i]] &gt;= dfn[now])
            {
                e2[now].push_back(n+ ++cnt);
                size[cnt]=1;
                while(mstack[top+1]!=e[now][i])
                    e2[n+cnt].push_back(mstack[top--]),
                    size[cnt]++;
            }
        }
        else if(e[now][i]!=father)
            low[now]=min(low[now],dfn[e[now][i]]);
}
long long ans,ans2;
int dfs(int now)//返回now的子树内的方点个数
{
    int t_cnt=(now&gt;n);
    for(int i=0;i&lt;int(e2[now].size());i++)
        t_cnt+=dfs(e2[now][i]);
    if(now&gt;n and t_cnt==1)
        ans2++,ans*=(size[now-n]-1);
    return t_cnt;
}
bool Check()//判断1所在块是否为叶子块
{
    int x=e2[1][0],t_cnt=0;
    for(int i=0;i&lt;int(e2[x].size());i++)
        if(e2[e2[x][i]].size()!=0)
            t_cnt++;
    return t_cnt==1;
}
int main()
{
    for(int o=1;;o++)
    {
        m=read();
        if(m==0) break;
        for(int i=1;i&lt;=500*2+5;i++)
            e[i].clear(),e2[i].clear();
        dfn_to=cnt=0;
        memset(vis,0,sizeof vis);   

        n=0;
        for(int i=1;i&lt;=m;i++)
        {
            int s=read(),t=read();
            e[s].push_back(t);
            e[t].push_back(s);
            n=max(max(n,s),t);
        }

        Tarjan(1,0);
        ans=1,ans2=0;
        dfs(1);
        if(e2[1].size()==1 and Check()==true)//特殊处理1号节点
            ans*=(size[e2[1][0]-n]-1),
            ans2++;
        if(cnt==1)//特判只有一个连通块的情况
            ans=(n*(n-1))/2,ans2=2;


        printf("Case %d: %lld %lld\n",o,ans2,ans);
    }
    return 0;
}

</code></pre>
