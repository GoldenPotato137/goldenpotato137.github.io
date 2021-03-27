---
title: "[Luogu P1501] [国家集训队]Tree II"
date: 2019-03-11 04:18:23
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P1501" target="_blank"  rel="nofollow" >洛谷 P1501</a></p>
<hr />
<h1>Solution</h1>
<p>这是一道肥肠考验LCT基本功的一道题。</p>
<p>口胡起来是很容易的：<strong>对于每一个加/乘操作，我们把对应的链split出来，然后打标记即可；Link/Cut是基本操作；查询的话我们也是把对应的链split出来，然后直接输出根的sum即可</strong>。<br />
这里的打标记和线段树II那道题非常像，<del>不会的同学可以先去做线段树II</del>。 <del>都在写LCT了，怎么可能没打过线段树II</del>。我们只需要像线段树II那样pushdown并处理sum即可。</p>
<p><strong>口胡是口胡，打起来就是另外一码事了</strong><br />
这里列出我写出的锅，大家写的时候可以考虑注意以下几点：<br />
1. $(5w*5w)$会爆int的，请开longlong 。<br />
2. 我们在$pushdown$算各种标记、sum的时候一定要注意膜$p$，小心爆longlong。<br />
3. LCT的rotate和正常的splay的rotate不一样，我们要特判一下z是否是另外一颗splay的，不能直接无脑rotate。<del>当然，这种锅也只有我这种蒟蒻才会错</del><br />
4. 先cut再link，避免连出环来。<br />
5. <del>没了</del></p>
<hr />
<h1>Code</h1>
<h3>数据生成器</h3>
<p><strong>不包含link,cut操作</strong></p>
<pre><code class="language-cpp line-numbers">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;ctime&gt;
#include&lt;cstdlib&gt;
using namespace std;
const int N=10;
const int MAX=50000;
int main()
{
    srand(time(NULL));
    freopen("1501.in","w",stdout);

    int n=N,m=10*N;
    cout&lt;&lt;n&lt;&lt;" "&lt;&lt;m&lt;&lt;endl;
    for(int i=2;i&lt;=n;i++)
    {
        int to=rand()%i;
        if(to==0) to=1;
        cout&lt;&lt;i&lt;&lt;" "&lt;&lt;to&lt;&lt;endl;
    }

    for(int i=1;i&lt;=m;i++)
    {
        int op=rand()%3;
        if(op==0)
            cout&lt;&lt;"+ "&lt;&lt;rand()%n+1&lt;&lt;" "&lt;&lt;rand()%n+1&lt;&lt;" "&lt;&lt;rand()%MAX+1&lt;&lt;endl;
        else if(op==1)
            cout&lt;&lt;"* "&lt;&lt;rand()%n+1&lt;&lt;" "&lt;&lt;rand()%n+1&lt;&lt;" "&lt;&lt;rand()%MAX+1&lt;&lt;endl;
        else
            cout&lt;&lt;"/ "&lt;&lt;rand()%n+1&lt;&lt;" "&lt;&lt;rand()%n+1&lt;&lt;endl;
    }
    return 0;
}
</code></pre>
<h3>正解</h3>
<pre><code class="language-cpp line-numbers">//Luogu P1501 [国家集训队]Tree II 
//Mar,10th,2019
//LCT+线段树II
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
using namespace std;
const int N=100000+1000;
const long long poi=51061;
struct LCT
{
    long long son[N][2],fa[N],w[N],sum[N],plus[N],take[N],lazy[N],size[N];
    inline void Mirror(int x)
    {
        lazy[x]=!lazy[x];
        swap(son[x][0],son[x][1]);
    }
    inline void pushdown(int x)
    {
        if(lazy[x]==true)
        {
            Mirror(son[x][0]);
            Mirror(son[x][1]);
            lazy[x]=false;
        }
        if(son[x][0]!=0)
        {
            w[son[x][0]]=(w[son[x][0]]*take[x]+plus[x])%poi;
            take[son[x][0]]=take[son[x][0]]*take[x]%poi;
            plus[son[x][0]]=(plus[son[x][0]]*take[x]+plus[x])%poi;
            sum[son[x][0]]=(sum[son[x][0]]*take[x]+size[son[x][0]]*plus[x])%poi;
        }
        if(son[x][1]!=0)
        {
            w[son[x][1]]=(w[son[x][1]]*take[x]+plus[x])%poi;
            take[son[x][1]]=(take[son[x][1]]*take[x])%poi;
            plus[son[x][1]]=(plus[son[x][1]]*take[x]+plus[x])%poi;
            sum[son[x][1]]=(sum[son[x][1]]*take[x]+size[son[x][1]]*plus[x])%poi;
        }
        plus[x]=0,take[x]=1;
    }
    inline void update(int x)
    {
        size[0]=sum[0]=0;
        sum[x]=(sum[son[x][0]]+sum[son[x][1]]+w[x])%poi;
        size[x]=(size[son[x][0]]+size[son[x][1]]+1);
    }
    inline void rotate(int x,int type)
    {
        int y=fa[x],z=fa[y];
        fa[x]=z;
        if(IsRoot(y)==false)
            son[z][y==son[z][1]]=x;
        fa[son[x][type]]=y,son[y][!type]=son[x][type];
        fa[y]=x,son[x][type]=y;
        update(y),update(x);
    }
    bool IsRoot(int x)
    {
        return (x!=son[fa[x]][0] and x!=son[fa[x]][1]);
    }
    int mstack[N],top;
    void splay(int x)
    {
        mstack[top=1]=x;
        for(int now=x;IsRoot(now)==false;now=fa[now])
            mstack[++top]=fa[now];
        for(;top&gt;0;top--)
            pushdown(mstack[top]);
        while(IsRoot(x)==false)
        {
            if(IsRoot(fa[x])==false and x==son[fa[x]][fa[x]==son[fa[fa[x]]][1]])
                rotate(fa[x],x==son[fa[x]][0]),
                rotate(x,x==son[fa[x]][0]);
            else
                rotate(x,x==son[fa[x]][0]);
        }
    }
    void Access(int x)
    {
        for(int t=0;x!=0;t=x,x=fa[x])
            splay(x),son[x][1]=t,fa[t]=x,update(x);
    }
    int GetRoot(int x)
    {
        Access(x),splay(x);
        while(son[x][0]!=0)
            pushdown(x),x=son[x][0];
        splay(x);
        return x;
    }
    void MakeRoot(int x)
    {
        Access(x);
        splay(x);
        Mirror(x);
    }
    void Link(int x,int y)
    {
        if(GetRoot(x)==GetRoot(y)) return;
        MakeRoot(x);
        fa[x]=y;
    }
    void Split(int x,int y)
    {
        MakeRoot(y);
        Access(x),splay(x);
    }
    void Cut(int x,int y)
    {
        Split(x,y);
        if(y!=son[x][0] or fa[y]!=x) return;
        son[x][0]=0,fa[y]=0;
        update(x);
    }
    void Add(int x,int y,long long num)
    {
        Split(x,y);
        plus[x]=num%poi,w[x]=(w[x]+num)%poi,sum[x]=(sum[x]+size[x]*num)%poi;
    }
    void Take(int x,int y,long long num)
    {
        Split(x,y);
        take[x]=num%poi,w[x]=(w[x]*num)%poi,sum[x]=sum[x]*num%poi;
    }
    long long Query(int x,int y)
    {
        Split(x,y);
        return sum[x]%poi;
    }
}lct;
int n,q;
int main()
{
    n=read(),q=read();
    for(int i=1;i&lt;=n;i++)
        lct.take[i]=lct.w[i]=lct.sum[i]=lct.size[i]=1;
    for(int i=1;i&lt;n;i++)
    {
        int s=read(),t=read();
        lct.Link(s,t);
    }

    char OP[5];
    for(int i=1;i&lt;=q;i++)
    {
        scanf("%s",OP+1);
        if(OP[1]=='+')
        {
            long long u=read(),v=read(),x=read()%poi;
            lct.Add(u,v,x);
        }
        else if(OP[1]=='-')
        {
            long long u1=read(),v1=read(),u2=read(),v2=read();
            lct.Cut(u1,v1);
            lct.Link(u2,v2);
        }
        else if(OP[1]=='*')
        {
            long long u=read(),v=read(),x=read()%poi;
            lct.Take(u,v,x);
        }
        else
        {
            long long u=read(),v=read();
            printf("%lld\n",lct.Query(u,v));
        }
    }
    return 0;
}

</code></pre>
