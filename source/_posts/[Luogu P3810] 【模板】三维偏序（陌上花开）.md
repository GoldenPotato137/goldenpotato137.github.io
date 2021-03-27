---
title: "[Luogu P3810] 【模板】三维偏序（陌上花开）"
date: 2019-03-27 07:11:54
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P3810" target="_blank"  rel="nofollow" >P3810 【模板】三维偏序（陌上花开）</a></p>
<hr />
<h1>Solution</h1>
<p>这是一道CDQ分治的模板题。</p>
<p>题目要我们求的是$(a,b,c)$这样的三维“顺序对”的数量。<br />
<strong>考虑我们把所有的数按照以$a$为第一关键字，以$b$为第二关键字，以$c$为第三关键字来排序。</strong><br />
这样子，我们就可以保证<strong>有可能与某个数形成“顺序对”的数一定在它的左边</strong>。</p>
<p>我们都知道，归并排序能用来求逆序对的数量，在这里，也能用类似的方法来求“顺序对”的数量。<br />
接下来我们考虑对这个排好序的序列做<strong>以$b$为关键字的类似于归并排序的操作</strong>。<br />
这里我们要排序的是$[1,n]$这一整个序列，我们要递归下去做，假设我们现在已经求出了$[1,mid]$与$[mid+1,n]$这两个区间内部的“顺序对”的数量，现在我们考虑求出跨越两个区间的顺序对。<strong>因为我们归并排序前已经按照$a$排序过了，所以这里有且只有可能右边块的数会与左边块的数会形成“顺序对”。</strong><br />
接下来，我们考虑维护左右两个块两个指针，<strong>保证右边块当前看的数的$b$比左边指针扫到的所有的数的$b$都大。</strong>这时候，<strong>我们只需要找到左边扫到的数的$c$有多少个比当前扫到的数的$c$小。</strong>这个东西我们用一个树状数组/线段树直接维护即可。</p>
<p>最后我们以$b$为关键字排序这两个区间，递归回去继续做即可。<br />
注意一点细节：有可能会有几个元素的$(a,b,c)$全部相等，我们在刚刚的计算中是不会算到它们的相互影响的。对于这种情况，我们要提前为他们算上内部的影响，然后去重做即可。</p>
<p>在这里，我们在第二维上做的类似于归并排序的操作即为CDQ分治。</p>
<p>时间复杂度$O(nlog^2n)$<br />
就酱，我们又切掉一道题啦(๑¯∀¯๑)</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//Luogu P3810 【模板】三维偏序（陌上花开） 
//Mar,26th,2019
//CDQ分治+线段树+排序维护三维偏序
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
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
struct node
{
    int x,y,z,no,ans;
    friend bool operator == (node a,node b)
    {
        return (a.x==b.x and a.y==b.y and a.z==b.z);
    }
}nd[N];
bool cmp(node a,node b)
{
    if(a.x==b.x)
    {
        if(a.y==b.y)
            return a.z&lt;b.z;
        return a.y&lt;b.y;
    }
    return a.x&lt;b.x;
}
bool cmp2(node a,node b)
{
    if(a.y==b.y) return a.z&lt;b.z;
    return a.y&lt;b.y;
}
struct SegmentTree
{
    #define lson (now&lt;&lt;1)
    #define rson (now&lt;&lt;1|1)
    #define mid ((now_l+now_r)&gt;&gt;1)
    int sum[M&lt;&lt;2];
    inline void update(int now)
    {
        sum[now]=sum[lson]+sum[rson];
    }
    void Add(int x,int v,int now,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            sum[now]+=v;
            return ;
        }
        if(x&lt;=mid)  Add(x,v,lson,now_l,mid);
        else Add(x,v,rson,mid+1,now_r);
        update(now);
    }
    int Query(int l,int r,int now,int now_l,int now_r)
    {
        if(now_l&gt;=l and now_r&lt;=r)
            return sum[now];
        int t_ans=0;
        if(l&lt;=mid) t_ans+=Query(l,r,lson,now_l,mid);
        if(r&gt;mid) t_ans+=Query(l,r,rson,mid+1,now_r);
        return t_ans;
    }
    #undef lson
    #undef rson
    #undef mid
}sgt;
int n,m,K,belong[N],cnt[N],ans[N],cnt_belong,ans2[N];
void CDQ(int l,int r)
{
    if(l==r) return;
    int mid=(l+r)/2;
    CDQ(l,mid);
    CDQ(mid+1,r);

    int ptl=l;
    for(int i=mid+1;i&lt;=r;i++)
    {
        for(;nd[ptl].y&lt;=nd[i].y and ptl&lt;=mid;ptl++)
            sgt.Add(nd[ptl].z,cnt[nd[ptl].no],1,0,K);
        ans[nd[i].no]+=sgt.Query(0,nd[i].z,1,0,K);
    }
    ptl=l;
    for(int i=mid+1;i&lt;=r;i++)
        for(;nd[ptl].y&lt;=nd[i].y and ptl&lt;=mid;ptl++)
            sgt.Add(nd[ptl].z,-cnt[nd[ptl].no],1,0,K);

    sort(nd+l,nd+r+1,cmp2);
}
int main()
{
    n=read(),K=read();
    for(int i=1;i&lt;=n;i++)
        nd[i].x=read(),nd[i].y=read(),nd[i].z=read(),nd[i].no=i;

    sort(nd+1,nd+1+n,cmp);
    for(int i=1;i&lt;=n;i++)
        if(nd[i]==nd[i-1])
            cnt[cnt_belong]++,belong[nd[i].no]=cnt_belong;
        else
            cnt[++cnt_belong]++,belong[nd[i].no]=cnt_belong;
    for(int i=1;i&lt;=cnt_belong;i++)
        ans[i]+=cnt[i]-1;
    m=unique(nd+1,nd+1+n)-nd-1;
    for(int i=1;i&lt;=m;i++)
        nd[i].no=i;

    CDQ(1,m);

    for(int i=1;i&lt;=n;i++)
        ans2[ans[belong[i]]]++;
    for(int i=0;i&lt;n;i++)
        printf("%d\n",ans2[i]);
    return 0;
}

</code></pre>
