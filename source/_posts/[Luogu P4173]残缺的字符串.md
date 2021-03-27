---
title: "[Luogu P4173]残缺的字符串"
date: 2019-03-01 06:55:43
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：<a href="https://www.luogu.org/problemnew/show/P4173" target="_blank"  rel="nofollow" >洛咕</a></p>
<hr />
<h1>Solution</h1>
<p><del>这题我写得脑壳疼，我好菜啊</del></p>
<p>好吧，我们来说正题。<br />
这题.....emmmmmmm<br />
显然KMP类的字符串<del>神仙</del>算法在这里没法用了。</p>
<p>那咋搞啊（或者说这题和数学有半毛钱关系啊）<br />
我们考虑把两个字符相同强行变为一个数学关系，怎么搞呢？<br />
考虑这题是带通配符的，我们可以这样设:<br />
$C(x,y)=(A[x]-B[y])^2&#42;A[x]&#42;B[y]$<br />
因此，我们可以看出两个字符一样当且仅当$C(x,y)=0$</p>
<p>因此，我们再设一个函数$P(x)$表示$B$串以第$x$项为结尾的长度为$m$的子串是否与$A$串匹配，显然有：<br />
$P(x)=\sum_{i=0}^{m-1}C(i,x-m+i+1)$<br />
$P(x)=\sum_{i=0}^{m-1}(A[i]-B[x-m+i+1])^2&#42;A[i]&#42;B[x-m+i+1]$</p>
<p>后面那个式子写的太蛋疼了，我们把$x-m+i+1$设为$j$吧。<br />
$P(x)=\sum_{i=0}^{m-1}(A[i]-B[j])^2&#42;A[i]&#42;B[j]$</p>
<p>大力展开得：<br />
$P(x)=\sum_{i=0}^{m-1}A[i]^3B[j]-2A[i]^2B[j]^2+A[i]B[j]^3$</p>
<p>然后.....我们试着把$\sum$展开？<br />
$P(x)=\sum_{i=0}^{m-1}A[i]^3B[j]-\sum_{i=0}^{m-1}2A[i]^2B[j]^2+\sum_{i=0}^{m-1}A[i]B[j]^3$</p>
<p>还是没法算啊......<br />
诶，等下，如果我们把$A$串反转为$A'$,肯定有$A[i]=A'[m-i-1]$<br />
然后这个$(m-i-1)+(j)$......不就是等于$x$嘛。<br />
所以说我们马上就有：<br />
$P(x)=\sum_{i+j=x}A[i]^3B[j]-\sum_{i+j=x}2A[i]^2B[j]^2+\sum_{i+j=x}A[i]B[j]^3$</p>
<p>哦豁，卷积，搞定~<br />
时间复杂度$O(nlogn)$</p>
<p>搞定个鬼，这题要做7次FFT，常数爆大，<del>我卡不进去</del><br />
还请各位dalao赐教。</p>
<hr />
<h1>Code</h1>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;complex&gt;
#include&lt;cmath&gt;
#include&lt;algorithm&gt;
using namespace std;
const int M=300000+100;
const int N=M*4;
const double PI=acos(-1);
const double eps=1e-1;
typedef complex &lt;double&gt; cp;
cp omega(int K,int n)
{
    return cp(cos(2*PI*K/n),sin(2*PI*K/n));
}
inline void FFT(cp a[],int n,bool type)
{
    static int tmp[N],num=n-1,len;
    while(num!=0) num/=2,len++;
    for(int i=0,j;i&lt;=n;i++)
    {
        for(j=0,num=i;j&lt;len;j++)
            tmp[j]=num%2,num/=2;
        reverse(tmp,tmp+len);
        for(j=0,num=0;j&lt;len;j++)
            num+=tmp[j]*(1&lt;&lt;j);
        if(i&lt;num) swap(a[i],a[num]);
    }
    for(int l=2;l&lt;=n;l*=2)
    {
        int m=l/2;
        cp x0=omega(1,l);
        if(type==true) x0=conj(x0);
        for(int i=0;i&lt;n;i+=l)
        {
            cp x(1,0);
            for(int j=0;j&lt;m;j++,x*=x0)
            {
                cp temp=x*a[i+j+m];
                a[i+j+m]=a[i+j]-temp;
                a[i+j]=a[i+j]+temp;
            }
        }
    }
}
char A[N],B[N];
int m,n,t=1;
cp S1[N],S2[N],S3[N],B1[N],B2[N],B3[N];
bool OK[N];
int main()
{
    scanf("%d%d%s%s",&amp;m,&amp;n,A,B);

    while(t&lt;=(n+m)) t*=2;
    reverse(A,A+m); 
    for(int i=0;i&lt;t;i++)
        A[i]=(A[i]=='*' or A[i]&lt;'a' or A[i]&gt;'z'?0:A[i]-'a'+1),
        B[i]=(B[i]=='*' or B[i]&lt;'a' or B[i]&gt;'z'?0:B[i]-'a'+1);
    for(int i=0;i&lt;t;i++)
        S1[i]=A[i],S2[i]=A[i]*A[i],S3[i]=A[i]*A[i]*A[i];
    for(int i=0;i&lt;t;i++)
        B1[i]=B[i],B2[i]=B[i]*B[i],B3[i]=B[i]*B[i]*B[i];
    FFT(S1,t,false);
    FFT(S2,t,false);
    FFT(S3,t,false);
    FFT(B1,t,false);
    FFT(B2,t,false);
    FFT(B3,t,false);

    for(int i=0;i&lt;t;i++)
        S3[i]*=B1[i];
    for(int i=0;i&lt;t;i++)
        S1[i]*=B3[i];
    for(int i=0;i&lt;t;i++)
        S2[i]*=B2[i];
    for(int i=0;i&lt;t;i++)
        S3[i]+=S1[i]-2.0*S2[i];
    FFT(S3,t,true);
    int cnt=0;
    for(int i=m-1;i&lt;n;i++)
        if(fabs(S3[i].real()/t)&lt;eps)
            OK[i]=true,cnt++;

    printf("%d\n",cnt);
    for(int i=m-1;i&lt;n;i++)
        if(OK[i]==true)
            printf("%d ",i-m+1+1);
    return 0;
}

</code></pre>
