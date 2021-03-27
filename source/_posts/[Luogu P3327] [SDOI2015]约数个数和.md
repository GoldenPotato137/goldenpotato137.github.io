---
title: "[Luogu P3327] [SDOI2015]约数个数和"
date: 2019-03-05 06:57:16
tags: "迁移"
---
<h1>题面：</h1>
<p>传送门：<a href="https://www.luogu.org/problemnew/show/P3327" target="_blank"  rel="nofollow" >洛咕</a></p>
<hr />
<h1>Solution</h1>
<p>首先，我们需要一个结论：<br />
$\large d(i,j)=\sum_{x|i}\sum_{y|j}[gcd(x,y)=1]$</p>
<blockquote><p>
  证明<br />
  理性证明请看<a href="https://www.luogu.org/blog/An-Amazing-Blog/mu-bi-wu-si-fan-yan-ji-ge-ji-miao-di-dong-xi" target="_blank"  rel="nofollow" >这篇博客</a>的例五<br />
  本蒟蒻提供一个感性证明的方法：如果$x&#42;y$是$i&#42;j$的因数，我们必须有$x|i,y|j$，而后面那个$gcd(x,y)$是用来去重的
</p></blockquote>
<p>有了这个柿子之后，我们之后的推导就比较套路了：<br />
<strong>为了方便讨论，之后的柿子均默认$m>n$</strong><br />
<strong>为了方便书写，之后的除法默认向下取整</strong><br />
原式：<br />
$\large \sum_{i=1}^n\sum_{j=1}^md(i&#42;j)$</p>
<p>把我们上面的结论代进去<br />
$\large \sum_{i=1}^n\sum_{j=1}^m\sum_{x|i}\sum_{y|j}[gcd(x,y)=1]$</p>
<p><del>根据套路</del>，这里的$x|i$与$y|j$应该写成枚举的形式：<br />
$\large \sum_{i=1}^n\sum_{j=1}^m\sum_{x=1}^{n}[x|i]\sum_{y=1}^{m}[y|j][gcd(x,y)=1]$</p>
<p>这里显然可以把$x,y$的和式写到最前面去：<br />
$\large \sum_{x=1}^{n}[x|i]\sum_{y=1}^{m}[y|j]\sum_{i=1}^n\sum_{j=1}^m[gcd(x,y)=1]$<br />
然后就可以去掉$x,y$后面的那两个判断式啦<br />
$\large \sum_{x=1}^{n}\sum_{y=1}^{m}\sum_{i=1}^{n/x}\sum_{j=1}^{m/y}[gcd(x,y)=1]$</p>
<p>然后我们再去套路后面那个$gcd$，根据莫比乌斯函数的性质$[x=1]=\sum_{d|x}\mu(d)$，我们就把$gcd$带入得<br />
$\large \sum_{x=1}^{n}\sum_{y=1}^{m}\sum_{i=1}^{n/x}\sum_{j=1}^{m/y}\sum_{d|gcd(x,y)}\mu(d)$<br />
然后去枚举$d$<br />
$\large \sum_{x=1}^{n}\sum_{y=1}^{m}\sum_{i=1}^{n/x}\sum_{j=1}^{m/y}\sum_{d=1}^{n}\mu(d)[d|gcd(x,y)]$<br />
套路地把$\mu$的和式丢到最前面，化简一下就有：<br />
$\large \sum_{d=1}^{n}\mu(d)\sum_{x=1}^{n/d}\sum_{y=1}^{m/d}\sum_{i=1}^{n/(x&#42;d)}\sum_{j=1}^{m/(y&#42;d)}1$<br />
然后有<br />
$\large \sum_{d=1}^{n}\mu(d)\sum_{x=1}^{n/d}\sum_{y=1}^{m/d}\frac{n}{x&#42;d}\frac{m}{y&#42;d}$</p>
<p>移项整理一下：<br />
$\large \sum_{d=1}^{n}\mu(d)\sum_{x=1}^{n/d}\frac{n}{x&#42;d}\sum_{y=1}^{m/d}\frac{m}{y&#42;d}$</p>
<p><del>好了，到这里我就不会推了</del><br />
<img src="https://s2.ax1x.com/2019/01/23/kAl7Ct.jpg" alt="kAl7Ct.jpg" /></p>
<p>之后的内容感谢@Maxwei_wzj的教学<br />
事实上，这个柿子我们已经可以算了。<br />
$\large \sum_{d=1}^{n}\mu(d)\sum_{x=1}^{n/d}\frac{n}{x&#42;d}\sum_{y=1}^{m/d}\frac{m}{y&#42;d}$<br />
首先，我们有一个结论<del>(我不会证)</del><br />
$\large x/(y&#42;z)=(x/y)/z$ (这里的除法<strong>向下取整</strong>)</p>
<p>我们的柿子就可以变为<br />
$\large \sum_{d=1}^{n}\mu(d)\sum_{x=1}^{n/d}\frac{\frac{n}{d}}{x}\sum_{y=1}^{m/d}\frac{\frac{m}{d}}{y}$</p>
<p>然后我们再设一个方程:<br />
$f(x)=\sum_{i=1}^x\frac{x}{i}$<br />
我们柿子就可以写为：<br />
$\large \sum_{d=1}^{n}\mu(d)f(\frac{n}{d})f(\frac{m}{d})$</p>
<p>诶？我们好像又能整除分块了。<br />
没错，我们只需要先用$O(n&#42;\sqrt n)$的整除分块预处理出$f$<br />
然后再每次$O(\sqrt n)$整除分块算出那个柿子就好。</p>
<p>时间复杂度$O(n&#42;\sqrt n)$<br />
完结撒花✿✿ヽ(°▽°)ノ✿</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">//Luogu  P3327 [SDOI2015]约数个数和
//Jan,22ed,2019
//莫比乌斯反演
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x\*10+c-'0';c=getchar();}
    return x\*f;
}
const int N=50000+1000;
const int M=50000;
int prime[N],p_cnt,mu[N];
bool noPrime[N];
void GetPrime(int n)
{
    noPrime[1]=true,mu[1]=1;
    for(int i=2;i&lt;=n;i++)
    {
        if(noPrime[i]==false)
            prime[++p_cnt]=i,mu[i]=-1;
        for(int j=1;j&lt;=p_cnt and i\*prime[j]&lt;=n;j++)
        {
            noPrime[i\*prime[j]]=true;
            if(i%prime[j]==0)
            {
                mu[i\*prime[j]]=0;
                break;
            }
            mu[i\*prime[j]]=mu[i]\*mu[prime[j]];
        }
    }
}
long long f[N],pre_mu[N];
int main()
{
    GetPrime(M);
    for(int i=1;i&lt;=M;i++)
        for(int l=1,r;l&lt;=i;l=r+1)
        {
            r=i/(i/l);
            f[i]+=(r-l+1)\*(i/l);
        }
    for(int i=1;i&lt;=M;i++)
        pre_mu[i]=pre_mu[i-1]+mu[i];

    int T=read();
    for(;T&gt;0;T--)
    {
        long long n=read(),m=read();
        if(n&gt;m) swap(n,m);

        long long ans=0;
        for(int l=1,r;l&lt;=n;l=r+1)
        {
            r=min(n/(n/l),m/(m/l));
            ans+=(pre_mu[r]-pre_mu[l-1])\*f[n/l]\*f[m/l];
        }

        printf("%lld\n",ans);
    }
    return 0;
}

</code></pre>
