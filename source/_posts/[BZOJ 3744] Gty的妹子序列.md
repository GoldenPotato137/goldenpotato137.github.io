---
title: "[BZOJ 3744] Gty的妹子序列"
date: 2019-03-20 14:41:38
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.lydsy.com/JudgeOnline/problem.php?id=3744" target="_blank"  rel="nofollow" >3744: Gty的妹子序列</a></p>
<hr />
<h1>Solution</h1>
<p>这是一道分块妙题。</p>
<p>区间逆序对......log数据结构应该是没法搞的。<br />
因此，我们考虑用分块解决这个问题。<br />
<strong>设$f[i][j]$表示第$i$块与第$j$块的所有元素的逆序对个数</strong><br />
这个东西我们可以考虑用线段树/树状数组直接搞，我们把所有数字从大到小插入，(数字相同的时候一起插入),每插入一种数字，我们可以直接计算它到其他所有块会新产生的逆序对数：即那个块的大小-已经填好的数字的个数。<br />
上面的东西可以$O(n\cdot \sqrt n \cdot logn)$预处理出来。</p>
<p>接下来，我们用<strong>$g[i][j]$表示从第$i$块开始，第$i$块与$i-j$块中所有元素的逆序对个数</strong>，这个可以利用$f$直接前缀和计算。</p>
<p>那么$l,r$中所有元素的逆序对个数即为里面整块的$\sum g[i][br]+$零散的值的逆序对个数。对于零散的值的个数，我们可以直接用主席树求即可。</p>
<p>时间复杂度$O(n\cdot \sqrt n \cdot logn)$,卡常。<br />
如果您BZOJ卡不过的话，可以来luogu<a href="https://www.luogu.org/problemnew/show/U66042" target="_blank"  rel="nofollow" >这道题试试</a></p>
<p>就酱，我们又切掉一道题啦ヾ(o´∀｀o)ﾉ</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//[BZOJ 3744] Gty的妹子序列
//Mar,20th,2019
//分块求区间逆序对数
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;cmath&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=50000+100;
const int Q=225+20;
struct SegmentTree
{
    #define lson (now&lt;&lt;1)
    #define rson (now&lt;&lt;1|1)
    #define mid ((now_l+now_r)&gt;&gt;1)
    int sum[N&lt;&lt;2];
    inline void update(int now)
    {
        sum[now]=sum[lson]+sum[rson];
    }
    void Add(int x,int num,int now,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            sum[now]+=num;
            return;
        }
        if(x&lt;=mid) Add(x,num,lson,now_l,mid);
        else Add(x,num,rson,mid+1,now_r);
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
struct PresidentTree
{
    #define mid ((now_l+now_r)&gt;&gt;1)
    static const int M=(50000&lt;&lt;2)*30;
    int sum[M],son[M][2],to;
    inline void update(int now)
    {
        sum[now]=sum[son[now][0]]+sum[son[now][1]];
    }
    void Add(int x,int now,int pre,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            sum[now]=sum[pre]+1;
            return;
        }
        if(x&lt;=mid)
        {
            Add(x,son[now][0]=++to,son[pre][0],now_l,mid);
            son[now][1]=son[pre][1];
        }
        else
        {
            Add(x,son[now][1]=++to,son[pre][1],mid+1,now_r);
            son[now][0]=son[pre][0];
        }
        update(now);
    }
    int Query(int l,int r,int now,int pre,int now_l,int now_r)
    {
        if(now_l&gt;=l and now_r&lt;=r) return sum[now]-sum[pre];
        int t_ans=0;
        if(l&lt;=mid)  t_ans+=Query(l,r,son[now][0],son[pre][0],now_l,mid);
        if(r&gt;mid)   t_ans+=Query(l,r,son[now][1],son[pre][1],mid+1,now_r);
        return t_ans;
    } 
    #undef mid
}prt;
struct NUM
{
    int w,no;
}num[N];
bool cmp(NUM x, NUM y)
{
    return x.w&gt;y.w;
}
bool cmp2(NUM x,NUM y)
{
    return x.no&lt;y.no;
}
int t_a[N],n,m,block,cnt[Q],msize[Q];
int f[Q][Q],g[Q][Q],root[N];
int main()
{
    int t=clock();
    freopen("3744.in","r",stdin);
    freopen("3744.out","w",stdout);

    n=read();
    for(int i=1;i&lt;=n;i++)
        num[i].w=t_a[i]=read(),num[i].no=i;

    sort(t_a+1,t_a+1+n);
    m=unique(t_a+1,t_a+1+n)-t_a-1;
    for(int i=1;i&lt;=n;i++)
        num[i].w=lower_bound(t_a+1,t_a+1+m,num[i].w)-t_a;

    sort(num+1,num+1+n,cmp);
    int size=int(sqrt(n));
    msize[0]=size-1,msize[n/size]=n%size+1;
    for(int i=1;i&lt;n/size;i++)
        msize[i]=size;
    block=n/size;
    for(int i=1;i&lt;=n;i++)
        sgt.Add(i,1,1,1,n);
    static int mqueue[N],tail;
    int to=0;
    while(to&lt;n)
    {
        tail=0;
        for(to++;to&lt;=n;to++)
        {
            sgt.Add(num[to].no,-1,1,1,n);
            cnt[num[to].no/size]++;
            mqueue[tail++]=to;
            if(num[to+1].w != num[to].w)
                break;
        }
        for(int t=0;t&lt;tail;t++)
        {
            int i=mqueue[t];
            for(int j=num[i].no/size+1;j&lt;=block;j++)
                f[j][num[i].no/size]+=msize[j]-cnt[j],
                f[num[i].no/size][j]+=msize[j]-cnt[j];
            f[num[i].no/size][num[i].no/size]+=sgt.Query(num[i].no,(num[i].no/size+1)*size-1,1,1,n);
        }
    }
    for(int i=0;i&lt;=block;i++)
    {
        g[i][i]=f[i][i];
        for(int j=i+1;j&lt;=block;j++)
            g[i][j]+=g[i][j-1]+f[i][j];
    }

    /*for(int i=0;i&lt;=block;i++)
    {
        for(int j=i;j&lt;=block;j++)
            cerr&lt;&lt;g[i][j]&lt;&lt;" ";
        cerr&lt;&lt;endl;
    }*/

    sort(num+1,num+1+n,cmp2);
    for(int i=1;i&lt;=n;i++)
    {
        root[i]=++prt.to;
        prt.Add(num[i].w,root[i],root[i-1],0,m);
    }
    int q=read(),ans=0;
    for(int i=1;i&lt;=q;i++)
    {
        int l=read()^ans,r=read()^ans;
        ans=0;
        for(int j=l;j&lt;min((l/size+1)*size,r+1);j++)//左散块
            ans+=prt.Query(0,num[j].w-1,root[r],root[j],0,m);
        if(l/size+1 &lt;= r/size-1)//中间块
            for(int j=l/size+1;j&lt;=r/size-1;j++)
                ans+=g[j][r/size-1];
        if(l/size!=r/size)//右散块
            for(int j=r/size*size;j&lt;=r;j++)
            {
                ans+=prt.Query(0,num[j].w-1,root[r],root[j],0,m);
                if(l/size+1 &lt;= r/size-1)
                    ans+=prt.Query(num[j].w+1,m,root[r/size*size-1],root[(l/size+1)*size-1],0,m);
            }
        printf("%d\n",ans);
    }

    cerr&lt;&lt;clock()-t;
    return 0;
}

</code></pre>
