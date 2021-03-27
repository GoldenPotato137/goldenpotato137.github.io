---
title: "[Luogu P4215] 踩气球"
date: 2019-02-22 09:23:08
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P4215" target="_blank"  rel="nofollow" >洛谷</a></p>
<hr />
<h1>Solution</h1>
<p>这题十分有意思。</p>
<p>首先，我们可以先想想离线做法，因为在线做法可以从离线做法推出。<del>（虽然这题推不出）</del></p>
<p>我们可以明确一点，一个熊孩子开心的时间是满足二分的要求的（如果他某个时刻开心了，那之后的时刻都会保持开心）。</p>
<p>对于判断一个区间是否为全0，我们可以用主席树以一个log的代价来判断。</p>
<p>得到每个熊孩子开心的时刻之后，我们就可以直接前缀和解决问题了。</p>
<p>时间复杂度$O(m*log^2)$</p>
<p>.</p>
<p>很可惜，这题强制在线。</p>
<p>很可惜*2，<del>刚刚的做法跟正解一点关系都没有</del>。</p>
<p>我们可以考虑用线段树。</p>
<p>问题是怎么判断一个熊孩子在某个操作后是否开心呢？</p>
<p>我们显然可以快速地判断线段树上的一个直接的区间（即这个区间可以用一个节点表示）是否全为0，问题是我们不能很快地判断一个非直接的区间是否全为0。</p>
<p>所以说，我们可以考虑<strong>把熊孩子“拆开”</strong>。</p>
<p>因为<strong>一个熊孩子的区间一定可以表示为线段树上的几个直接区间，我们可以在这些直接区间上打上标记，记录这个区间被哪几个熊孩子直接包含</strong>。</p>
<p>我们<strong>再记录一下每个熊孩子被拆成了几个区间</strong>。</p>
<p><strong>一个区间全部变为0的时候，我们把它对应的熊孩子的记录值-1，当一个熊孩子记录值为0的时候，就代表着这个熊孩子的区间被彻底干掉了</strong>。</p>
<p>时间复杂度$O(mlogn+qlogn)$</p>
<p>.</p>
<p>就酱，这题就被我们切掉啦φ(>ω&lt;*)</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">//Luogu P4215 踩气球
//Oct,14th,2018
//有意思的线段树
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
const int N=100000+100;
int ans,tot[N];
struct SegmentTree
{
    #define lson (now&lt;&lt;1)
    #define rson (now&lt;&lt;1|1)
    #define mid ((now_l+now_r)/2)
    vector &lt;int&gt; son[N&lt;&lt;2];
    int IsClear[N&lt;&lt;2],cnt[N];
    inline void update(int now)
    {
        IsClear[now]=IsClear[lson]&amp;IsClear[rson];
    }
    void Mark(int L,int R,int x,int now,int now_l,int now_r)
    {
        if(now_l&gt;=L and now_r&lt;=R)
        {
            tot[x]++;
            son[now].push_back(x);
            return;
        }
        if(L&lt;=mid) Mark(L,R,x,lson,now_l,mid);
        if(R&gt;mid) Mark(L,R,x,rson,mid+1,now_r);
    }
    void Sub(int x,int now,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            cnt[x]--;
            if(cnt[x]==0)
                IsClear[now]=true;
        }
        if(now_l!=now_r)
        {
            if(x&lt;=mid) Sub(x,lson,now_l,mid);
            else Sub(x,rson,mid+1,now_r);
            update(now);
        }
        if(IsClear[now]==true)
            for(int i=0;i&lt;int(son[now].size());i++)
            {
                tot[son[now][i]]--;
                if(tot[son[now][i]]==0)
                    ans++;
            }
    }
    #undef lson
    #undef rson
    #undef mid
}sgt;
int n,m,q;
int main()
{
    n=read(),m=read();
    for(int i=1;i&lt;=n;i++)
        sgt.cnt[i]=read();
    for(int i=1;i&lt;=m;i++)
    {
        int L=read(),R=read();
        sgt.Mark(L,R,i,1,1,n);
    }

    int q=read(),lans=0;
    for(int i=1;i&lt;=q;i++)
    {
        int x=read();
        x=(x+lans-1)%n+1;
        sgt.Sub(x,1,1,n);
        lans=ans;
        printf("%d\n",lans);
    }
    return 0;
}

</code></pre>
