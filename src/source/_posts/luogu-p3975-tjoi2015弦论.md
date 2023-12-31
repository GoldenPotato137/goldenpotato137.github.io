---
title: '[Luogu P3975] [TJOI2015]弦论'
tags:
  - 字符串
id: '423'
categories:
  - - 后缀自动机
  - - 字符串
date: 2019-04-10 12:19:42
---

## 题面

[P3975 [TJOI2015]弦论](https://www.luogu.org/problemnew/show/P3975)

## Solution

看到题面要求不同情况下的$K$小串，给人一种自动机上做DP就可以写的感觉。 

因此，我们考虑用后缀自动机来解决这个问题。我们先建出SAM。 

对于$k=0$的情况，肥肠好写。根据SAM的常识，在SAM上任意走都是原串的一个子串。题目要求求出第$k$小不重复子串，既是让我们求出SAM的前$k$条路径。因为这里的$k$很大，我们是不能暴力走的。

因此，我们可以设$f[i]$表示从$i$出发有多少条路径，转移非常显然，$f[i]=\sum f[j]$(j为i能到的点)。我们用记忆话搜索/拓扑序DP即可求出。

接下来，我们在SAM上做dfs即可。 

对于$k=1$的情况，我们则必须求出每个串的出现次数。这需要sam的一点性质。我们知道，**SAM的fail树上的任何一个节点所代表的的字符串一定是它的孩子节点所代表的的字符串的后缀。**

因此，**当前节点所表示的串的出现次数就是1+子孙出现次数**，这里要注意一点，复制出来的节点不需要+1，如果+1就会计算重复。计算出来每个串的出现次数后的事情就和$k=0$的做法一模一样了。 

时间复杂度$O(n)$ 

就酱，这道题就被我们切掉啦ヾ(^Д^*)/

## Code

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<vector>
using namespace std;
//const int N=500;
const int N=500000*2+200;
struct SAM
{
    static const int M=26;
    struct node
    {
        int son[M],fail,len,size;
        bool IsNew;
    }nd[N];
    int last,to;
    void Init()
    {
        last=to=1;
    }
    void Insert(char c)
    {
        int x=c-'a',now=last,n_now=++to;
        nd[n_now].len=nd[now].len+1;
        last=n_now;
        for(;now!=0 and nd[now].son[x]==0;now=nd[now].fail)
            nd[now].son[x]=n_now;
        if(now==0)
            nd[n_now].fail=1;
        else
        {
            int tmp=nd[now].son[x];
            if(nd[tmp].len==nd[now].len+1)
                nd[n_now].fail=tmp;
            else
            {
                int n_tmp=++to;
                nd[n_tmp]=nd[tmp],nd[n_tmp].len=nd[now].len+1,nd[tmp].fail=n_tmp,nd[n_tmp].IsNew=true;
                nd[n_now].fail=n_tmp;
                for(;now!=0 and nd[now].son[x]==tmp;now=nd[now].fail)
                    nd[now].son[x]=n_tmp;
            }
        }
    }
    vector <int> e[N];
    void Build()
    {
        for(int i=1;i<=to;i++)
            e[i].reserve(4);
        for(int i=1;i<=to;i++)
            e[nd[i].fail].push_back(i);
    }
    int dfs(int now,bool type)
    {
        nd[now].size=(nd[now].IsNew==false);
        for(int i=0;i<int(e[now].size());i++)
            nd[now].size+=dfs(e[now][i],type);
        if(type==0)
            nd[now].size=1;
        return nd[now].size;
    }
    int mstack[N],top,cnt[N];
    int dfs2(int now)
    {
        if(cnt[now]!=0) return cnt[now];
        cnt[now]+=nd[now].size;
        for(int i=0;i<M;i++)
            if(nd[now].son[i]!=0)
                cnt[now]+=dfs2(nd[now].son[i]);
        return cnt[now];
    }
    bool OK;
    void dfs3(int now,int K)
    {
        if(OK==true) return;
        K-=nd[now].size;
        if(K<=0)
        {
            OK=true;
            for(int i=1;i<=top;i++)
                printf("%c",mstack[i]+'a');
            return;
        }
        for(int i=0;i<M;i++)
            if(nd[now].son[i]!=0)
            {
                if(cnt[nd[now].son[i]]>=K)
                {
                    mstack[++top]=i;
                    dfs3(nd[now].son[i],K);
                }
                else
                    K-=cnt[nd[now].son[i]];
            }
    }
}sam;

char s[N];
int K,type;
int main()
{
    scanf("%s%d%d",s+1,&type,&K);
    int len=strlen(s+1);
    sam.Init();
    for(int i=1;i<=len;i++)
        sam.Insert(s[i]);

    sam.Build();
    sam.dfs(1,type);
    sam.nd[1].size=0;
    sam.dfs2(1);

    sam.dfs3(1,K);
    if(sam.OK==false)
        printf("-1");
    return 0;
}

```