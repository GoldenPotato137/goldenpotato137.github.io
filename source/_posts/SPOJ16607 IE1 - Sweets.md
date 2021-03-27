---
title: "SPOJ16607 IE1 - Sweets"
date: 2019-02-25 11:21:15
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：</p>
<ul>
<li><a href="https://www.luogu.org/problemnew/show/SP16607" target="_blank"  rel="nofollow" >洛咕</a></li>
<li><a href="https://www.spoj.com/problems/IE1/" target="_blank"  rel="nofollow" >SPOJ</a></li>
</ul>
<hr />
<h1>Solution</h1>
<p>这题的想法挺妙的。</p>
<p>.</p>
<p>首先，对于这种区间求答案的问题，我们一般都可以通过类似前缀和的思想一减来消去a，<strong>即求[a,b]的答案可以转化为求[1,b]-[1,a-1]</strong></p>
<p>接下来我们可以先考虑一下每个物品数量不限制的做法。我们可以把这个问题类比为放球问题：我们要在n个相同的盒子里放x个球，这个问题可以用隔板法解决，<strong>显然答案为$C_{x+n-1}^{n-1}$</strong></p>
<p>因为我们的n特别小，而且p为合数，所以可以用分解质因数的方法来算这个组合数。</p>
<p>.</p>
<p>接下来，我们可以考虑一下如何处理多计算的答案，考虑用容斥定理来解决这个问题。</p>
<p>不了解容斥定理的同志可以先看一下<a href="https://blog.csdn.net/m0_37286282/article/details/78869512" target="_blank"  rel="nofollow" >这篇文章</a></p>
<p>我们要求的是至少有一个物品不满足要求的方案总数，即求所有不满足要求的方案的并。</p>
<p>根据容斥定理，这个并的值为 $\sum有一个物品不满足要求-有两个物品不满足要求+有三个物品不满足要求-...$</p>
<p>所以说，我们只需要强制某些物品先选$m_i+1$个，再按照上面的放球问题的公式来计算就可以得出有若干个物品不满足要求的方案数。</p>
<p>答案即为总方案数-不满足要求的方案数的并</p>
<p>时间复杂度$O(2^n*log_{max(a,b)})$</p>
<p>这个问题就被我们切掉啦ヽ(￣▽￣)ﾉ</p>
<p>.</p>
<p>如果有不清楚的地方可以看一下代码。</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">//Luogu SP16607 IE1 - Sweets
//Jan,14th,2019
//容斥原理的应用
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
const int poi=2004;
const int N=15;
int prime[6]={-1,2,3,5,7};
long long C(long long x,long long y)//x为底，y为指
{
    if(y&gt;x) return 0;
    int cnt[6]={0};
    long long t_ans=1;
    for(long long i=x-y+1;i&lt;=x;i++)
    {
        long long t_num=i;
        for(int j=1;j&lt;=4;j++)
            while(t_num%prime[j]==0)
            {
                t_num/=prime[j];
                cnt[j]++;
            }
        t_ans=(t_ans*t_num)%poi;
    }
    for(long long i=1;i&lt;=y;i++)
    {
        long long t_num=i;
        for(int j=1;j&lt;=4;j++)
            while(t_num%prime[j]==0)
            {
                t_num/=prime[j];
                cnt[j]--;
            }
        }
    for(int i=1;i&lt;=4;i++)
        while(cnt[i]&gt;0)
            t_ans=(t_ans*prime[i])%poi,cnt[i]--;
    return t_ans;
}
int m[N],n,a,b;
long long t_ans2,t_x;
bool used[N];
void dfs(int now)
{
    if(now==n+1)
    {
        long long t_cnt=0,tot=0;
        for(int i=1;i&lt;=n;i++)
            if(used[i]==true)
                t_cnt+=m[i]+1,tot++;
        if(t_cnt&gt;t_x) return;
        long long f=(tot%2==1?-1:1);
        t_ans2+=f*C(t_x-t_cnt+n,n);
        t_ans2=(t_ans2%poi+poi)%poi;
        return;
    }
    for(int i=0;i&lt;=1;i++)
        used[now]=i,dfs(now+1);
}
long long Calc(long long x)
{
    t_ans2=0,t_x=x;
    dfs(1);
    return t_ans2;
}
int main()
{
    n=read(),a=read(),b=read();
    for(int i=1;i&lt;=n;i++)
        m[i]=read();

    printf("%lld",((Calc(b)-Calc(a-1))%poi+poi)%poi);
    return 0;
}

</code></pre>
