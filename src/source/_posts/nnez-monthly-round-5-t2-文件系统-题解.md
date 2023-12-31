---
title: '[NNEZ Monthly Round 5 T2] 文件系统 题解'
tags:
  - 数据结构
id: '355'
categories:
  - - LCT
  - - NNEZ
  - - 模拟
date: 2019-03-19 12:02:37
---

# 题面

[洛谷团队题（尚未入团的请尽快加入团队）](https://www.luogu.org/problemnew/show/T72049)

* * *

# 梗

$\\huge μ'sic\\ Forever!$ 114514.avi.zip 1919810 （(懂的人自然懂(确信)) [真寻酱实在是太可爱了](https://zh.moegirl.org/%E5%88%AB%E5%BD%93%E6%AC%A7%E5%B0%BC%E9%85%B1%E4%BA%86) ⑨个样例(我够良心吧)

* * *

# 真\*Solution

稍有常识的人都可以看出,**一个文件目录在任意时刻都是一个树的形式**（如果没有快捷方式的话）。 像这样：（样例） ![AnDGBd.png](https://s2.ax1x.com/2019/03/19/AnDGBd.png)

### 解法一

我们来看看type=1啥意思.... op!=3 and op!=5 and 名字为数字? emmmm？ 因为OP！=5，树上已经插入的边是不会变的，因此，我们考虑用vector/链式前向星/领接表把这棵树存下来。 首先，我们要记录每个点的信息：**它的size，是文件还是文件夹,它的父亲是什么** 每次我们查询size的时候直接dfs遍历一遍子树来计算即可。 啥？你说怎么处理Error？大力分类讨论，请。 其实很是很好讨论的qwq。 啥？你不会输入？一时数字一时字符串怎么办？**那请全部使用字符串的形式读入，再检测是否为数字，如果为数字的话，手动计算成数字即可。** 啥？你说不会读入字符串？爆零,请。[NOIp2018 普及组T1 标题统计](https://www.luogu.org/problemnew/show/P5015)，请 时间复杂度$O(n^2)$ 恭喜你，获得了10分的好成绩 .

### 解法二

emmm，少了个op!=3，用人话来说，就是允许改名了。 emmm？这分简直就是送出去的啊喂。 我们思考一下，改名会造成什么问题： 1. **因为我们用名字来作为记录孩子的关键字，因此会导致孩子发生改变->直接修改vector/领接表/链式前向星的对应的边即可** 2. **要对应地创造出一个新的点并且复制原有的信息，把原有的点删除掉（删除掉标记即可）。->4遍赋值：赋值fa，size，type，vis** 3. 没了 时间复杂度$O(n^2)$ 恭喜你，获得了20分的好成绩 .

### 解法三

emmm，这次不改名了.....但是TM的要改父亲。 这就肥肠尴尬了.......改父亲涉及到某个点删除某个孩子，很不幸的是，这种操作在vector/链式前向星中是很难做到的，即使强行做了，时间复杂度也很高。 怎么办呢？我们不妨换个思路：**我们在每次插入一个新的文件的时候，计算它的“贡献”**。 什么叫贡献呢？下面这个图可以很清晰的说明： ![AncpSP.png](https://s2.ax1x.com/2019/03/19/AncpSP.png) 也就是说，我们插入一个大小为$x$的文件，会导致它的父亲及祖先在查询$size$的时候，查询出来的答案增加$x$（因为多了这一个文件嘛） 因此，我们可以直接记录查询某个节点时的答案（$size,cnt$），**每次查询的时候直接输出它的答案。每次插入新的文件的时候，我们直接暴力向上更新他的父亲及祖先的答案。修改的时候，我们也是先暴力修改他的父亲及祖先的答案，然后改变$fa$，再暴力修改新的父亲及祖先的答案** 这样，我们就只用记录每个点的父亲，而无需记录它的孩子是什么，解决了无法处理链式前向星/vector不方便删除的问题。 时间复杂度$O(n^2)$ 恭喜你，获得了40分的好成绩。 .

### 解法四

改名和改父亲一起上....... emmmmmm？这分也是送的啊喂。 我们这里有两种做法，一是把上面两个做法拼接到一起$O(n^2)$，二是我们把原来的每个点做一层“映射”。在这里，我主要讲第二个做法，因为后面可能要用到。 什么叫再做一次映射呢？考虑这么做：**我们给每一次加文件/文件夹操作产生的新文件再赋予一个编号，对于这个点的所有记录全部在这个编号下标下的数组操作。**这样子，即使我们改变了一个点的名字，也仅仅只是改变点的名字->编号的映射关系而已，我们只要把旧的名字->编号的映射关系先删掉，然后再新增一个新的名字->编号的映射关系即可。 比如说，原来有一个文件夹叫"192608"，它所对应的点的编号为17，我们称这种关系为"192608"->17的映射。那如果我们把这个文件夹名字修改为"114514"，我们只需要把原有的映射关系删除，然后再新添加一条"114514"->17的映射即可。 文件名长度不超过6，因此**这个映射关系可以直接使用一个数组来存**。 时间复杂度$O(n \\cdot 100)$（树是随机的，最多只有100层） 恭喜你，获得了75分的好成绩。 .

### 解法五

这次文件名不全是数字了....... 这...... 这不是直接换成字符串就好了吗？ 之前我们提到的映射关系是数字到数字的映射，**这里我们只用改为字符串到数字的映射即可**。 因为文件名长度不超过25，因此**这个映射关系可以用char\[N\]\[25\]**的方式来存。 每次输入一个文件名我们暴力匹配即可。 期望时间复杂度$O(n \\cdot 100)$ 干得不错，你又成功A掉了一道水题！

* * *

# End？

![An2bLT.png](https://s2.ax1x.com/2019/03/19/An2bLT.png) . . . .

### 解法五

你是不是觉得这是一道辣鸡题，数据点分布毫无意义？ . ![AnRhnK.png](https://s2.ax1x.com/2019/03/19/AnRhnK.png) ![AnR57D.png](https://s2.ax1x.com/2019/03/19/AnR57D.png) 没错，这个菜鸡故意卡了暴力匹配。那四个点全部都是字符集只有“a,b”的串，导致失配长度都特别长，直接TLE。 所以说，时间复杂度为$O(n\\cdot 100 \\cdot 25)$ 恭喜你，拿到了80分的好成绩！

* * *

### 解法六

所以说这毒瘤出题人出的毒瘤数据怎么处理啊啊啊啊 ![AnRq1I.png](https://s2.ax1x.com/2019/03/19/AnRq1I.png) OK，我们来回忆一下字符串匹配算法有什么？暴力匹配，Trie树，KMP，哈希，AC自动机，后缀自动机 考虑到我们这里是多模式串，多匹配串，KMP，AC自动机失去了用处。 因此，这题我们可以考虑用Trie树来处理匹配问题。 当然，这个菜鸡出题者作为暴力党，肯定是不会写一个Trie树来干这种破事的。因此，我们考虑用hash来解决这个问题。 我们直接对每个串大力hash，算出来的hash结果再用刚刚的映射方法映射到编号。酱紫，我们可以用两层映射来解决这个破问题。 怎么存哈希值->原编号这个映射呢？这就是个技术活了。因为我们这里的hash值很大，很难用一个数组存下来。 这时候，我们有两种解决办法： 1. 哈希表 2. map 菜鸡出题人作为暴力主义的一份子，是懒得写hash表的。因此我们直接用map存每个字符串到编号的映射即可。 啥？map怎么用？[看这个](https://www.cnblogs.com/fnlingnzb-learner/p/5833051.html) 时间复杂度$O(n\*100)$ 干的不错，你又成功地A掉了一道水题。 **代码在这里**

```cpp
#include<iostream>
#include<string>
#include<map>
#include<cstdio>
using namespace std;
const int N=100000+1000;
map <string,int> emap;
int n,m;
string str[N];
int to,fa[N],type[N],cnt[N];//type:0:文件夹；1：文件
long long size[N];
void update(int now,int x,int x2)
{
    do
    {
        now=fa[now];
        size[now]+=x;
        cnt[now]+=x2;
    }while(now!=0);
}
bool Check(int x1,int x2)//检验x2是否为x1的子文件夹
{
    do
    {
        x2=fa[x2];
        if(x1==x2) 
            return true;
    }while(x2!=0);
    return false;
}
int main()
{
//  freopen("file7.in","r",stdin);
//  freopen("file7a.out","w",stdout);

    scanf("%d%d",&n,&m);

    emap["root"]=0;
    str[0]="root";
    for(int i=1;i<=n;i++)
    {
        //cerr<<i<<endl;
        int op; 
        scanf("%d",&op);

        if(op==1)
        {
            string s1,s2;
            cin>>s1>>s2;
            if(emap.count(s1)==0 or type[emap[s1]]==1 or emap.count(s2)!=0)
            {
                printf("Error\n");
                continue;
            }
            emap[s2]=++to,type[to]=0,fa[to]=emap[s1];
            str[to]=s2;
            printf("Success\n");
        }

        if(op==2)
        {
            string s1,s2;
            int x;
            cin>>s1>>s2>>x;
            if(emap.count(s1)==0 or type[emap[s1]]==1 or emap.count(s2)!=0)
            {
                printf("Error\n");
                continue;
            }
            emap[s2]=++to,type[to]=1,fa[to]=emap[s1],size[to]=x,cnt[to]=1;
            str[to]=s2;
            update(to,size[to],1);
            printf("Success\n");
        }

        if(op==3)
        {
            string s1,s2;
            cin>>s1>>s2;
            if(emap.count(s1)==0 or emap.count(s2)!=0)
            {
                printf("Error\n");
                continue;
            }
            emap[s2]=emap[s1];
            emap.erase(s1);
            str[emap[s2]]=s2;
            printf("Success\n");
        }

        if(op==4)
        {
            string s1;
            cin>>s1;
            if(emap.count(s1)==0 or type[emap[s1]]==1)
            {
                printf("Error\n");
                continue;
            }
            printf("%d\n",cnt[emap[s1]]);
        }

        if(op==5)
        {
            string s1,s2;
            cin>>s1>>s2;
            if(emap.count(s1)==0 or emap.count(s2)==0 or type[emap[s2]]==1 or s1==s2 or Check(emap[s1],emap[s2])==true)
            {
                printf("Error\n");
                continue;
            }
            update(emap[s1],-size[emap[s1]],-cnt[emap[s1]]);
            fa[emap[s1]]=emap[s2];
            update(emap[s1],size[emap[s1]],cnt[emap[s1]]);
            printf("Success\n");
        }

        if(op==6)
        {
            string s1;
            cin>>s1;
            if(emap.count(s1)==0 or type[emap[s1]]==1)
            {
                printf("Error\n");
                continue;
            }
            printf("%lld\n",size[emap[s1]]);
        }

        if(op==7)
        {
            string s1;
            cin>>s1;
            if(emap.count(s1)==0 or type[emap[s1]]==0)
            {
                printf("Error\n");
                continue;
            }
            cout<<str[fa[emap[s1]]]<<endl;
        }
    }

    return 0;
}
```

* * *

# True End？

事实上，上面的那个做法已经可以成功的A掉这道题了。 但是，这题的数据是随机的，总让人有水题的感觉，那我们加强一下？我们数据不随机。 ....

### 解法七

这里需要用到高级数据结构的知识，NOIp选手们请绕道行车/小心驾驶。翻车了也不要紧，我敢打包票NOIp绝对不会考这种数据结构（5年内） 我们发现，这里动态连边是不是让人有一种LCT的感觉？ 对了！这题的确可以用LCT来解决.......吗？ 这题维护子树？LCT能维护子树？ LCT当然不能维护子树啊。问题是，这题看上去需要维护子树，实际上真的需要吗？ 考虑我们维护贡献的时候，是维护它到根的一条链的。 诶？因此，LCT是可以通过维护链来间接解决这道题的。我们直接在每个点上对应的维护它的$size,cnt$,连边的时候暴力打标记，暴力pushdown标记即可。 啥？你说你不会LCT？如果你会splay并且把NOIp的芝士都差不多过了一遍，可以[戳这里](https://www.goldenpotato.cn/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/lct%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/) 时间复杂度：真·$O(nlogn)$（但是因为splay常数巨大，这里的复杂度我们基本上可以再乘上一个$logn$）

* * *

# True End

事实上呢，这题的确比NOIp中的模拟题稍微难那么一点点，但是把我们学过的知识综合起来，还是勉强可以做的。如果你仔细观察数据点的分布，你会发现送了很多很多的部分分，**而且数据点的数据范围是对你的解题有引导作用的，它会逐步带领你走向正解**。 以及，真寻酱很感谢你帮她写的文件系统，并奖励了你一瓶神秘小♂药♂水 ![Anfcsx.png](https://s2.ax1x.com/2019/03/19/Anfcsx.png)