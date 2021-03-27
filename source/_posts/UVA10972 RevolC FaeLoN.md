---
title: "UVA10972 RevolC FaeLoN"
date: 2019-04-09 03:47:47
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/UVA10972" target="_blank"  rel="nofollow" > UVA10972 RevolC FaeLoN</a></p>
<hr />
<h1>Solution</h1>
<p>这题就比较牛皮。</p>
<p>我们先来考虑一下图联通的话怎么做。显然，我们可以先把图按边双缩点，边双内部是肯定不用加任何一条有向边就能改成强连通分量的（易证）。<br />
缩完点之后，图一定会变成一颗树。接下来我们依旧可以像<a href="https://www.luogu.org/problemnew/show/P2860" target="_blank"  rel="nofollow" >这道题</a>那样贪心。我们数一下广义叶子数有多少，要加的边的个数一定为$sum/2$（向上取整）。<br />
连边方式如图所示：<br />
接下来再来考虑不连通的情况。显然，我们可以发现，对于多颗树来说，我们依旧可以照样刚刚那样贪心。我们左右两棵树在叶子那里连边即可。
因此，我们的总答案依旧是$sum/2$（向上取整）</p>
<p>时间复杂度$O(n)$<br />
就酱，这题就被我们切掉啦(*≧▽≦)</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//UVA10972 RevolC FaeLoN
//Apr,9th,2019
//边双
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
const int N=1000+10;
vector &lt;int&gt; e[N],e2[N];
int dfn[N],dfn_to,low[N],mstack[N],top,belong[N],cnt;
bool vis[N],InStack[N];
void Tarjan(int now,int father)
{
    vis[now]=InStack[now]=true;
    mstack[++top]=now;
    dfn[now]=low[now]=++dfn_to;
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
int n,m;
int dfs(int now,int father)
{
    vis[now]=true;
    int t_ans=0;
    for(int i=0;i&lt;int(e2[now].size());i++)
        if(e2[now][i]!=father)
            t_ans+=dfs(e2[now][i],now);
    if(e2[now].size()==1)
        t_ans++;
    if(e2[now].size()==0)
        t_ans+=2;
    return t_ans;
}
int main()
{
    for(int o=1;;o++)
    {
        if(scanf("%d%d",&amp;n,&amp;m)==EOF) break;

        for(int i=0;i&lt;=n;i++)
            e[i].clear(),e2[i].clear();
        for(int i=1;i&lt;=n;i++)
            e[i].reserve(4),e2[i].reserve(4);
        for(int i=1;i&lt;=m;i++)
        {
            int s=read(),t=read();
            e[s].push_back(t);
            e[t].push_back(s);
        }

        memset(vis,0,sizeof vis);
        memset(mstack,0,sizeof mstack);
        dfn_to=cnt=0;
        for(int i=1;i&lt;=n;i++)
            if(vis[i]==false)
                Tarjan(i,i);

        for(int i=1;i&lt;=n;i++)
            for(int j=0;j&lt;int(e[i].size());j++)
                if(belong[i]!=belong[e[i][j]])
                    e2[belong[i]].push_back(belong[e[i][j]]);
        memset(vis,0,sizeof vis);
        if(cnt==1)
            printf("0\n");
        else
        {
            int ans=0;
            for(int i=1;i&lt;=n;i++)
                if(vis[belong[i]]==false)
                    ans+=dfs(belong[i],belong[i]);
            printf("%d\n",ans/2+ans%2);
        }
    }
    return 0;
}

</code></pre>
