---
title: "[SPOJ694] DISUBSTR - Distinct Substrings"
date: 2019-03-06 15:32:09
tags: "迁移"
---
<h1>题面</h1>
<p>传送门：<a href="https://www.spoj.com/problems/DISUBSTR/" target="_blank"  rel="nofollow" >SPOJ 694</a></p>
<hr />
<h1>Solution</h1>
<p>这题可以用SAM来搞，也可以用SA来搞。但无论是哪种搞法，都是基本操作。下面我们来分别讲解一下怎么搞。</p>
<h3>SAM</h3>
<p>这题用SAM来求就十分粗暴简单。不会SAM的小伙伴可以戳<a href="https://www.goldenpotato.cn/uncategorized/后缀自动机sam学习笔记/">这里</a></p>
<p>首先，我们先把SAM建出来。根据SAM的性质，我们从出发点随着SAM任意走，走到哪里都是一个完全不同的子串。因此，我们只需要对SAM做一个拓扑序DP/记忆化搜索即可求出SAM上的路径总数，既不同子串的数量。</p>
<h3>SA</h3>
<p>这题我们显然还是要把后缀数组和height建出来的，不会SA的小伙伴可以戳<a href="https://www.goldenpotato.cn/%E5%AD%97%E7%AC%A6%E4%B8%B2/%E5%90%8E%E7%BC%80%E6%95%B0%E7%BB%84sa%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/">这里</a></p>
<p>我们可以发现，<strong>对于原串的一个后缀，它的每一个前缀都是原串中的子串</strong>。因此，如果我们把所有后缀长度加起来，得到的就是子串的总数量。</p>
<p>如何去重呢？我们可以发现任意两个后缀，它们会造成重复的子串一定是它们的公共前缀。因此，重复的子串的数量即为$\sum_{i=0}^nheight[i]$，<strong>答案既是后缀总长度减去重复子串的数量</strong></p>
<hr />
<h1>Code</h1>
<p>本题我用SA来实现，用SAM的小伙伴还请自行yy一下w。</p>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstring&gt;
using namespace std;
const int N=1000+10;
int id[N],sa[N],height[N];
long long rank[N];
void CountSort(long long a[],int n,int exp,int m)
{
    static long long cnt[N],b[N];
    memset(cnt,0,sizeof cnt);
    for(int i=1;i&lt;=n;i++)
        cnt[(a[i]/exp)%m]++;
    for(int i=1;i&lt;=m;i++)
        cnt[i]+=cnt[i-1];
    for(int i=n;i&gt;=1;i--)
    {
        b[cnt[(a[i]/exp)%m]]=a[i];
        if(exp==1)
            id[cnt[(a[i]/exp)%m]--]=i;
        else
            sa[cnt[(a[i]/exp)%m]--]=id[i];
    }
    for(int i=1;i&lt;=n;i++)
        a[i]=b[i];
}
void RadixSort(long long a[],int n,int m)
{
    CountSort(a,n,1,m);
    CountSort(a,n,m,m);
}
char s[N];
int n;
void GetSA()
{
    static long long t[N];
    for(int i=1;i&lt;=n;i++)
        rank[i]=t[i]=s[i];
    int m=1000+1;
    for(int k=1;;k=(k&lt;&lt;1))
    {
        for(int i=1;i&lt;=n;i++)
            rank[i]=t[i]=rank[i]*m+(i+k&lt;=n?rank[i+k]:0);
        RadixSort(t,n,m);
        m=0;
        for(int i=1;i&lt;=n;i++)
        {
            if(t[i]!=t[i-1]) m++;
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
int main()
{
    int T;
    scanf("%d",&amp;T);

    for(;T&gt;0;T--)
    {
        scanf("%s",s+1);

        n=strlen(s+1);
        memset(rank,0,sizeof rank);
        GetSA();
        long long ans=0;
        for(int i=1;i&lt;=n;i++)
            ans+=n-sa[i]+1-height[i];

        printf("%lld\n",ans);
    }
    return 0;
}

</code></pre>
