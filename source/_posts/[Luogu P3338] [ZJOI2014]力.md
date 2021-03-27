---
title: "[Luogu P3338] [ZJOI2014]力"
date: 2019-03-01 06:54:13
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：</p>
<ul>
<li><a href="https://www.luogu.org/problemnew/show/P3338" target="_blank"  rel="nofollow" >洛咕</a></li>
<li><a href="https://www.lydsy.com/JudgeOnline/problem.php?id=3527" target="_blank"  rel="nofollow" >BZOJ</a></li>
</ul>
<hr />
<h1>Solution</h1>
<p><del>写到脑壳疼，我好菜啊</del></p>
<p>我们来颓柿子吧<br />
$F_j=\sum_{i&lt;j}\frac{q_i&#42;q_j}{(i-j)^2}-\sum_{i>j}\frac{q_i&#42;q_j}{(i-j)^2}$</p>
<p>$q_j$与$i$没有半毛钱关系，提到外面去<br />
$F_j=q_j&#42;\sum_{i&lt;j}\frac{q_i}{(i-j)^2}-q_j&#42;\sum_{i>j}\frac{q_i}{(i-j)^2}$</p>
<p>左右同时除以$q_j$<br />
$E_j=\sum_{i=1}^{j-1}\frac{q_i}{(i-j)^2}-\sum_{i=j+1}^{n}\frac{q_i}{(i-j)^2}$</p>
<p>我们设$f(i)=q(i),g(i)=\frac{1}{i^2}$，有<br />
$E_j=\sum_{i=1}^{j-1}f(i)&#42;g(i-j)-\sum_{i=j+1}^{n}f(i)&#42;g(i-j)$</p>
<p>因为$g(i)$是个偶函数，因此有：<br />
$E_j=\sum_{i=1}^{j-1}f(i)&#42;g(j-i)-\sum_{i=j+1}^{n}f(i)&#42;g(i-j)$</p>
<p>这时候，我们显然可以发现左边那个式子是个卷积，右边的这样一波化简就也变成了卷积形式：<br />
<img src="https://img2018.cnblogs.com/blog/1316999/201901/1316999-20190118160422690-423540635.jpg" alt="" /></p>
<p>卷积用FFT快速计算即可</p>
<p><strong>时间复杂度$O(nlogn)$</strong></p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp line-numbers">//Luogu P3338 [ZJOI2014]力
//Jan,18th,2019
//FFT加速卷积
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cmath&gt;
#include&lt;algorithm&gt;
#include&lt;complex&gt;
using namespace std;
typedef complex &lt;double&gt; cp;
const double PI=acos(-1);
const int M=100000+100;
const int N=8\*M;
inline cp omega(int K,int n)
{
    return cp(cos(2\*PI\*K/n),sin(2\*PI\*K/n));
}
void FFT(cp a[],int n,bool type)
{
    static int len=0,num=n-1,t[N];
    while(num!=0) len++,num/=2;
    for(int i=0,j;i&lt;=n;i++)
    {
        for(j=0,num=i;j&lt;len;j++)
            t[j]=num%2,num/=2;
        reverse(t,t+len);
        for(j=0,num=0;j&lt;len;j++)
            num+=t[j]\*(1&lt;&lt;j);
        if(i&lt;num) swap(a[i],a[num]);
    }
    for(int l=2;l&lt;=n;l\*=2)
    {
        int m=l/2;
        cp x0=omega(1,l);
        if(type==true) x0=conj(x0);
        for(int j=0;j&lt;n;j+=l)
        {
            cp x=cp(1,0);
            for(int k=0;k&lt;m;k++,x\*=x0)
            {
                cp temp=x\*a[j+k+m];
                a[j+k+m]=a[j+k]-temp;
                a[j+k]=a[j+k]+temp;
            }
        }
    }
}
int n,m;
double q[N];
cp f[N],g[N],f2[N];
int main()
{
    scanf("%d",&amp;n);
    for(int i=1;i&lt;=n;i++)
        scanf("%lf",&amp;q[i]);

    for(int i=1;i&lt;=n;i++)
        g[i]=(1.0/i/i);
    m=1;
    while(m&lt;2\*n) m\*=2;
    for(int i=1;i&lt;m;i++)
        f[i]=q[i],f2[i]=q[i];

    FFT(g,m,false);
    FFT(f,m,false);
    reverse(f2+1,f2+n+1);
    FFT(f2,m,false);
    for(int i=0;i&lt;m;i++)
        f[i]\*=g[i],f2[i]\*=g[i];
    FFT(f,m,true);
    FFT(f2,m,true);

    for(int i=1;i&lt;=n;i++)
        printf("%lf\n",(f[i].real()-f2[n-i+1].real())/m);
    return 0;
}

</code></pre>
