---
title: "[Luogu P4606] [SDOI2018]战略游戏"
date: 2019-04-08 10:09:27
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P4606" target="_blank"  rel="nofollow" >P4606 [SDOI2018]战略游戏</a></p>
<hr />
<h1>Solution</h1>
<blockquote><p>
  圆方树上圆方果，<br />
    圆方树下你和我。<br />
    圆方树前建虚树，<br />
    欢乐多又多。
</p></blockquote>
<p><a href="https://imgchr.com/i/A5PwBd" target="_blank"  rel="nofollow" ><img src="https://s2.ax1x.com/2019/04/08/A5PwBd.th.png" alt="A5PwBd.th.png" /></a></p>
<p>.</p>
<p>好吧，我们来说正题。<br />
这题就比较强。<del>根据常识</del>，如果我们爆掉的点能影响这个图的连通性，<strong>那么，这个点一定是割点</strong>。<br />
因此，我们要先对原图做<strong>Tarjan求点双</strong>。接下来，我们考虑用圆方树来解决一个问题。</p>
<p>我们先考虑暴力怎么做，我们先对原图求出圆方树。接下来，我们发现，<strong>对答案有贡献的点一定是孩子有被选定的点的圆点，并且这个点的总共被选定的孩子数不等于总共被选定数</strong>（因为如果这个点被割掉了，其被选定的孩子一定会与其他点断开来，且方点代表一个点双，并不能割）。<br />
因此，我们可以直接统计一下有多少个有贡献的点即可。</p>
<p>接下来，我们进一步观察数据，发现$\sum S$非常小，因此考虑用虚树来解决这个问题。<br />
我们对每个询问的点构建虚树。这时候，我们就要多计算边的贡献，考虑一条边的贡献，即为这条边上有多少个原点，随便转移一下就好了。<del>好个鬼啊，细节比较多，大家注意一下</del></p>
<p>时间复杂度$O(\sum S \cdot log n)$<br />
就酱，这题就被我们切掉啦ヾ(●´∀｀●)</p>
<hr />
<h1>Code</h1>
<h3>数据生成器</h3>
<pre><code class="language-cpp line-numbers">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;ctime&gt;
#include&lt;cstdlib&gt;
#include&lt;cstring&gt;
using namespace std;
const int N=10;
const int M=15;
bool vis[N+5];
int main()
{
    srand(time(NULL));
    freopen("4606.in","w",stdout);

    cout&lt;&lt;"1\n";
    int n=N,m=M;
    cout&lt;&lt;n&lt;&lt;" "&lt;&lt;m&lt;&lt;endl;
    for(int i=2;i&lt;=n;i++)
        cout&lt;&lt;i&lt;&lt;" "&lt;&lt;max(rand()%i,1)&lt;&lt;endl; 
    for(int i=n;i&lt;=m;i++)
    {
        int s=rand()%n+1,t=rand()%n+1;
        cout&lt;&lt;s&lt;&lt;" "&lt;&lt;t&lt;&lt;endl;
    }

    int q=N;
    cout&lt;&lt;endl&lt;&lt;q&lt;&lt;endl;
    for(int i=1;i&lt;=q;i++)
    {
        int t=max(2,rand()%n+1);
        memset(vis,0,sizeof vis);
        cout&lt;&lt;t&lt;&lt;" ";
        for(int j=1;j&lt;=t;j++)
        {
            int tmp=rand()%n+1;
            while(vis[tmp]==true) 
                tmp=rand()%n+1;
            vis[tmp]=true;
            cout&lt;&lt;tmp&lt;&lt;" ";
        }
        cout&lt;&lt;endl;
    }
    return 0;
}

</code></pre>
<h3>正解</h3>
<pre><code class="language-cpp line-numbers">//Luogu P4606 [SDOI2018]战略游戏
//Apr,8th,2019
//圆方树+虚树
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;vector&gt;
#include&lt;cstring&gt;
#include&lt;algorithm&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=2*100000+100;
vector &lt;int&gt; e[N],e2[N],e3[N];
int n,m,q;
int low[N],cnt,dfn_to,dfn[N],mstack[N],top;
bool vis[N];
void Tarjan(int now,int father)
{
    vis[now]=true;
    dfn[now]=low[now]=++dfn_to;
    mstack[++top]=now;
    for(int i=0;i&lt;int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            Tarjan(e[now][i],now);
            low[now]=min(low[now],low[e[now][i]]);
            if(low[e[now][i]]&gt;=dfn[now])
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
    pre[now]=pre[father]+(now&lt;=n);
    for(int i=1;i&lt;=20;i++)
        fa[now][i]=fa[fa[now][i-1]][i-1];
    for(int i=0;i&lt;int(e2[now].size());i++)
        if(e2[now][i]!=father)
                dfs(e2[now][i],now);
}
int LCA(int x,int y)
{
    if(depth[x]&lt;depth[y]) swap(x,y);
    for(int i=20;i&gt;=0;i--)
        if(depth[x]-(1&lt;&lt;i) &gt;= depth[y])
            x=fa[x][i];
    if(x==y) return x;
    for(int i=20;i&gt;=0;i--)
        if(fa[x][i]!=fa[y][i])
            x=fa[x][i],y=fa[y][i];
    return fa[x][0];
}
bool cmp(int x,int y)
{
    return dfn[x]&lt;dfn[y];
}
bool sp[N];
int f[N];
int GetSum(int x,int y) //(x,y)
{
    return pre[y]-pre[x]-(y&lt;=n);
}
int dfs2(int now)
{
    f[now]=0;
    for(int i=0;i&lt;int(e3[now].size());i++)
        f[now]+=dfs2(e3[now][i])+GetSum(now,e3[now][i]);
    if(sp[now]==false and now&lt;=n and (now!=1 or e3[now].size()!=1))
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

    for(;T&gt;0;T--)
    {
        n=read(),m=read();

        for(int i=1;i&lt;=2*n;i++)
            e[i].clear(),e2[i].clear(),e3[i].clear();
        memset(vis,0,sizeof vis);
        dfn_to=0;

        for(int i=1;i&lt;=m;i++)
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
        for(int i=1;i&lt;=q;i++)
        {
            m=read();
            for(int j=1;j&lt;=m;j++)
                a[j]=read();

            sort(a+1,a+1+m,cmp);
            mstack[top=1]=1,cnt=0;
            for(int j=(a[1]==1?2:1);j&lt;=m;j++)
            {
                while(LCA(mstack[top],a[j])!=mstack[top])
                {
                    int lca=LCA(mstack[top],a[j]);
                    if(depth[lca] &gt; depth[mstack[top-1]])
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
            while(top&gt;1)
            {
                e3[mstack[top-1]].push_back(mstack[top]);
                rec[++cnt]=mstack[top--];
            }
            rec[++cnt]=1;

            for(int i=1;i&lt;=m;i++)
                sp[a[i]]=true;

            printf("%d\n",dfs2(1));

            for(int i=1;i&lt;=cnt;i++)
                sp[rec[i]]=false,e3[rec[i]].clear();
        }
    }
    return 0;
}

</code></pre>
