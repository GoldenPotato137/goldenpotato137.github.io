**欢迎！**

这里是[GoldenPotato137的小屋](https://goldenpotato.cn)的源代码&部署文件仓库。

这个博客是使用hexo搭建的，你可以在`./src`里面找到网站的源代码。

网站目前部署在[github page](https://goldenpotato137.github.io)（作为备份）与[我的服务器](https://goldenpotato.cn)上

你可以在`.github/workflows/deploy.yml`里面找到自动部署相关的github action

> 简单介绍一下我的自动化流程，主要由3个job组成
> 
>【编译】调用hexo的deploy命令，将源代码编译并push至gh-pages分支
>
>【deploy1】将gh-pages分支内的内容发布到github pages上
>
>【deploy2】ssh到我的服务器中，pull最新的gh-pages分支

部署后的文件可以在`gh-pages`找到
