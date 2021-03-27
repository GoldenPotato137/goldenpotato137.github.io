---
title: "[Luogu P2257] YY的GCD"
date: 2019-03-04 05:28:35
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：<a href="https://www.luogu.org/problemnew/show/P2257" target="_blank"  rel="nofollow" >洛咕</a></p>
<hr />
<h1>Solution</h1>
<p><del>推到自闭，我好菜啊</del></p>
<p>显然，这题让我们求：<br />
$\large \sum_{i=1}^{n}\sum_{j=1}^{m}[gcd(i,j)\in prime]$</p>
<p>根据套路，我们可以把判断是否为质数改为枚举这个质数，有：<br />
为了方便枚举，我们在这里假设有$m>n$<br />
$\large \sum_{i=1}^{n}\sum_{j=1}^{m}\sum_{k\in prime}^{n}[gcd(i,j)= k]$<br />
显然，要让$gcd(i,j)=k$，必须要有$i,j$均为$k$的倍数，因此有：<br />
$\large \sum_{k\in prime}^{n}\sum_{i=1}^{n/k}\sum_{j=1}^{m/k}[gcd(i,j)= 1]$ (在这里除号指向下取整)</p>
<p>根据套路，我们要去掉这里的判断符号。因为我们的莫比乌斯函数有这个性质：$[x=1]=\sum_{d|x}\mu(d)$，我们这里可以直接把$gcd(i,j)$作为$x$带入这个性质里面，有：<br />
$\large \sum_{k\in prime}^{n}\sum_{i=1}^{n/k}\sum_{j=1}^{m/k}\sum_{d|gcd(i,j)}\mu(d)$</p>
<p>然后根据套路，我们直接枚举这里的$d$，有：<br />
$\large \sum_{k\in prime}^{n}\sum_{i=1}^{n/k}\sum_{j=1}^{m/k}\sum_{d=1}^{n/k}μ(d)[d|gcd(i,j)]$ （因为前面$i,j$中最小的是$n/k$,所以说我们这里$d$的最大值也为$n/k$）<br />
然后我们这里的$\sum_{d=1}^{n/k}$显然可以直接往前提<br />
$\large \sum_{k\in prime}^{n}\sum_{d=1}^{n/k}\sum_{i=1}^{n/k}\sum_{j=1}^{m/k}μ(d)[d|gcd(i,j)]$<br />
这时候$\mu(d)$显然也可以往前提<br />
$\large \sum_{k\in prime}^{n}\sum_{d=1}^{n/k}μ(d)\sum_{i=1}^{n/k}\sum_{j=1}^{m/k}[d|gcd(i,j)]$</p>
<p>这时候，我们可以发现后面那个判断式为1当且仅当$i,j$均为$d$的倍数，所以我们可以直接把那两个$\sum$简化掉<br />
$\large \sum_{k\in prime}^{n}\sum_{d=1}^{n/k}μ(d)\frac{n}{k&#42;d}\frac{m}{k&#42;d}$</p>
<p>这时候，我们已经可以在$O(logn&#42;\sqrt n)$的时间内算一次答案了（这里的$log$为质数个数），很可惜，这样的复杂度并不能通过这一题。</p>
<p>事实上，我们还有一个常见的套路来优化这里：<br />
我们可以设$T=k&#42;d$，于是我们有：<br />
$\large \sum_{k\in prime}^{n}\sum_{d=1}^{n/k}μ(\frac{T}{k})\frac{n}{T}\frac{m}{T}$<br />
然后可以把后面那个和式提前，枚举T，有：<br />
$\large \sum_{T=1}^{n}\frac{n}{T}\frac{m}{T}\sum_{(k\in prime,k|T)}μ(\frac{T}{k})$</p>
<p>搞定，到这里为止，我们一切东西都可以算了。<br />
前面的$\frac{n}{T}\frac{m}{T}$可以整除分块来搞，后面那个$μ$可以在$O(n)$的时间预处理，然后算的时候前缀和一搞就ok啦。<br />
如何预处理呢？我们可以考虑这样做：我们先枚举每一个质数$x$，再考虑这个$x$对它的整数倍$t$的贡献为$\mu(t)$</p>
<p>酱紫，我们就可以在$O(\sqrt n)$的时间内处理每一个询问了。<br />
完结撒花✿✿ヽ(°▽°)ノ✿</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//Luogu P2257 YY的GCD
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
const int N=10000000+1000;
const int M=10000000;
int mu[N],prime[N],cnt_p;
bool noPrime[N];
void GetPrime(int n)
{
    mu[1]=1;
    noPrime[1]=true;
    for(int i=2;i&lt;=n;i++)
    {
        if(noPrime[i]==false)
            prime[++cnt_p]=i,mu[i]=-1;
        for(int j=1;j&lt;=cnt_p and i\*prime[j]&lt;=n;j++)
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
long long f[N],pre_f[N];
int main()
{
    int t=clock();
    GetPrime(M);
    for(int i=1;i&lt;=cnt_p;i++)
        for(int j=1;prime[i]\*j&lt;=M;j++)
            f[prime[i]\*j]+=mu[j];
    for(int i=1;i&lt;=M;i++)
        pre_f[i]=pre_f[i-1]+f[i];

    int T=read();
    for(;T&gt;0;T--)
    {
        long long n=read(),m=read();
        if(n&gt;m) swap(n,m);

        int l=1,r=1;
        long long ans=0;
        for(;l&lt;=n;l=r+1)
        {
            r=min(n/(n/l),m/(m/l));
            ans+=(pre_f[r]-pre_f[l-1])\*(n/l)\*(m/l);
        }

        printf("%lld\n",ans);
    }
    cerr&lt;&lt;clock()-t;
    return 0;
}

</code></pre>
