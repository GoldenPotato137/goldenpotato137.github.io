---
title: "[Luogu P2852] [USACO06DEC]牛奶模式Milk Patterns"
date: 2019-03-06 03:44:45
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：<a href="https://www.luogu.org/problemnew/show/P2852" target="_blank"  rel="nofollow" >洛谷P2852</a></p>
<hr />
<h1>Solution</h1>
<p>首先，我们阅读题面可以发现题目让我们求出一个<strong>出现次数>k的可重复的子串</strong>。</p>
<p>这玩意我们可以用SA求，也可以用SAM求。</p>
<h3>SA</h3>
<p>这题用SA做就比较妙，首先我们显然要求把SA及height求出来。<br />
因为<strong>两个后缀的LCP是它们之间的height的min</strong>，我们可以利用这个性质。</p>
<p>考虑一个子串，它所能“控制”的区间的所有的height都必须比它大。<br />
因此，我们可以找出一个height所影响的左右范围，这个我们使用单调栈可以很轻松地求出。</p>
<pre><code class="language-cpp ">for(int i=1;i&lt;=n;i++)
{
    while(top&gt;0 and height[i]&lt;=height[mstack[top]]) top--;
    L[i]=mstack[top]+1;
    mstack[++top]=i;
}
mstack[top=0]=n+1;
for(int i=n;i&gt;=1;i--)
{
    while(top&gt;0 and height[i]&lt;=height[mstack[top]]) top--;
    R[i]=mstack[top]-1;
    mstack[++top]=i;
}
</code></pre>
<p>然后我们可以发现一个height所“控制”的区间中，这个串被“引用”的次数一定是$R[i]-(L[i]-1)+1$，下面这个图可以简单的说明这一点：<br />
<a href="https://imgchr.com/i/kjv0bR" target="_blank"  rel="nofollow" ><img src="https://s2.ax1x.com/2019/03/06/kjv0bR.md.png" alt="kjv0bR.md.png" /></a></p>
<p>这样，我们只需要二分一个答案，然后一路扫过去检查就完事啦(～￣▽￣)～<br />
时间复杂度$O(nlogn)$,空间复杂度$O(n)$</p>
<h3>SAM</h3>
<p>这题用SAM做就非常显然了。<strong>根据fail树的性质：fail树中的每个节点所代表的串都必然是它的孩子节点的后缀</strong>。我们可以得到一个比较显然的性质：每个叶子节点所代表的子串在原串中只出现了1次，而每个节点所代表的子串在原串中出现的次数必然为它的孩子的出现次数的总和，如果这个点不是被复制出来的，次数还要+1（它自己所代表的串）；</p>
<p>然后，我们只需要便利一遍SAM，把所有出现次数>K的节点的len取个max即可。</p>
<p>时间复杂度$O(n)$，空间复杂度$O(n\ *10)$</p>
<hr />
<h1>Code</h1>
<p>我只写了SA的做法，SAM的做法还请自行yy</p>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstring&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int M=1000000+100;
const int N=20000+100;
int s[N],sa[N],id[N],height[N];
long long rank[N];
void CountSort(long long a[],int n,int Exp,int m)
{
    static long long cnt[M],b[N];
    memset(cnt,0,sizeof cnt);
    for(int i=1;i&lt;=n;i++)
        cnt[(a[i]/Exp)%m]++;
    for(int i=1;i&lt;=m;i++)
        cnt[i]+=cnt[i-1];
    for(int i=n;i&gt;=1;i--)
    {
        b[cnt[(a[i]/Exp)%m]]=a[i];
        if(Exp==1)
            id[cnt[(a[i]/Exp)%m]--]=i;
        else
            sa[cnt[(a[i]/Exp)%m]--]=id[i];
    }
    for(int i=1;i&lt;=n;i++)
        a[i]=b[i];
}
void RadixSort(long long a[],int n,int m)
{   
    CountSort(a,n,1,m);
    CountSort(a,n,m,m);
}
int n,K,L[N],R[N];
void GetSA()
{   
    static long long t[N];
    for(int i=1;i&lt;=n;i++)
        rank[i]=t[i]=s[i];
    int m=1000000+1;
    for(int k=1;;k=k&lt;&lt;1)
    {
        for(int i=1;i&lt;=n;i++)
            rank[i]=t[i]=rank[i]*m+(i+k&lt;=n?rank[i+k]:0);
        RadixSort(t,n,m);
        m=0;
        for(int i=1;i&lt;=n;i++)
        {
            if(t[i]!=t[i-1])
                m++;
            rank[sa[i]]=m;
        }
        if(m==n) break;
        m++;
    }

    for(int i=1;i&lt;=n;i++)
    {
        if(rank[i]==1) continue;
        int to=max(0,height[rank[i-1]]-1);
        for(;sa[rank[i]]+to&lt;=n and sa[rank[i]-1]+to&lt;=n;to++)
            if(s[sa[rank[i]]+to]!=s[sa[rank[i]-1]+to])
                break;
        height[rank[i]]=to;
    }
}
int mstack[N],top;//记录从哪来，单调严格上升栈
bool Check(int x)
{
    for(int i=1;i&lt;=n;i++)
        if(height[i]&gt;=x and (R[i]-(L[i]-1)+1)&gt;=K)
            return true;
    return false;
}
int main()
{
    n=read(),K=read();
    for(int i=1;i&lt;=n;i++)
        s[i]=read();

    GetSA();
    for(int i=1;i&lt;=n;i++)
    {
        while(top&gt;0 and height[i]&lt;=height[mstack[top]]) top--;
        L[i]=mstack[top]+1;
        mstack[++top]=i;
    }
    mstack[top=0]=n+1;
    for(int i=n;i&gt;=1;i--)
    {
        while(top&gt;0 and height[i]&lt;=height[mstack[top]]) top--;
        R[i]=mstack[top]-1;
        mstack[++top]=i;
    }

    int ans=0,l=0,r=n,mid;
    while(l&lt;=r)
    {
        mid=(l+r)/2;
        if(Check(mid)==true)
            l=mid+1,ans=max(ans,mid);
        else
            r=mid-1;
    }

    printf("%d",ans);
    return 0;
}

</code></pre>
