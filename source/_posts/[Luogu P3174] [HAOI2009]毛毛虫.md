---
title: "[Luogu P3174] [HAOI2009]毛毛虫"
date: 2019-03-11 06:47:01
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P3174" target="_blank"  rel="nofollow" >洛谷P3174</a></p>
<hr />
<h1>Solution</h1>
<p>我们不难发现，一条“毛毛虫”一定是由一条主链外加主链的点所连到的点构成的。<br />
那既然是一条链，它的形态无外乎以下两种：<br />
<img src="https://s2.ax1x.com/2019/03/11/ACQopn.png" alt="ACQopn.png" /><br />
因此，我们可以<strong>直接枚举最上面的那个点，他做为根会产生的最大的答案即为其孩子的最大答案与次大答案之和再减去多算的一小部分即可</strong>。(具体转移可以看看代码，要做点简单的分类讨论)</p>
<p>时间复杂度$O(n)$<br />
就酱，这题我们就切掉啦(*´ﾟ∀ﾟ｀)ﾉ</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//Luogu P3174 [HAOI2009]毛毛虫
//Mar,10th,2019
//树形DP
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
const int N=300000+100;
vector &lt;int&gt; e[N];
int n,m;
bool vis[N];
int f1[N],f2[N],ans;
void dfs(int now)
{
    vis[now]=true;
    f1[now]=e[now].size()+1;
    for(int i=0;i&lt;int(e[now].size());i++)
        if(vis[e[now][i]]==false)
        {
            dfs(e[now][i]);
            if(f1[e[now][i]]+int(e[now].size())-1 &gt;= f1[now])
            {
                f2[now]=f1[now];
                f1[now]=f1[e[now][i]]+e[now].size()-1;
            }
            else if(f1[e[now][i]]+int(e[now].size())-1 &gt; f2[now])
                f2[now]=f1[e[now][i]]+e[now].size()-1;
        }

    if(f2[now]==0)
        ans=max(ans,f1[now]);
    else
        ans=max(ans,f1[now]+f2[now]-int(e[now].size())-1);
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

    dfs(1);

    printf("%d",ans);
    return 0;
}

</code></pre>
