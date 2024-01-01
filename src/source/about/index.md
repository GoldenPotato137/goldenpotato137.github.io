---
layout: docs
seo_title: 关于
bottom_meta: false
sidebar: []
twikoo:
  placeholder: 有什么想对我说的呢？
---

## About this blog
这里是GoldenPotato137的个人博客，主要用来记录一些个人记录，包括但不限于踩过的坑/做成与没做成的事/~~发电~~/一些想法。

如你所见，这里的文章上可追溯至2018年（因为我把之前反复搭建的博客网站内容搬过来/导入过来了。

这是这个博客的发展史（bushi
* **2018/04 - 2019/02** [博客园](https://www.cnblogs.com/GoldenPotato/?page=1)，记录自己的OI刷题记录，直到WC2019。
* **2019/02 - 2019/05** WC2019上不务正业天天折腾服务器，成果就是：[wordpress，启动](https://potatox.moe/2019/02/11/hello-world-1/)！ 记录自己省选期间的刷题记录，~~直到省选暴毙~~
* **2019/05 - 2020/02** 省选暴毙之后回归文化，狠狠开卷。期间只更新了AFO后记、CSP-S 2019暴力记
* 高考结束后开摆了，备份了一下网站之后就不管了
* **2021-02 - 2022-02** 大一年度项目学搞深度学习被狠狠折磨了，想到应该记录一下又重新买了个服务器。wordpress，启动！结果还是太摆了，简单写一点之后就没动了，乐。
* 摆！
* **2024-01** 被毕设(在cuda上计算一种零知识证明)与自己的一个winui3项目狠狠折磨了，又想到应该记录一下。联想到之前wordpress备份起来非常麻烦，觉得自己还是应该搞一个静态的博客。**hexo，启动！**

以下为这个网站的运行逻辑：

* 在本地的git仓库里面写文章，写好后push到github远程仓库
* 简单撸了[github action](https://github.com/GoldenPotato137/goldenpotato137.github.io/blob/master/.github/workflows/deploy.yml)，监听修改。当有修改的时候自动执行deploy动作：编译html到gh-pages分支，并发布到backup.potatox.moe(一个github page)。
* github action继续执行job：ssh到服务器上并执行git pull来获取更新过后的编译结果，这个服务器上跑的就是现在你看到的界面。

为什么要搞这么麻烦呢？我们分步拆解一下：
* 为什么要发布到backup.potatox.moe：因为我很摆，万一到时候又开摆了我可以直接cname到github page上接着奏乐接着舞（。
* 为什么要搞一个服务器：因为github page在国内访问奇慢无比


## About me

* GoldenPotato137， aka 土豆
* HITer, 计算数学大四在读。 准备继续在哈尔滨多呆5年（希望真只有五年）做高性能计算方向学习与研究
* 拉拉人 μ's forever！
* Galgame玩家，喜欢纯爱废萌 ~~（什么five~~
* (曾经)打OI 与 ACM （可惜疫情，一次线下都没去成（悲

很高兴认识你！