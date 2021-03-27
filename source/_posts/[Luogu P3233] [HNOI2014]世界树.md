---
title: "[Luogu P3233] [HNOI2014]世界树"
date: 2019-04-01 01:09:42
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P3233" target="_blank"  rel="nofollow" >P3233 [HNOI2014]世界树</a></p>
<hr />
<h1>Solution</h1>
<p>这是一道虚树妙题。</p>
<p>我们不妨先考虑一下每一次$O(n)$计算的暴力怎么做。<br />
$O(n\cdot m)$的暴力肥肠简单，我们只需要做两遍dfs。<strong>考虑设$f[i]$表示离$i$最近的聚居地是什么，$MIN[i]$表示$i$到最近的聚居地的距离。我们第一遍dfs先找出$i$到它子树内的聚居地的最小距离，之后再做一遍dfs来找$i$往祖先方向后头走能走到的最近聚居地的距离即可。</strong></p>
<p>观察数据范围后发现，$\sum m&lt;=300000$，因此考虑使用<strong>虚树</strong>。<br />
建出来虚树之后，显然对于在虚树上的点，我们还是能直接暴力做，问题是怎么处理非虚树上的点。<br />
我们会发现，<strong>我们虚树上的一条边在原树种对应一条链(包括链上的子树)。</strong>我们会发现，<strong>这条链上的点上一定是上半部分的最近距离在上面那个点，下半部分的最近距离在下面那个点。</strong>因此，我们考虑用倍增的思想来找出这个“分界点”，找到后计算一下上下分别贡献即可。<br />
这里有个小细节，我们是在原树上做倍增的，因此<strong>我们倍增过程中不应该使用跟DP有关的量</strong>，这里理论上我们只需要使用上端点与下端点的$f,MIN$，以及每个点的深度，$fa$即可实现这个倍增。</p>
<p>时间复杂度$O(mlogn)$<br />
就酱，我们就把这题切掉啦(*≧▽≦)</p>
<hr />
<h1>Code</h1>
<p><strong>本题细节较多，请各位dalao小心慢行</strong><br />
<del>直接两行泪就完事了</del><br />
<strong>数据生成器</strong></p>
<pre><code class="language-cpp line-numbers">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstdlib&gt;
#include&lt;ctime&gt;
#include&lt;cstring&gt;
using namespace std;
const int N=50;
bool used[N+5];
int main()
{
    srand(time(NULL));
    freopen("3233.in","w",stdout);

    int n=N;
    cout&lt;&lt;n&lt;&lt;endl;
    for(int i=2;i&lt;=n;i++)
        cout&lt;&lt;max(rand()%i,1)&lt;&lt;" "&lt;&lt;i&lt;&lt;endl;

    cout&lt;&lt;n&lt;&lt;endl;
    for(int i=1;i&lt;=n;i++)
    {
        memset(used,0,sizeof used);
        int m=rand()%n+1;
        cout&lt;&lt;m&lt;&lt;endl;

        for(int j=1;j&lt;=m;j++)
        {
            int t=rand()%n+1;
            while(used[t]==true)
                t=rand()%n+1;
            used[t]=true;
            cout&lt;&lt;t&lt;&lt;" ";
        }
        cout&lt;&lt;endl;
    }
    return 0;
}

</code></pre>
<p><strong>正解</strong></p>
<pre><code class="language-cpp line-numbers">//Luogu P3233 [HNOI2014]世界树
//Apr,1st,2019
//虚树+DP+倍增神题
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;vector&gt;
#include&lt;algorithm&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=300000+1000;
vector &lt;int&gt; e[N],e2[N];
int n,q,a[N],b[N];
int dfn[N],dfn_to,depth[N],fa[N][21],size[N];
void dfs(int now)
{
    dfn[now]=++dfn_to;
    size[now]=1;
    for(int i=1;i&lt;=20;i++)
        fa[now][i]=fa[fa[now][i-1]][i-1];
    for(int i=0;i&lt;int(e[now].size());i++)
        if(dfn[e[now][i]]==0)
        {
            depth[e[now][i]]=depth[now]+1;
            fa[e[now][i]][0]=now;
            dfs(e[now][i]);
            size[now]+=size[e[now][i]];
        }
}
int LCA(int x,int y)
{
    if(depth[x]&lt;depth[y]) swap(x,y);
    for(int i=20;i&gt;=0;i--)
        if(depth[x]-(1&lt;&lt;i)&gt;=depth[y])
            x=fa[x][i];
    if(x==y) return x;
    for(int i=20;i&gt;=0;i--)
        if(fa[x][i]!=fa[y][i])
            x=fa[x][i],y=fa[y][i];
    return fa[x][0];
}
int cmp(int x,int y)
{
    return dfn[x]&lt;dfn[y];
}
bool sp[N];
int MIN[N],f[N],ans[N];
inline int GetDis(int x,int y)
{
    if(depth[x]&lt;depth[y]) swap(x,y);
    return depth[x]-depth[y];
}
void dfs2(int now)
{
    if(sp[now]==true) 
        f[now]=now,MIN[now]=0;
    for(int i=0;i&lt;int(e2[now].size());i++)
    {
        dfs2(e2[now][i]);
        if(MIN[e2[now][i]]+GetDis(e2[now][i],now) &lt; MIN[now] 
        or (MIN[e2[now][i]]+GetDis(e2[now][i],now)==MIN[now] and f[now]&gt;f[e2[now][i]]))
            f[now]=f[e2[now][i]],MIN[now]=MIN[e2[now][i]]+GetDis(e2[now][i],now);
    }
}
void dfs3(int now,int fa) 
{
    if(fa!=0)
    {
        if(MIN[fa]+GetDis(fa,now) &lt; MIN[now] 
        or (MIN[fa]+GetDis(fa,now)==MIN[now] and f[now]&gt;f[fa]))
            f[now]=f[fa],MIN[now]=MIN[fa]+GetDis(fa,now);
    }
    ans[f[now]]++;
    for(int i=0;i&lt;int(e2[now].size());i++)
        dfs3(e2[now][i],now);
}
void GetSum(int x,int y,int &amp;sum_x,int &amp;sum_y)
{
    bool IsSwap=false;
    if(depth[x]&lt;depth[y]) IsSwap=true,swap(x,y);
    int sx=x,dis_x=MIN[x];
    for(int i=20;i&gt;=0;i--)
        if(dis_x+(1&lt;&lt;i) &lt; MIN[y]+depth[x]-depth[y]-(1&lt;&lt;i))
            f[fa[x][i]]=f[x],
            x=fa[x][i],dis_x+=(1&lt;&lt;i);
    if(dis_x+1==MIN[y]+depth[x]-depth[y]-1 and f[x]&lt;f[y])
        x=fa[x][0];
    sum_x=size[x]-size[sx];
    for(int i=20;i&gt;=0;i--)
        if(depth[sx]-(1&lt;&lt;i)&gt;depth[y])
            sx=fa[sx][i];
    sum_y=size[sx]-size[x];
    if(IsSwap==true)
        swap(sum_x,sum_y);
}
void dfs4(int now)
{
    int tmp=size[now]-1;
    for(int i=0;i&lt;int(e2[now].size());i++)
    {
        int sum1,sum2;
        GetSum(now,e2[now][i],sum1,sum2);
        ans[f[now]]+=sum1,ans[f[e2[now][i]]]+=sum2;
        tmp-=(size[e2[now][i]]+sum1+sum2);
        dfs4(e2[now][i]);
    }
    ans[f[now]]+=tmp;
}
int main()
{
    freopen("3233.in","r",stdin);
    freopen("3233.out","w",stdout);

    n=read();
    for(int i=1;i&lt;n;i++)
    {
        int s=read(),t=read();
        e[s].push_back(t);
        e[t].push_back(s);
    }

    fa[1][0]=1;
    dfs(1);

    q=read();
    for(int i=1;i&lt;=q;i++)
    {
        int m=read();
        for(int j=1;j&lt;=m;j++)
            b[j]=a[j]=read();

        sort(a+1,a+1+m,cmp);
        static int mstack[N],top,rec[N],cnt;
        cnt=0;
        mstack[top=1]=1;
        for(int j=(a[1]==1?2:1);j&lt;=m;j++)
        {
            while(LCA(mstack[top],a[j])!=mstack[top])
            {
                int lca=LCA(mstack[top],a[j]);
                if(depth[lca]&gt;depth[mstack[top-1]])
                {
                    e2[lca].push_back(mstack[top]);
                    rec[++cnt]=mstack[top],mstack[top]=lca;
                }
                else
                {
                    e2[mstack[top-1]].push_back(mstack[top]);
                    rec[++cnt]=mstack[top--];
                }
            }
            mstack[++top]=a[j];
        }
        while(top&gt;1)
        {
            e2[mstack[top-1]].push_back(mstack[top]);
            rec[++cnt]=mstack[top--];
        }
        rec[++cnt]=1;

        for(int j=1;j&lt;=m;j++)
            sp[a[j]]=true;
        for(int j=1;j&lt;=cnt;j++)
            MIN[rec[j]]=0x3f3f3f3f,ans[rec[j]]=0;
        dfs2(1); 
        dfs3(1,0);
        dfs4(1);
        for(int j=1;j&lt;=m;j++)
            printf("%d ",ans[b[j]]);
        printf("\n");

        for(int j=1;j&lt;=m;j++)
            sp[a[j]]=false;
        for(int j=1;j&lt;=cnt;j++)
            e2[rec[j]].clear(),ans[rec[j]]=0;
    }
    return 0;
}

</code></pre>
