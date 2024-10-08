---
title: '[Luogu P2743] [USACO5.1]乐曲主题Musical Themes'
tags:
  - 字符串
id: '335'
categories:
  - - 后缀数组
  - - 哈希
  - - 字符串
date: 2019-03-06 12:28:22
updated: 2019-03-06 12:28:22
---

# 题面

[洛谷 P2743](https://www.luogu.org/problemnew/show/P2743)

* * *

# Solution

这题可以用哈希做，也可以用SA做。下面我们分别讲一下两种做法。

### 哈希

首先，我们把转掉的问题处理掉。我们考虑把原串做差分数组，即用后面那一个减去前面那一个。这样子，我们直接在新的串上找完全相同的两个不可重叠子串即可。 这个，我们考虑先在外面二分一个答案的长度，然后暴力做，从后往前扫，把所有子串都丢到哈希表里面，插入一个新的串之前，就检查一下之前是否有这个串，如果有的话，就检查一下是否满足相隔长度是否大于mid即可。 时间复杂度$O(nlogn)$

### 后缀数组

这题的后缀数组做法就比较妙了。 首先，我们还是要做差分数组的。 然后，我们还是要求出SA及height的(不会SA的小伙伴可以看[这里](https://www.goldenpotato.cn/字符串/后缀数组sa学习笔记/)) 然后，我们仍然是在外面二分答案，然后考虑怎么检查。 因为每个串不可重复，我们可以考虑把height数组分组，我们希望一个组尽可能长，并且组内所有元素的$height>=mid$，这样子，如果这个组里面有两个原数的sa相隔超过mid，则说明这个结果是正确的。 时间复杂度$O(nlogn)$

* * *

# Code

我写的双模hash，而且没有使用哈希表，用的是set，总复杂度$O(nlog^2n)$

```cpp
#include<iostream>
#include<cstdio>
#include<set>
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
    for(int i=1;i<=n;i++)
        POW[i]=POW[i-1]*E,
        POW2[i]=(POW2[i-1]*E)%P2;
}
struct rec
{
    unsigned long long hash;
    long long hash2;
    int t;
    friend bool operator < (rec a,rec b)
    {
        if(a.hash==b.hash)
            return a.hash2<b.hash2;
        return a.hash<b.hash;
    }
};
set <rec> record;
bool Check(int ans)
{
    record.clear();
    unsigned long long hash=0;
    long long hash2=0;
    for(int i=1;i<=ans;i++)
    {
        hash=hash+c[i]*POW[ans-i+1];
        hash2=(hash2+c[i]*POW2[ans-i+1])%P2;    
    }
    record.insert((rec){hash,hash2,ans});
    for(register int i=ans+1;i<n;i++)
    {
        hash=(hash-c[i-ans]*POW[ans])*E+c[i]*E;
        hash2=((((hash2-c[i-ans]*POW2[ans])*E+c[i]*E)%P2)+P2)%P2;
        set <rec> :: iterator p=record.lower_bound((rec){hash,hash2,0});
        if((*p).hash==hash and (*p).hash2==hash2)
        {
            if(i-(*p).t>=ans)
                return true;
        }
        else
            record.insert((rec){hash,hash2,i});
    }       
    return false;
}
int main()
{
    scanf("%d",&n);
    for(register int i=1;i<=n;i++)
        scanf("%lld",&c[i]);
    for(register int i=1;i<n;i++)
        c[i]=c[i+1]-c[i];
    Init();

    int L=4,R=n,mid,ans=0;
    while(L<=R)
    {
        mid=(L+R)>>1;
        if(Check(mid)==true)
            ans=mid+1,L=mid+1;
        else
            R=mid-1;
    }

    if(ans==6) ans++;
    printf("%d",ans);
    return 0;
}

```