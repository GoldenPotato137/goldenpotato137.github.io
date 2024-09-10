---
title: 手把手带你入门GUIDE
tags: []
id: '405'
categories:
  - - 其他
date: 2019-04-03 15:57:20
updated: 2019-04-03 15:57:20
---

# 什么是GUIDE

> GUIDE(GAIT Universal IDE)是由北航GAIT研究组开发的、专门为NOI选手设计的轻型集成开发环境。GUIDE具有跨平台、操作简单、支持C/C++/Pascal三种语言和单文件编译调试等优点。经过近一年的试用和修改之后，GUIDE 1.0.1版目前正式发布。 ——www.noi.cn

换句话来说，GUIDE是一个NOI官方指定的，**在NOI Linux系统上预装**的一款pascal/c/c++ IDE(集成开发环境)。

* * *

# GUIDE有什么优点

我们GUIDE有一点好，就是出了什么bug，用可视化gdb调试调得比什么人都快 首先，GUIDE作为一款IDE，高亮，代码补全，括号匹配什么的肯定是应有尽有。 但对于笔者来说，GUIDE最重要的功能莫过于它的可视化gdb调试，它可以方便地让我们定位bug的位置并解决问题。 其次，GUIDE设计在windows系统下非常简洁美观。 况且，我们平时用的IDE和考场上用的IDE一模一样,可以有效避免因为不习惯的问题造成的翻车，心态爆炸。

* * *

# GUIDE的基本使用

### GUIDE的安装

要使用GUIDE，显然，你必须要有一份GUIDE程序。考虑到读者们大多使用windows编程(以及我并不会用新版ubuntu装GUIDE)的问题，**接下来的所有操作都在笔者的WINDOWS10系统下进行。如果有因系统不同而造成的报道上的偏差，请读者自行推广到其他的系统。** GUIDE可以到[NOI官网下载](http://www.noi.cn/newsview.html?id=27&hash=B7759F&type=1) 但是考虑到GUIDE对MINGW的版本要求比较特殊的问题，为了方便大家使用GUIDE，我为大家特意打包了一份： [OFB](https://nbcc3-my.sharepoint.com/:u:/g/personal/goldenpotato_cctv_admin_edu_pl/EQuLXvqje6tOhRjjS8NyG40BU8Is1B7TAGZrQVa0W7bEcQ?e=72TSog)（推荐） [度盘](https://pan.baidu.com/s/1qo_9iN6u_Rk8zqEVaHNGuQ) 提取码: bbvq 下载完压缩包并解压之后，文件夹内应有以下文件： ![Ac4DGd.png](https://s2.ax1x.com/2019/04/03/Ac4DGd.png) 接下来，我们可以直接点开主程序GUIDE.exe 打开之后，它应该会让你选择gcc,g++,fpc的目录。**对于MinGW:**我们直接选定`GUIDE/MinGW/bin`这个文件夹即可；**对于fpc**，请自行安装目录下带有的`fpc-2.2.2.i386-win32.exe`。

> 为了让GUIDE的调试功能有效，请使用MinGW，如果您不需要gdb调试功能，可以选择MinGW\_new

![Ac4tr6.png](https://s2.ax1x.com/2019/04/03/Ac4tr6.png) 这样子，我们的安装工作基本上就告一段落了。

* * *

### GUIDE的基本设置

俗话说得好，调好字体就是AC的一半。 因此，我们可以在`编辑/选项/语法高亮显示/全部字体`中选择一个好看的字体及大小。 ![Ac4ozn.png](https://s2.ax1x.com/2019/04/03/Ac4ozn.png) 接下来，我们也可以在这里设置自动补全及括号匹配相关的问题。调整为自己习惯的设定即可。 我们发现GUIDE主程序界面下还有编译结果及文件夹两个额外的文本框。这些都是可以任意拖动的。 ![Ac5AoD.png](https://s2.ax1x.com/2019/04/03/Ac5AoD.png) 我一般习惯关闭文件夹这个文本框，将编译结果框拖到左边来，因为这样子方便调试代码。 ![AcIPpj.md.png](https://s2.ax1x.com/2019/04/03/AcIPpj.md.png)

* * *

### GUIDE的基本使用

搞完上述事情后，我们就可以正常使用GUIDE啦。 现在，我将以写[P1706 全排列问题](https://www.luogu.org/problemnew/show/P1706)的事例来介绍如何使用GUIDE编辑，运行，调试一个程序。 首先，我们得创建一个新文件，左上角菜单`文件/新文件`或点击![AcI0gA.png](https://s2.ax1x.com/2019/04/03/AcI0gA.png)或`CTRL+N`，来创建一个新的代码文件。 接下来，**我们必须先保存文件，这样GUIDE才知道要用什么语言的语法高亮**。 按下`CTRL+s`或点击![AcIIuq.png](https://s2.ax1x.com/2019/04/03/AcIIuq.png)来保存文件 **注意，这里我们设置文件名时一定要打上后缀名，如c语言应该命名为：`xxx.c,c++: xxx.cpp,pascal: xxx.pas`** ![AcIbUU.png](https://s2.ax1x.com/2019/04/03/AcIbUU.png) 接下来我们就可以在这个新文件里面打上我们的代码。 对于这道题，我们可以发现这题我们做一个全排列即可完成。 ![AcoPUO.md.png](https://s2.ax1x.com/2019/04/03/AcoPUO.md.png) 打完代码之后，你可以通过鼠标点击![AcoMa8.png](https://s2.ax1x.com/2019/04/03/AcoMa8.png)编译你的代码，也可以通过按下`"F7"`来编译。 在这里，我故意让程序有一点小问题无法通过编译。**你可以通过双击编译结果中的某一个问题让你快速跳转到有问题的那一行。**(截图并不能截到光标，大家意会一下光标在对应行即可) ![AcoaZV.png](https://s2.ax1x.com/2019/04/03/AcoaZV.png) 在这里，我们发现我们漏写了一个分号，加上后就可以通过编译了。 接下来，我们可以通过鼠标点击![AcofIO.png](https://s2.ax1x.com/2019/04/03/AcofIO.png)运行代码，也可以按下`CTRL+F5`来运行。 我们输入样例： 注意到，和DEVC++类似，GUIDE是会自动帮你在末尾加上“按任意键继续”来方便你的观察的。 ![Aco4iD.png](https://s2.ax1x.com/2019/04/03/Aco4iD.png)

* * *

# GUIDE的进一步使用

接下来的才是笔者最想介绍的地方：GUIDE的可视化gdb调试功能

> 如果您安装GUIDE时使用的是MinGW\_new这个编译器，请在`设置/重设编译器路径`这里调整回MinGW以使用GUIDE的逐过程功能。（因为GUIDE并不资磁最新的编译器）

逐过程，顾名思义，我们可以按照我们的程序逐步逐步观察我们的代码的运行： 通过按下![Ac7JH0.png](https://s2.ax1x.com/2019/04/03/Ac7JH0.png)来进入gdb模式 ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1pguyyg5sj31hc0sy0wo.jpg) 我们发现，在调试模式下，在行号标旁边出现了一个小蓝箭头，表示当前程序运行到了第几行。 左边的编译信息框会自动切换到GDB信息框，同时，我们也可以通过点击小箭头以切换到不同的菜单夹并点击对应的按钮来进入对应的界面。 ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1pgxb7vx2j307g05ddfo.jpg) 接下来，由笔者来介绍如何逐过程：

### 逐过程

顾名思义，就是按照一个一个过程来进行你的程序。对于函数，它将不会带领你进入函数，而是直接运行完这个函数后继续(比如说，我现在要执行`cin`函数，它将不会带领我进入`iostream`库中cin的对应位置，而是直接运行完这个函数继续下一行) ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1pgyrpvhgj306i021web.jpg) 比如说，现在程序运行到了dfs这句话之前，如果按逐过程，则将直接执行完这个函数，然后进入下一行，而非把运行光标带到函数内部: ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph2eze4aj306601uq2r.jpg) 按下逐过程后，代码会执行到这一步：(即dfs函数完成之后的下一步) ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph3trl4dj31hc0ssjux.jpg)

### 逐语句

![AcHoz4.png](https://s2.ax1x.com/2019/04/03/AcHoz4.png) 顾名思义，按照一个一个语句来执行你的程序。**它会带领你的程序进入函数** 例如说： ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph2eze4aj306601uq2r.jpg) 按下逐语句后： ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph6ahrvdj30ap0dbdgj.jpg) 跳出循环的功能我没用过，就不解释了。 运行到光标处就是字面意思，也不多做解释。

### 断点设置与运行到断点

我们可以通过按下`F9`来在光标行设置断点(断点以红心表示) ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1phibvhqaj30cc06ojrm.jpg) 接下来，我们可以通过按下`F5`或![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1phj97bq8j307t01ia9v.jpg)  
来让程序在断点处停下来(在此之前不能有别的中断事情，如别的断点，数据输入没有完成)

### 变量查看

我们在调试过程中，任何时刻都可以查看当前状态下每个变量的值，先点进变量查看栏 **可以通过按下那个加号来查看某个名字的变量的值/函数运行结果。** ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1phc0i3zwj309k0qcq32.jpg) 也可以：先选定一段字符（变量名），右键：“添加到变量查看” ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1phdvx909j30bi05saa5.jpg) 效果如下：**(对于长度大于500的数组，我们还能选定显示起始位置和终止位置)** ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1phfsqqefj30890amgll.jpg) 对于结构体，我们还可以展开来看到所有的元素

### 堆栈信息查看

我们还可以查看我们程序的堆栈情况： ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1phh50fdxj30et038dfs.jpg) 有了以上调试功能，相信读者一定能如虎添翼，更快的DE出自己程序的BUG，AC掉各种各样的神题。

* * *

# Tips

1.要让GUIDE的可视化调试能正常运行，必须使用5.1.4版本的(远古版本)的MinGW。但有时候，我们需要一些先进的编译器，可以暂时放弃调试，在`编辑/重设编译器路径`这里暂时设置为MinGW\_new。这个时候，虽然不能调试，但是其他功能还是可以正常运行的。 2.请不要在展开巨大长度的数组时按下一步，这样操作很有可能导致GUIDE崩溃。 3.我们可以使用一切文本编辑常用快捷键`CTRL F、S、C、V、Z、Y`等 4.我们可以通过右键文件名来设置编译选项 ![](http://ww1.sinaimg.cn/large/0061bb0Wly1g1phqe10sgj306h04adft.jpg)

* * *

# 后记

希望大家能用好GUIDE，祝大家在OI赛事中取的理想的成绩！ 以及别用DEVCPP了，来用GUIDE吧，DEVCPP的调试功能是在是emmmmmm 当然，各位julao也可以使用各种各样更优秀的编辑器：如Emacas，Vim等。但是考虑到其陡峭的学习曲线，如果您不是julao的话，GUIDE一样能称为您的解题利器。