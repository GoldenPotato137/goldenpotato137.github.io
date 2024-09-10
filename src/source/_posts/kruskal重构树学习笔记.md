---
title: Kruskal重构树学习笔记
tags:
  - 学习笔记
  - 数据结构
id: '344'
categories:
  - - Kruskal重构树
  - - 学习笔记
  - - 数据结构
date: 2019-03-07 17:43:16
updated: 2019-03-07 17:43:16
---

# 为什么要学Kruskal重构树

有时候，题目让我们多次求一个图的两点路径上最小值最大/最大值最小是多少。这种时候，根据一个比较显然的结论，我们可以把问题搬到一颗最小/最大生成树里面去做。 这个时候，我们当然可以倍增来搞这个问题。但在这里，我想向大家引入一种新的数据结构，它是基于kruskal求生成树的算法改来的，因此被称为Kruskal重构树。

* * *

# 什么是Kruskal重构树

这张图可以一目了然的介绍Kruskal重构树： 图出自[https://blog.csdn.net/wu\_tongtong/article/details/77601523](https://blog.csdn.net/wu_tongtong/article/details/77601523) [![kxUrsx.md.png](https://s2.ax1x.com/2019/03/07/kxUrsx.md.png)](https://imgchr.com/i/kxUrsx)

* * *

# 怎么建Kruskal重构树

肥肠简单，我们在做Krusckal算法的时候，当我们要连边的时候，我们把这两个点连向一个新构建的点(在图中以方点表示)，然后把这两个点的fa设为那个方点，方点的权值为这条边的权值，继续做Kruskal即可。 代码可能更优表现力：

```cpp
bool cmp(edge x,edge y)
{
    return x.w<y.w;
}
int fa[N],w[N],to;
int FindFather(int x)
{
    if(fa[x]==0) return x;
    return fa[x]=FindFather(fa[x]);
}
vector <int> e2[N];
void Kruskal()
{
    sort(e+1,e+1+m,cmp);
    to=n;
    for(int i=n+1;i<=2*n;i++)
        e2[i].reserve(4);
    for(int i=1;i<=m;i++)
    {
        int fa1=FindFather(e[i].s),fa2=FindFather(e[i].t);
        if(fa1==fa2) continue;
        w[++to]=e[i].w,fa[fa1]=to,fa[fa2]=to;
        e2[to].push_back(fa1),e2[to].push_back(fa2);
    }
}
```

* * *

# Kruskal重构树有什么性质

1.  **两个点在原图中的所有路径上某条路径中的最小值最大/最大值最小即为他们在Kruskal重构树上这两个点的LCA的权值**
2.  这是一个**二叉树**
3.  树上权值满足**堆**的性质 (从我们构建过程中可以很简单的证明)

* * *

# Kruskal重构树相关题目

1.[BZOJ 3732](https://www.lydsy.com/JudgeOnline/problem.php?id=3732) 真\*模板题 2.[NOI2018 归程](https://www.luogu.org/problemnew/show/P4768) 相关性质的简单应用

* * *

注：肥肠感谢julao lbc的教学