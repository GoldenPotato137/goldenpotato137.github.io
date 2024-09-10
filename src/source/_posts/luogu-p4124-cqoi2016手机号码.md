---
title: '[Luogu P4124] [CQOI2016]手机号码'
tags:
  - DP
id: '220'
categories:
  - - 动态规划
  - - 数位DP
date: 2019-02-25 19:20:25
updated: 2019-02-25 19:20:25
---

# 题面

传送门：[洛咕](https://www.luogu.org/problemnew/show/P4124)

* * *

# Solution

感谢神仙@lizbaka的教学 这题是数位DP的非常非常模板的题目，只是状态有点多 . 这题我使用记忆化搜索实现的 中国有句古话说的好，有多少个要求就设多少个状态。 所以说，考虑这样设置状态: 设$f\[i\]\[j\]\[k\]\[2\]\[2\]\[2\]\[2\]\[2\]$表示**当前填到第i位，上一位填了j，上两位填了k，是否卡上界，上一个数是否为前导零，是否有4，是否有8，是否出现了连续三个相同的数字，之后任意填的可行方案总数** 使用记忆化搜索的话，转移是非常容易的，我们只需要像写搜索一样递归写下去就好 差不多长成这样：

```cpp
for(int i=0;i<=(limit==true?l[to]:9);i++)
    {
        if(zero==false or i!=0)
            t_ans+=dfs(to+1,i,last1,limit==true and i==l[to],false,four or i==4,eight or i==8,ok==true or(last1==last2 and last2==i));
        else
            t_ans+=dfs(to+1,i,last1,limit==true and i==l[to],true,false,false,false);
    }
```

注：此题不用讨论前导零，但是我为了模板的完整性，也加上了。 **注意，递归算法一定要有出口，这里也不例外，出口为i==n+1（即已经填完了）** 具体写法还请看代码

* * *

# Code

```cpp
//Luogu P4124 [CQOI2016]手机号码
//Jan,13rd,2019
//测试递归实现数位DP
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
const int N=20;
long long f[N][15][15][2][2][2][2][2];//位数，上一位，上两位，limit，zero，4，8，OK
int n,l[N];
long long dfs(int to,int last1,int last2,bool limit,bool zero,bool four,bool eight,bool ok)
{
    if(f[to][last1][last2][limit][zero][four][eight][ok]>=0) 
        return f[to][last1][last2][limit][zero][four][eight][ok];
    long long t_ans=0;  
    if(to==n+1) 
    {
        if((four and eight)==false and ok==true)
            t_ans=1;
        return f[to][last1][last2][limit][zero][four][eight][ok]=t_ans; 
    }
    for(int i=0;i<=(limit==true?l[to]:9);i++)
    {
        if(zero==false or i!=0)
            t_ans+=dfs(to+1,i,last1,limit==true and i==l[to],false,four or i==4,eight or i==8,ok==true or(last1==last2 and last2==i));
        else
            t_ans+=dfs(to+1,i,last1,limit==true and i==l[to],true,false,false,false);
    }
    return f[to][last1][last2][limit][zero][four][eight][ok]=t_ans;
}
int main()
{
    long long ans[3];
    for(int i=1;i<=2;i++)
    {
        long long t_num;
        scanf("%lld",&t_num);
        if(i==1) t_num--;
        n=0;
        while(t_num!=0)
            l[++n]=t_num%10,t_num/=10;
        reverse(l+1,l+1+n);

        memset(f,0x80,sizeof f);
        dfs(1,0,0,true,true,false,false,false);

        ans[i]=f[1][0][0][true][true][false][false][false];
    }

    printf("%lld",ans[2]-ans[1]);
    return 0;
}

```