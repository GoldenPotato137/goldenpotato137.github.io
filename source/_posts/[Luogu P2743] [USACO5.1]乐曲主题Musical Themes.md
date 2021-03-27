---
title: "[Luogu P2743] [USACO5.1]乐曲主题Musical Themes"
date: 2019-03-06 04:28:22
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P2743" target="_blank"  rel="nofollow" >洛谷 P2743</a></p>
<hr />
<h1>Solution</h1>
<p>这题可以用哈希做，也可以用SA做。下面我们分别讲一下两种做法。</p>
<h3>哈希</h3>
<p>首先，我们把转掉的问题处理掉。我们考虑把原串做差分数组，即用后面那一个减去前面那一个。这样子，我们直接在新的串上找完全相同的两个不可重叠子串即可。</p>
<p>这个，我们考虑先在外面二分一个答案的长度，然后暴力做，从后往前扫，把所有子串都丢到哈希表里面，插入一个新的串之前，就检查一下之前是否有这个串，如果有的话，就检查一下是否满足相隔长度是否大于mid即可。</p>
<p>时间复杂度$O(nlogn)$</p>
<h3>后缀数组</h3>
<p>这题的后缀数组做法就比较妙了。</p>
<p>首先，我们还是要做差分数组的。<br />
然后，我们还是要求出SA及height的(不会SA的小伙伴可以看<a href="https://www.goldenpotato.cn/字符串/后缀数组sa学习笔记/">这里</a>)<br />
然后，我们仍然是在外面二分答案，然后考虑怎么检查。</p>
<p>因为每个串不可重复，我们可以考虑把height数组分组，我们希望一个组尽可能长，并且组内所有元素的$height>=mid$，这样子，如果这个组里面有两个原数的sa相隔超过mid，则说明这个结果是正确的。</p>
<p>时间复杂度$O(nlogn)$</p>
<hr />
<h1>Code</h1>
<p>我写的双模hash，而且没有使用哈希表，用的是set，总复杂度$O(nlog^2n)$</p>
<pre><code class="language-cpp ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;set&gt;
using namespace std;
const int N=20000+100;
const int E=233;
const int P2=2333333333;
int n;
unsigned long long POW[N];
long long POW2[N],c[N];
void Init()
{
    POW2[0]=POW[0]=1;
    for(int i=1;i&lt;=n;i++)
        POW[i]=POW[i-1]*E,
        POW2[i]=(POW2[i-1]*E)%P2;
}
struct rec
{
    unsigned long long hash;
    long long hash2;
    int t;
    friend bool operator &lt; (rec a,rec b)
    {
        if(a.hash==b.hash)
            return a.hash2&lt;b.hash2;
        return a.hash&lt;b.hash;
    }
};
set &lt;rec&gt; record;
bool Check(int ans)
{
    record.clear();
    unsigned long long hash=0;
    long long hash2=0;
    for(int i=1;i&lt;=ans;i++)
    {
        hash=hash+c[i]*POW[ans-i+1];
        hash2=(hash2+c[i]*POW2[ans-i+1])%P2;    
    }
    record.insert((rec){hash,hash2,ans});
    for(register int i=ans+1;i&lt;n;i++)
    {
        hash=(hash-c[i-ans]*POW[ans])*E+c[i]*E;
        hash2=((((hash2-c[i-ans]*POW2[ans])*E+c[i]*E)%P2)+P2)%P2;
        set &lt;rec&gt; :: iterator p=record.lower_bound((rec){hash,hash2,0});
        if((*p).hash==hash and (*p).hash2==hash2)
        {
            if(i-(*p).t&gt;=ans)
                return true;
        }
        else
            record.insert((rec){hash,hash2,i});
    }       
    return false;
}
int main()
{
    scanf("%d",&amp;n);
    for(register int i=1;i&lt;=n;i++)
        scanf("%lld",&amp;c[i]);
    for(register int i=1;i&lt;n;i++)
        c[i]=c[i+1]-c[i];
    Init();

    int L=4,R=n,mid,ans=0;
    while(L&lt;=R)
    {
        mid=(L+R)&gt;&gt;1;
        if(Check(mid)==true)
            ans=mid+1,L=mid+1;
        else
            R=mid-1;
    }

    if(ans==6) ans++;
    printf("%d",ans);
    return 0;
}

</code></pre>
