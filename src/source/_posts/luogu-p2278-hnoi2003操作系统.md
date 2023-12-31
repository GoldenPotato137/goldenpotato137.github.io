---
title: '[Luogu P2278] [HNOI2003]操作系统'
tags:
  - 堆
  - 数据结构
  - 模拟
id: '98'
categories:
  - - 堆
  - - 数据结构
date: 2019-02-12 23:57:02
---

# 题面

传送门:[洛咕](https://www.luogu.org/problemnew/show/P2278)

* * *

# Solutiton

挺简单的一道模拟题,拿堆模拟一下题目意思就好 堆中有两个关键字,分别是优先级和到达时间 还要维护一下每个任务剩余时间(还有多久完成) 因为堆不能直接改.得在堆里记录编号然后映射出来 这里总结一下要注意的细节: 1.在下一个任务到达之前,尽可能把CPU内的任务完成了 2.注意读入 3.注意注释文件读写233(我因为这破事爆零一次) 4.没了 好像不需要记录到达时间,编号即为到达时间先后 但懒得改了

* * *

# Code

```cpp
//Luogu P2278 [HNOI2003]操作系统
//May,4th,2018
//堆+模拟
#include<iostream>
#include<cstdio>
#include<queue>
using namespace std;
const int N=1000000;
int t[N];
struct op
{
    int a,no,at;
    friend bool operator < (op A,op B)
    {
        if(A.a==B.a)
            return A.at > B.at;
        return A.a < B.a;
    }
};
priority_queue <op,vector<op> > d;
int main()
{
    //freopen("system.in","r",stdin);
    int no,at,T,a,t_now=0;
    while(scanf("%d%d%d%d",&no,&at,&T,&a)==4)
    {
        while(d.empty()==false)
        {
            op now=d.top();
            if(t[now.no]<=at-t_now)
            {
                d.pop();
                t_now+=t[now.no];
                printf("%d %d\n",now.no,t_now);
            }
            else
            {
                t[now.no]-=at-t_now;
                break;
            }
        }
        op temp;
        temp.no=no,temp.a=a,temp.at=at,t[no]=T;
        d.push(temp);
        t_now=at;
    }

    while(d.empty()==false)
    {
        op now=d.top();
        d.pop();
        t_now+=t[now.no];
        printf("%d %d\n",now.no,t_now);
    }
    return 0;
}

```