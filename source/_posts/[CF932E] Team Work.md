---
title: "[CF932E] Team Work"
date: 2019-02-25 11:29:02
tags: "迁移"
---
<h1>题面</h1>
<ul>
<li><a href="https://www.luogu.org/problemnew/show/CF932E" target="_blank"  rel="nofollow" >洛咕</a></li>
<li><a href="http://codeforces.com/problemset/problem/932/E" target="_blank"  rel="nofollow" >CodeForces</a></li>
</ul>
<hr />
<h1>Solution</h1>
<p><del>这题写得我脑壳疼，我好菜啊</del></p>
<p>.</p>
<p>显然，这题让我们求$\sum_{i=1}^{n}C_n^i\times i^k$</p>
<p>这个$i^k$让人浑身难受，我们可以考虑把它搞掉，能搞掉某个数的幂次方的有啥？本蒟蒻只会第二类斯特林数。</p>
<p>.</p>
<p>所以说我们无脑把第二类斯特林数带进去再说：</p>
<p>$\sum_{i=1}^{n}C_n^i\times \sum_{j=0}^{i}S(k,j)&#42;j!&#42;C_i^j$</p>
<p>.</p>
<p>然后把组合数展开：</p>
<p>$\sum_{i=1}^{n}\frac{n!}{i!(n-i)!}\times \sum_{j=0}^{i}S(k,j)&#42;j!&#42;\frac{i!}{j!(i-j)!}$</p>
<p>.</p>
<p>因为前面那一项与j无关，我们可以把它放到后面去</p>
<p>$\sum_{i=1}^{n} \sum_{j=0}^{i} \frac{n!}{i!(n-i)!} &#42; S(k,j)&#42;j!&#42;\frac{i!}{j!(i-j)!}$</p>
<p>.</p>
<p>约分得：</p>
<p>$\sum_{i=1}^{n} \sum_{j=0}^{i} \frac{n!}{(n-i)!} &#42; S(k,j)&#42;\frac{1}{(i-j)!}$</p>
<p>.</p>
<p>因为$k$很小，$j$很大，而第二类斯特林数的定义告诉我们，当$j>k$时，$S(k,j)=0$。因此，我们可以考虑把$S(k,j)$放到外面来处理，根据<a href="https://blog.csdn.net/github_35736728/article/details/80933891" target="_blank"  rel="nofollow" >交换和号</a>的原则，我们可以处理前面两个$\sum$来方便把$S(k,j)$提到外面来。</p>
<p>交换和号后得：</p>
<p>$\sum_{j=0}^{n} \sum_{i=j}^{n} \frac{n!}{(n-i)!} &#42; S(k,j)&#42;\frac{1}{(i-j)!}$</p>
<p>.</p>
<p>然后就可以把$S(k,j)$提到外面来了：</p>
<p>$\sum_{j=0}^{n} S(k,j) \times \sum_{i=j}^{n} \frac{n!}{(n-i)!}&#42;\frac{1}{(i-j)!}$</p>
<p>.</p>
<p>考虑到一点，当我们的$j>k$的时候，$S(k,j)=0$，因此，我们前面$j$的枚举范围可以缩小为$min(k,n)$</p>
<p>$\sum_{j=0}^{min(n,k)} S(k,j) \times \sum_{i=j}^{n} \frac{n!}{(n-i)!}&#42;\frac{1}{(i-j)!}$</p>
<p>.</p>
<p>这时候我们可以发现：后面那个循环长得非常像组合数，考虑上下同时乘以$(n-j)!$让它变成组合数:</p>
<p>$\sum_{j=0}^{min(n,k)} S(k,j) \times \sum_{i=j}^{n} \frac{n!}{(n-j)!}&#42;\frac{(n-j)!}{(n-i)!*(i-j)!}$</p>
<p>$\sum_{j=0}^{min(n,k)} S(k,j) \times \sum_{i=j}^{n} \frac{n!}{(n-j)!}&#42;C_{(n-j)}^{(n-i)}$</p>
<p>.</p>
<p>同理，我们可以把后面那个阶乘提到前面去</p>
<p>$\sum_{j=0}^{min(n,k)} S(k,j)&#42;\frac{n!}{(n-j)!} \times \sum_{i=j}^{n} C_{(n-j)}^{(n-i)}$</p>
<p>.</p>
<p>我们还可以注意到，后面那个循环：当$i&lt;j$的时候，算出来的东西是没有意义的，因此我们可以改变循环范围为$i=0$ 来方便把那个难以计算的$\sum$变为方便计算的$2^x$的形式</p>
<p>$\sum_{j=0}^{min(n,k)} S(k,j)&#42;\frac{n!}{(n-j)!} \times \sum_{i=0}^{n} C_{(n-j)}^{(n-i)}$</p>
<p>.</p>
<p>$\sum_{j=0}^{min(n,k)} S(k,j)&#42;\frac{n!}{(n-j)!} \times 2^{(n-j)}$</p>
<p>.</p>
<p>搞定，到目前为止，这里里面的所有东西都可以方便的求出来了：</p>
<p>$S(k,j)$可以用$k^2$的递推暴力求算<del>神犇们大可用FFT或NTT快速计算，可惜我太菜了并不会</del></p>
<p>$\frac{n!}{(n-j)!}$可以用一个$O(k)$的暴力递推算即可</p>
<p>$2^{(n-j)}$........如果不会算的话请自行右上角</p>
<p>.</p>
<p>时间复杂度$O(n^2)$</p>
<p>酱紫，这题就被我们A掉啦~</p>
<p>撒花ヾ(●´∀｀●)</p>
<p>.</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">//CF932E Team Work
//Jan,15th,2019
//第二类斯特林数的应用+奇奇怪怪的推公式
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
using namespace std;
const int poi=1000000007;
long long FastPow(long long A,long long B)
{
    if(B==0) return 1;
    long long t_ans=FastPow(A,B/2);
    t_ans=t_ans*t_ans%poi;
    if(B%2==1) t_ans=t_ans*A%poi;
    return t_ans;
}
const int N=5000+100;
long long S[N][N],jc[N];
int n,K;
int main()
{
    scanf("%d%d",&amp;n,&amp;K);

    S[1][1]=1;
    for(int i=2;i&lt;=K;i++)
        for(int j=1;j&lt;=i;j++)
            S[i][j]=(S[i-1][j-1]+j*S[i-1][j])%poi;
    jc[0]=1;
    for(int i=1;i&lt;=K;i++)
        jc[i]=jc[i-1]*(n-i+1)%poi;

    long long ans=0;
    int t=min(n,K);
    for(int i=0;i&lt;=t;i++)
        ans+=S[K][i]*jc[i]%poi*FastPow(2,n-i),ans%=poi;

    printf("%lld",ans);
    return 0;
}

</code></pre>
