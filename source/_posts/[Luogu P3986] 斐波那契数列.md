---
title: "[Luogu P3986] 斐波那契数列"
date: 2019-02-22 04:31:00
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：<a href="ttps://www.luogu.org/problemnew/show/P3986" target="_blank"  rel="nofollow" >洛谷</a></p>
<hr />
<h1>Solution</h1>
<p>这是一道很有意思的数论题。<br />
首先，我们可以发现直接枚举a和b会T的起飞。</p>
<p>接下来，我们就可以观察一下式子了，我们略微手算一下，就会有这样的结果：<br />
<img src="https://s2.ax1x.com/2019/02/22/kWLV1A.png" alt="kWLV1A.png" /></p>
<p>我们可以发现，a，b在每一项中的数量都可以用同一个斐波那契数列表示。<br />
我们可以用$g[x]$表示斐波那契数列的第x项，那么，我们可以得到$f[x]=a<em>g[x-1]+b</em>g[x]$</p>
<p>接下来，由常识可以知道，斐波那契数列的第40项就差不多有10^9那么大了。<br />
所以说，我们可以考虑枚举当前项x，问题就变为了有多少个$a，b$使得 $K=a<em>g[x-1]+b</em>g[x]$<br />
移项得：$b=(K-g[x-1]*a)/g[x]$</p>
<p>因为$a，b$都是整数，问题就变为了有多少个$a$，使得$K-g[x-1]*a$能被$g[x]$整除<br />
即：<br />
<a href="https://imgchr.com/i/kWLZ6I" target="_blank"  rel="nofollow" ><img src="https://s2.ax1x.com/2019/02/22/kWLZ6I.png" alt="kWLZ6I.png" /></a></p>
<p>对于斐波那契数列，有一个定理，就是$f[x]$与$f[x-1]$互质（证明略复杂，在这里就不给出了），这样就保证了同余方程有解。<br />
同时，我们还有一个限制，就是$ K-g[x-1]*a > 0 $(因为b>0)即$ a&lt;K/g[x-1] $的</p>
<p>由这两个式子，我们就可以求出对于每一个$x$，有多少个$a，b$可以使得$K=a<em>g[x-1]+b</em>g[x]$</p>
<p>.</p>
<p>酱紫，我们就可以AC这道题(≧∀≦)♪</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
using namespace std;
const int N=45;
const int n=40+2;
const int poi=1000000007;
long long f[N],K,ans;
long long exgcd(long long A,long long B,long long &amp;x,long long &amp;y)
{
    if(B==0)
    {
        x=1,y=0;
        return A;
    }
    long long temp=exgcd(B,A%B,x,y),tx=x;
    x=y,y=tx-(A/B)*y;
    return temp;
}
long long inv(long long A,long long POI)
{
    long long t,tt;
    exgcd(A,POI,t,tt);
    return (t%POI+POI)%POI;
}
int main()
{
    scanf("%lld",&amp;K); 

    f[1]=f[2]=1;
    for(int i=3;i&lt;=n;i++)
        f[i]=f[i-1]+f[i-2];
    for(int i=2;i&lt;=n;i++)
    {
        long long a=(K*inv(f[i-1],f[i]))%f[i],to=K/f[i-1]-1;
        if(a&lt;to)
        {
            if(a==0) ans--;
            ans=(ans+1+(to-a)/f[i])%poi;
        }
    }

    printf("%lld",ans);
    return 0;
}
</code></pre>
