---
title: "后缀数组(SA)学习笔记"
date: 2019-03-06 03:06:37
tags: "迁移"
---
<h1>为什么要学后缀数组</h1>
<p>为了解决一类字符串<del>神题</del>妙题。</p>
<hr />
<h1>什么是后缀数组</h1>
<h3>什么是后缀</h3>
<p>要理解什么是后缀数组，首先要明白什么是后缀。<br />
一个字符串$[S_1:S_n]$的后缀为:$[S_i:S_n],i∈[1,n]$<br />
用人话来说，就是从每个字符开始到这个字符串的结尾所组成的串均为这个字符串的后缀。</p>
<blockquote><p>
  <strong>例如：</strong>假设我现在有一个字符串“abaaba”,那么。她的后缀有：a,ba,aba,aaba,baaba,abaaba。
</p></blockquote>
<h3>什么是后缀数组</h3>
<p>把所有后缀按<strong>字典序</strong>排序之后所构成的数组。</p>
<blockquote><p>
  <strong>例如：</strong>假设我现在有一个字符串“abaaba”,那么。她的后缀有：a,ba,aba,aaba,baaba,abaaba。她的后缀数组为：a,aaba,aba,abaaba,ba,baaba
</p></blockquote>
<hr />
<h1>后缀数组怎么求</h1>
<p><strong>FBI warning：本文并不会讲解为什么能这样求，但是她的证明并不难，感兴趣的读者可以自行拿出草稿纸感性理解一下，或百度一下以获得理性证明</strong></p>
<p>这是一张极其著名的图片 (图出自<a href="https://baike.baidu.com/item/%E5%90%8E%E7%BC%80%E6%95%B0%E7%BB%84/8989867?fr=aladdin" target="_blank"  rel="nofollow" >百度百科</a>)<br />
<img src="https://s2.ax1x.com/2019/03/06/kjgVQe.png" alt="kjgVQe.png" /><br />
<strong>这里的rank是我们要求的SA的“反数组”，rank指的是每个下标对应的后缀的排名，SA就是我们的后缀数组了：每个排名对应的后缀的下标</strong></p>
<p>我想各位读者看这个图片已经能略懂一二，我们每次对求出来的东西离散化，然后按照类似于倍增的方法把前后两个数字拼起来，多次排序，直到rank的值互不相同为止。</p>
<h3>如何排序</h3>
<p>在这里，我们可以使用快速排序，那么求后缀数组的总复杂度为$O(nlog^2n)$，但是，如果我们在这里使用<a href="https://www.goldenpotato.cn/%E5%85%B6%E4%BB%96/%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/">基数排序</a>，单次排序的复杂度即可接近$O(n)$,总复杂度$O(nlogn)$</p>
<p>当然，在这里我们要对基数排序做一点微小的工作，按照正常的基数排序，是不能找到排序后每个值对应的原下标的。魔改的办法也很简单，我们只需要在每次排序的时候传入当前情况下每个值对应的原下标即可：</p>
<pre><code class="language-cpp ">int n,sa[N],id[N];
void count_sort(long long a[],int n,int Exp,int m)
{
    static long long b[N],cnt[N];
    memset(cnt,0,sizeof cnt);
    for(int i=1;i&lt;=n;i++)
        cnt[(a[i]/Exp)%m]++;
    for(int i=1;i&lt;=m;i++)
        cnt[i]+=cnt[i-1];
    for(int i=n;i&gt;=1;i--)
    {
        b[cnt[(a[i]/Exp)%m]]=a[i];
        //我们这里相当于只排两位数，排第一位的时候我们将映射关系零时储存一下
        if(Exp==1) id[cnt[(a[i]/Exp)%m]--]=i;
        //事实上来说，我们第二次求出来的映射关系，本质上就是我们要求的SA数组
        else sa[cnt[(a[i]/Exp)%m]--]=id[i];
    }
    for(int i=1;i&lt;=n;i++)
        a[i]=b[i];
}
void radix_sort(long long a[],int n,int m)
{
    count_sort(a,n,1,m);
    count_sort(a,n,m,m);
}
void GetSA()
{
    for(int i=1;i&lt;=n;i++)
        t[i]=s[i],rank[i]=s[i];
    long long m='z'+1;
    for(int i=0;i&lt;=n;i++)
    { 
        for(int j=1;j&lt;=n;j++)
            rank[j]=t[j]=rank[j]*m+((j+(1&lt;&lt;i)&lt;=n)?rank[j+(1&lt;&lt;i)]:0);//把前后两个数拼起来
        radix_sort(t,n,m);//排序
        m=0;
        //离散化
        for(int j=1;j&lt;=n;j++)
        {
            if(t[j]!=t[j-1])
                ++m;
            rank[sa[j]]=m;
        }
        if(m==n) break;
        m++;
    }
}
</code></pre>
<hr />
<h1>后缀数组的进一步应用</h1>
<p>显然，我们单纯的求出后缀数组是没有什么Egg用的，我们要发掘一下我们求出来的这玩意的性质。其中，最有用的即是后缀数组的LCP了。</p>
<h2>LCP</h2>
<p>lcp，即最长公共前缀，后缀数组的height[i]指的是SA[i]与SA[i-1]的LCP。<br />
<img src="https://s2.ax1x.com/2019/03/06/kjgYLj.png" alt="kjgYLj.png" /></p>
<p><strong>FBI warning：本文并不会讲解为什么能这样求，<del>因为我太菜了并不会</del>，请感兴趣的同学自行百度一下以获得理性证明</strong></p>
<p>要求LCP，我们只需要一个性质<strong>height[rank[i]]>=height[rank[i-1]]-1</strong><br />
然后，我们接下来暴力枚举即可，总复杂度$O(n)$</p>
<pre><code class="language-cpp ">for(int i=1;i&lt;=n;i++)
{
    if(rank[i]==1) continue;
    int to=max(0,height[rank[i-1]]-1);
    for(;sa[rank[i]]+to&lt;=n and sa[rank[i]-1]+to&lt;=n;to++)
        if(s[sa[rank[i]]+to]!=s[sa[rank[i]-1]+to])
            break;
    height[rank[i]]=to;
}
</code></pre>
<p>我们可以发现一个比较显然的性质：<br />
<strong>两个后缀的$LCP[SA[i],SA[j]]=min(height[i+1,j])$</strong></p>
<h3>LCP的应用</h3>
<p>我们思考一下：一个串的后缀的前缀是啥.......</p>
<p>emmmmmmmmmm？这不就<strong>是一个原串的子串</strong>吗？</p>
<p>OK，由这个性质我们就可以做很多事情了，我们在具体的题中再慢慢讲。<br />
T1:<a href="https://www.goldenpotato.cn/%E5%AD%97%E7%AC%A6%E4%B8%B2/luogu-p2852-usaco06dec%E7%89%9B%E5%A5%B6%E6%A8%A1%E5%BC%8Fmilk-patterns/">[USACO06DEC]牛奶模式Milk Patterns</a><br />
T2：<a href="https://www.goldenpotato.cn/%E5%AD%97%E7%AC%A6%E4%B8%B2/luogu-p2743-usaco5-1%E4%B9%90%E6%9B%B2%E4%B8%BB%E9%A2%98musical-themes/">[USACO5.1]乐曲主题Musical Themes</a></p>
