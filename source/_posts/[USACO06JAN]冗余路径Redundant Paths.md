---
title: "[USACO06JAN]冗余路径Redundant Paths"
date: 2019-04-08 15:02:19
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P2860" target="_blank"  rel="nofollow" >P2860 [USACO06JAN]冗余路径Redundant Paths</a></p>
<hr />
<h1>Solution</h1>
<p>首先，我们可以发现题目要求每一个点到其他所有点的路径不只有一条，<strong>这本质上就是要我们把这个图所有的桥都消除掉。</strong></p>
<p>要消除掉桥，首先必须要把边双先缩起来。缩边双很简单：<strong>和求强连通分量一模一样，唯一要注意的是我们要多记录一个$fa$，防止我们求$low$的时候直接把$fa$算进来。</strong><br />
求完边双之后，我们会发现原图变成一个树的形式。想象一下：我们要把这个树上所有的单边去掉，我们只需要把叶子节点两两连起来即可。<strong>（注意，这里的叶子节点是广义的（即根也有可能是叶子节点））</strong><br />
还有一个小细节：对于直接就是一个环的情况，我们要特判一下，直接输出0即可。</p>
<p>时间复杂度$O(n)$<br />
就酱，这题就被我们切掉啦︿(￣︶￣)︿</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//Luogu P2860 [USACO06JAN]冗余路径Redundant Paths
//Apr,8th,2019
//边双
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
const int N=5000+100;
vector &lt;int&gt; e[N],e2[N];
int n,m;
int dfn[N],low[N],dfn_to,InStack[N],mstack[N],top,belong[N],cnt;
bool vis[N];
void Tarjan(int now,int father)
{
    vis[now]=InStack[now]=true;
    dfn[now]=low[now]=++dfn_to;
    mstack[++top]=now;
    for(int i=0;i&lt;int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            Tarjan(e[now][i],now);
            low[now]=min(low[now],low[e[now][i]]);
        }
        else if(e[now][i]!=father and InStack[e[now][i]]==true)
            low[now]=min(low[now],dfn[e[now][i]]);
    if(low[now]==dfn[now])
    {
        cnt++;
        while(mstack[top+1]!=now)
            InStack[mstack[top]]=false,
            belong[mstack[top--]]=cnt;
    }
}
int GetAns(int now,int father)
{
    int ans=0;
    for(int i=0;i&lt;int(e2[now].size());i++)
        if(e2[now][i]!=father)
            ans+=GetAns(e2[now][i],now);
    return max(1,ans);
}
int main()
{
    n=read(),m=read();
    for(int i=1;i&lt;=n;i++)
        e[i].reserve(4);
    for(int i=1;i&lt;=m;i++)
    {
        int s=read(),t=read();
        e[s].push_back(t);
        e[t].push_back(s);
    }

    Tarjan(1,0);

    for(int i=1;i&lt;=n;i++)
        for(int j=0;j&lt;int(e[i].size());j++)
            if(belong[i]!=belong[e[i][j]])
                e2[belong[i]].push_back(belong[e[i][j]]);
    int ans=GetAns(belong[1],belong[1])+(e2[belong[1]].size()==1);
    if(cnt==1)//特判只有一个环
        ans=0;
    printf("%d",ans/2+ans%2);
    return 0;
}

</code></pre>
