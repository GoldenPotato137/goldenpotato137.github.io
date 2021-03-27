---
title: "[Luogu P3645] [APIO2015]雅加达的摩天楼"
date: 2019-03-19 13:15:47
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P3645" target="_blank"  rel="nofollow" >P3645 [APIO2015]雅加达的摩天楼</a></p>
<hr />
<h1>Solution</h1>
<p>与其说这题是分块妙题，我更倾向于把这题称为分层图妙题。</p>
<p>这题有一个一眼贪心做法：<strong>对于每只doge，我们都暴力地去建它连向它能跳到的点的边，边权为跳的次数。然后直接求一遍单元最短路即可</strong>。<br />
很显然，这玩意的边的数量是$O(n^2)$的，求一遍最短路的复杂度达到了惊人的$n^2logn^2$</p>
<p>这显然是要T飞的，但是我们会从中发现一个问题：既然一个doge的跳跃是多步的，那我们能否直接把几步拆开来，然后省略重复的边？<br />
例如：<br />
<img src="https://s2.ax1x.com/2019/03/19/Au13S1.png" alt="Au13S1.png" /><br />
优化为：<br />
<img src="https://s2.ax1x.com/2019/03/19/Au8AbT.png" alt="Au8AbT.png" /></p>
<p>这样做看起来很星，很不幸的是，这样是不行的。因为我们在计算最短路的时候，我们有可能直接从中间某个点出发，但是很不幸的是，这样是不可行的。</p>
<p>怎么办呢？这时我们可以考虑把网络流的分层图那一套搬出来。<br />
我们可以考虑使用“拆点”的做法来限制从某个点出发去更新别的点的最短路。<br />
考虑把一个点拆分为size个点，<strong>每个拆分点的含义为所有一次跳x步的都从它出发，并到达它那里</strong>。<br />
<strong>从每个点的拆分点出发，向它的原点连一条边权为0的有向边</strong><br />
<strong>如果能从某个点出发，则对应的从原点连向那个跳x格远的分点连一条边权为0的边</strong><br />
<strong>接下来我们从每个点的对应的跳x格远的的点连向其他的点的跳x格的点，边权为1</strong></p>
<p>一图胜千言：<br />
<img src="https://s2.ax1x.com/2019/03/19/AuUNi8.png" alt="AuUNi8.png" /><br />
变为<br />
<strong>最后一行的所有点即为原来的点</strong><br />
<strong>从下往上第x行的点即为某个原点的第x个分点</strong><br />
<a href="https://imgchr.com/i/AuaFfS" target="_blank"  rel="nofollow" ><img src="https://s2.ax1x.com/2019/03/19/AuaFfS.png" alt="AuaFfS.png" /></a></p>
<p>通过这样一轮拆分，我们就已经可以解决问题啦。</p>
<p>啥？你说这样会有$n^2$个点？这是就得用到分块思想啦。你想，如果一个doge一次能跳的距离超过$\sqrt n$格远，那总共连出来的边不会超过$\sqrt n$条，我们直接在原点连就好啦qwq。</p>
<p><del>根据玄学证明，这里的块大小取100是最好的（我并不会证）</del><br />
时间复杂度O(能过)</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">// luogu-judger-enable-o2
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;vector&gt;
#include&lt;cmath&gt;
#include&lt;cstring&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=30000+100;
const int M=100+20;
struct edge
{
    int to,w;
    edge (int x,int y)
    {
        to=x,w=y;
    }
};
vector &lt;edge&gt; e[N*M];
int n,m,size,dis[N*M],S,T;
void spfa()
{
    static int InQueue[N*M],mqueue[N*M*10],front,tail;
    memset(dis,0x3f,sizeof dis);
    front=tail=0;
    mqueue[tail++]=S*size,dis[S*size]=0;
    while(tail&gt;front)
    {
        int now=mqueue[front++];
        InQueue[now]=false;
        for(int i=0;i&lt;int(e[now].size());i++)
            if(dis[e[now][i].to]&gt;dis[now]+e[now][i].w)
            {
                dis[e[now][i].to]=dis[now]+e[now][i].w;
                if(InQueue[e[now][i].to]==false)
                {
                    InQueue[e[now][i].to]=true;
                    mqueue[tail++]=e[now][i].to;
                }
            }
    }
}
int main()
{
    //freopen("3645.in","r",stdin);
    //freopen("3645.out","w",stdout);

    int t=clock();
    n=read(),m=read();
    size=min(int(sqrt(n)),50);
    int to=n*size;
    for(int i=1;i&lt;=to;i++)
        e[i].reserve(4);
    for(int i=0;i&lt;n;i++)
        for(int j=1;j&lt;size;j++)
            e[i*size+j].push_back(edge(i*size,0));
    for(int i=1;i&lt;=m;i++)
    {
        int b=read(),p=read();
        if(i==1) S=b;
        if(i==2) T=b;
        if(p&gt;=size)
        {
            for(int j=b+p,k=1;j&lt;n;j+=p,k++)
                e[b*size].push_back(edge(j*size,k));
            for(int j=b-p,k=1;j&gt;=0;j-=p,k++)
                e[b*size].push_back(edge(j*size,k));
        }
        else
        {
            e[b*size].push_back(edge(b*size+p,0));
            for(int j=b;j&lt;n-p;j+=p)
            {
                bool OK=false;
                for(int k=0;k&lt;int(e[j*size+p].size());k++)
                    if(e[j*size+p][k].to==(j+p)*size+p)
                    {
                        OK=true;
                        break;
                    }
                if(OK==true) break;
                e[j*size+p].push_back(edge((j+p)*size+p,1));
            }
            for(int j=b;j&gt;=p;j-=p)
            {
                bool OK=false;
                for(int k=0;k&lt;int(e[j*size+p].size());k++)
                    if(e[j*size+p][k].to==(j-p)*size+p)
                    {
                        OK=true;
                        break;
                    }
                if(OK==true) break;
                e[j*size+p].push_back(edge((j-p)*size+p,1));
            }
        }
    }

    spfa();

    if(dis[T*size]&lt;0x3f3f3f3f)
        printf("%d",dis[T*size]);
    else
        printf("-1");
    cerr&lt;&lt;clock()-t;
    return 0;
}

</code></pre>
