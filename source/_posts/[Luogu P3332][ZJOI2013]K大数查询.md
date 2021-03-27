---
title: "[Luogu P3332][ZJOI2013]K大数查询"
date: 2019-03-27 07:55:12
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P3332" target="_blank"  rel="nofollow" >P3332 [ZJOI2013]K大数查询</a></p>
<hr />
<h1>Solution</h1>
<p>这是一道不辣么模板的整体二分题。</p>
<p>首先，我们先来假设一下只有一个询问应该怎么搞。<br />
考虑这样做：我们先二分一个答案，修改中，如果所要修改的数大于mid，则在这段区间中每个数加上1。否则什么都不做。这样一来，最后我们只需要看一下询问的区间的区间和是否大于$K$即可。</p>
<p>接下来，我们考虑如何把所有询问一起来二分。<br />
同样还是二分一个答案，把所有答案大于mid的询问丢到右边，其余丢到左边即可。<br />
接下来，我们可以显然发现对于右边的区间，<strong>所有修改$&lt;=mid$的修改都是没有意义的。因此，我们考虑把所有$&lt;=mid$的修改放在右边，其余的放在左边</strong>。显然，<strong>对于左边所有的询问，$>=mid$的修改一定会对左边造成影响，因此还要把所有在左边的询问减上对应的值</strong>（在这个询问之前所有的操作对它产生的1的数量）。</p>
<p>对于询问和修改的顺序问题：我们分别保证询问与修改按照时间有序，用两个指针分开处理，每次询问之前把在它之前的修改全部做了即可。这样就可以保证时间合法。</p>
<p>时间复杂度$O(nlog^2n)$<br />
就酱，我们又切掉一道题啦(ﾉ≧∀≦)ﾉ</p>
<hr />
<h1>Code</h1>
<p><strong>数据生成器</strong></p>
<pre><code class="language-cpp line-numbers">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstdlib&gt;
#include&lt;ctime&gt;
using namespace std;
const int N=500;
const int NUM=1000;
int cnt[N+5];
int main()
{
    srand(time(NULL));
    freopen("3332.in","w",stdout);

    int n=N,m=N;
    cout&lt;&lt;n&lt;&lt;" "&lt;&lt;m&lt;&lt;endl;
    for(int i=1;i&lt;=m;i++)
    {
        int op=rand()%2,a=rand()%n+1,b=rand()%n+1,c=rand()%NUM+1;
        if(a&gt;b) swap(a,b);
        if(op==1)
        {
            cout&lt;&lt;op&lt;&lt;" "&lt;&lt;a&lt;&lt;" "&lt;&lt;b&lt;&lt;" "&lt;&lt;c&lt;&lt;endl;
            for(int j=a;j&lt;=b;j++)
                cnt[j]++;
        }
        else
        {
            int mtot=0;
            for(int j=a;j&lt;=b;j++)
                mtot+=cnt[j];
            if(mtot==0)
            {
                i--;
                continue;
            }   
            c=rand()%mtot+1;
            cout&lt;&lt;"2"&lt;&lt;" "&lt;&lt;a&lt;&lt;" "&lt;&lt;b&lt;&lt;" "&lt;&lt;c&lt;&lt;endl;
        }
    }
    return 0;
}

</code></pre>
<p><strong>正解</strong></p>
<pre><code class="language-cpp line-numbers">//Luogu P3332 [ZJOI2013]K大数查询 
//Mar,27th,2019
//整体二分+线段树
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstring&gt;
#include&lt;vector&gt;
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
struct SegmentTree
{
    #define lson (now&lt;&lt;1)
    #define rson (now&lt;&lt;1|1)
    #define mid ((now_l+now_r)&gt;&gt;1)
    long long sum[N&lt;&lt;2];
    int lazy[N&lt;&lt;2];
    inline void update(int now)
    {
        sum[now]=sum[lson]+sum[rson];
    }
    inline void pushdown(int now,int now_l,int now_r)
    {
        if(now_l!=now_r)
        {
            sum[lson]+=lazy[now]*(mid-now_l+1),sum[rson]+=lazy[now]*(now_r-mid);
            lazy[lson]+=lazy[now],lazy[rson]+=lazy[now];
        }
        lazy[now]=0;
    }
    void Change(int l,int r,int w,int now,int now_l,int now_r)
    {
        pushdown(now,now_l,now_r);
        if(now_l&gt;=l and now_r&lt;=r)
        {
            lazy[now]=w,sum[now]+=1ll*w*(now_r-now_l+1);
            return;
        }
        if(l&lt;=mid) Change(l,r,w,lson,now_l,mid);
        if(r&gt;mid) Change(l,r,w,rson,mid+1,now_r);
        update(now);
    }
    long long Query(int l,int r,int now,int now_l,int now_r)
    {
        pushdown(now,now_l,now_r);
        if(now_l&gt;=l and now_r&lt;=r)
            return sum[now];
        long long t_ans=0;
        if(l&lt;=mid) t_ans+=Query(l,r,lson,now_l,mid);
        if(r&gt;mid) t_ans+=Query(l,r,rson,mid+1,now_r);
        return t_ans;
    }
    #undef lson
    #undef rson
    #undef mid
}sgt;
struct OP
{
    int l,r,no;
    long long w;
}op1[N],op2[N];//op1:询问，op2:修改
struct DL
{
    int l,r;
    vector &lt;OP&gt; op,qur;
}mqueue[2*N];
int n,m,q,p,K=2*N-20;//q:询问，p:修改
int ans[N];
int main()
{
    freopen("3332.in","r",stdin);
    freopen("3332.out","w",stdout);

    n=read(),m=read();
    for(int i=1;i&lt;=m;i++)
    {
        int op=read();
        if(op==1)
            op2[++p].l=read(),op2[p].r=read(),op2[p].w=read(),op2[p].no=i;
        else
            op1[++q].l=read(),op1[q].r=read(),op1[q].w=read(),op1[q].no=i;
    }

    int front=0,tail=0;
    mqueue[tail].l=-N,mqueue[tail].r=N;
    for(int i=1;i&lt;=p;i++)
        mqueue[tail].op.push_back(op2[i]);
    for(int i=1;i&lt;=q;i++)
        mqueue[tail].qur.push_back(op1[i]);
    tail++;
    memset(ans,0x3f,sizeof ans);

    while(front!=tail)
    {
        //cerr&lt;&lt;front&lt;&lt;" "&lt;&lt;tail&lt;&lt;" "&lt;&lt;mqueue[front].l&lt;&lt;" "&lt;&lt;mqueue[front].r&lt;&lt;endl;
        if(mqueue[front].l==mqueue[front].r)
        {
            for(int i=0;i&lt;int(mqueue[front].qur.size());i++)
                ans[mqueue[front].qur[i].no]=mqueue[front].l;
        }
        else if(mqueue[front].qur.size()&gt;0)
        {
            int mid=int(floor((mqueue[front].l+mqueue[front].r)/2.0)),T=0;
            DL L,R;
            for(int i=0;i&lt;int(mqueue[front].qur.size());i++)
            {
                for(;T&lt;int(mqueue[front].op.size()) and mqueue[front].qur[i].no&gt;mqueue[front].op[T].no;T++)
                {
                    sgt.Change(mqueue[front].op[T].l,mqueue[front].op[T].r,mqueue[front].op[T].w&gt;mid,1,1,n);
                    if(mqueue[front].op[T].w&gt;mid)
                        R.op.push_back(mqueue[front].op[T]);
                    else
                        L.op.push_back(mqueue[front].op[T]);
                }
                long long t=sgt.Query(mqueue[front].qur[i].l,mqueue[front].qur[i].r,1,1,n);
                if(t&gt;=mqueue[front].qur[i].w)
                    R.qur.push_back(mqueue[front].qur[i]);
                else
                {
                    mqueue[front].qur[i].w-=t;
                    L.qur.push_back(mqueue[front].qur[i]);
                }
            }
            for(int i=0;i&lt;T;i++)
                sgt.Change(mqueue[front].op[i].l,mqueue[front].op[i].r,-(mqueue[front].op[i].w&gt;mid),1,1,n);
            L.l=mqueue[front].l,L.r=mid;
            R.l=mid+1,R.r=mqueue[front].r;
            mqueue[tail]=L,tail=(tail+1)%K;
            mqueue[tail]=R,tail=(tail+1)%K;
        }
        front=(front+1)%K;
    }

    for(int i=1;i&lt;=m;i++)
        if(ans[i]!=0x3f3f3f3f)
            printf("%d\n",ans[i]);
    return 0;
}

</code></pre>
