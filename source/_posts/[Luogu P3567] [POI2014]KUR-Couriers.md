---
title: "[Luogu P3567] [POI2014]KUR-Couriers"
date: 2019-03-07 03:50:57
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P3567" target="_blank"  rel="nofollow" >洛谷P3567</a></p>
<hr />
<h1>Solution</h1>
<p><del>大水题啊，真没什么好讲的</del></p>
<p>我们考虑建一颗权值主席树，从左往右逐个插入。因为个数满足可减性，因此我们可以很方便的“扣”出$[L,R]$区间构成的主席树。接下来只需要在树上二分看一下有没有出现次数超过$K$的即可。</p>
<p>时间复杂度$O(nlogn)$<br />
就酱，这题就被我们切掉啦︿(￣︶￣)︿</p>
<hr />
<h1>Solution</h1>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
using namespace std;
const int N=500000+100;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
struct SegmentTree
{
    #define mid ((now_l+now_r)&gt;&gt;1)
    static const int M=25*N;
    int size[M],son[M][2],to;
    inline void update(int now)
    {
        size[now]=size[son[now][0]]+size[son[now][1]];
    }
    void Add(int now,int pre,int x,int now_l,int now_r)
    {
        if(now_l==now_r)
        {
            size[now]=size[pre]+1;
            return;
        }
        if(x&lt;=mid)
        {
            son[now][1]=son[pre][1];
            Add(son[now][0]=++to,son[pre][0],x,now_l,mid);
        }
        else
        {
            son[now][0]=son[pre][0];
            Add(son[now][1]=++to,son[pre][1],x,mid+1,now_r);
        }
        update(now);
    }
    int Query(int now,int pre,int x,int now_l,int now_r)
    {
        if(now_l==now_r)
            return now_l;
        if(size[son[now][0]]-size[son[pre][0]]&gt;=x)
            return Query(son[now][0],son[pre][0],x,now_l,mid);
        else if(size[son[now][1]]-size[son[pre][1]]&gt;=x)
            return Query(son[now][1],son[pre][1],x,mid+1,now_r);
        return 0;
    }
    #undef mid
}sgt;
int root[N],n,m;
int main()
{
    n=read(),m=read();
    for(int i=1;i&lt;=n;i++)
    {
        int tmp=read();
        sgt.Add(root[i]=++sgt.to,root[i-1],tmp,1,n);
    }

    for(int i=1;i&lt;=m;i++)
    {
        int l=read(),r=read(),mid=(r-l+1)/2+1;
        printf("%d\n",sgt.Query(root[r],root[l-1],mid,1,n));
    }
    return 0;
}

</code></pre>
