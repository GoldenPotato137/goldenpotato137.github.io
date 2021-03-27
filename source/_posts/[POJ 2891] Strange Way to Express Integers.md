---
title: "[POJ 2891] Strange Way to Express Integers"
date: 2019-02-25 11:24:58
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：<a href="http://poj.org/problem?id=2891" target="_blank"  rel="nofollow" >POJ</a></p>
<hr />
<h1>Solution</h1>
<p>就是裸的扩展中国剩余定理嘛qwq</p>
<p>注意几点：一定要时时刻刻<del>去膜</del>取模，否则一定会爆long long，尤其是算出来的$k_0$</p>
<p>这里给出几组易锅数据：(第三组容易爆long long)</p>
<h3>input:</h3>
<pre><code class="">4
18373 16147
8614 14948
8440 17480
22751 21618
6
19576 8109
18992 24177
9667 17726
16743 19533
16358 12524
8280 22731
4
9397 38490
22001 25094
33771 38852
19405 35943
</code></pre>
<h3>output</h3>
<pre><code class="">13052907343337200
-1
78383942913636233
</code></pre>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">//POJ2891 Strange Way to Express Integers
//Jan,14th,2019
//扩展中国剩余定理
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
long long exgcd(long long A,long long B,long long &amp;x,long long &amp;y)
{
    if(B==0) 
    {
        x=1,y=0;
        return A;
    }
    long long t=exgcd(B,A%B,x,y),tx=x;
    x=y;
    y=tx-(A/B)*y;
    return t;
}
long long lcm(long long A,long long B)
{
    long long tx,ty;
    return (A*B)/exgcd(A,B,tx,ty);
}
const int N=100000+1000;
long long r[N],p[N];
int n;
int main()
{
    while(scanf("%d",&amp;n)!=EOF)
    {
        for(int i=1;i&lt;=n;i++)
            p[i]=read(),r[i]=read();

        long long R=r[1],P=p[1];
        bool OK=true;
        for(int i=2;i&lt;=n;i++)
        {
            long long x,y,gcd=exgcd(P,p[i],x,y);
            if((r[i]-R)%gcd!=0)
            {
                OK=false;
                break;
            }
            long long t_P=P,t=p[i]/gcd;
            x*=(r[i]-R)/gcd,P=lcm(P,p[i]),x=(x%t+t)%t;
            R=((t_P*x)%P+R)%P;
        }

        if(OK==true)
            printf("%lld\n",(R%P+P)%P);
        else
            printf("-1\n");
    }
    return 0;
}

</code></pre>
