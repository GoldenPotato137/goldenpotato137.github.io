---
title: "手把手带你入门GUIDE"
date: 2019-04-03 07:57:20
tags: "迁移"
---
<h1>什么是GUIDE</h1>
<blockquote><p>
  GUIDE(GAIT Universal IDE)是由北航GAIT研究组开发的、专门为NOI选手设计的轻型集成开发环境。GUIDE具有跨平台、操作简单、支持C/C++/Pascal三种语言和单文件编译调试等优点。经过近一年的试用和修改之后，GUIDE 1.0.1版目前正式发布。   ——www.noi.cn
</p></blockquote>
<p>换句话来说，GUIDE是一个NOI官方指定的，<strong>在NOI Linux系统上预装</strong>的一款pascal/c/c++ IDE(集成开发环境)。</p>
<hr />
<h1>GUIDE有什么优点</h1>
<p><del>我们GUIDE有一点好，就是出了什么bug，用可视化gdb调试调得比什么人都快</del></p>
<p>首先，GUIDE作为一款IDE，高亮，代码补全，括号匹配什么的肯定是应有尽有。<br />
但对于笔者来说，GUIDE最重要的功能莫过于它的可视化gdb调试，它可以方便地让我们定位bug的位置并解决问题。<br />
其次，GUIDE设计在windows系统下非常简洁美观。</p>
<p>况且，我们平时用的IDE和考场上用的IDE一模一样,可以有效避免因为不习惯的问题造成的翻车，心态爆炸。</p>
<hr />
<h1>GUIDE的基本使用</h1>
<h3>GUIDE的安装</h3>
<p>要使用GUIDE，显然，你必须要有一份GUIDE程序。考虑到读者们大多使用windows编程(<del>以及我并不会用新版ubuntu装GUIDE</del>)的问题，<strong>接下来的所有操作都在笔者的WINDOWS10系统下进行。如果有因系统不同而造成的报道上的偏差，请读者自行推广到其他的系统。</strong><br />
GUIDE可以到<a href="http://www.noi.cn/newsview.html?id=27&amp;hash=B7759F&amp;type=1" target="_blank"  rel="nofollow" >NOI官网下载</a><br />
但是考虑到GUIDE对MINGW的版本要求比较特殊的问题，为了方便大家使用GUIDE，我为大家特意打包了一份：<br />
<a href="https://nbcc3-my.sharepoint.com/:u:/g/personal/goldenpotato_cctv_admin_edu_pl/EQuLXvqje6tOhRjjS8NyG40BU8Is1B7TAGZrQVa0W7bEcQ?e=72TSog" target="_blank"  rel="nofollow" >OFB</a>（推荐）<br />
<a href="https://pan.baidu.com/s/1qo_9iN6u_Rk8zqEVaHNGuQ" target="_blank"  rel="nofollow" >度盘</a> 提取码: bbvq</p>
<p>下载完压缩包并解压之后，文件夹内应有以下文件：<br />
<img src="https://s2.ax1x.com/2019/04/03/Ac4DGd.png" alt="Ac4DGd.png" /></p>
<p>接下来，我们可以直接点开主程序GUIDE.exe<br />
打开之后，它应该会让你选择gcc,g++,fpc的目录。<strong>对于MinGW:</strong>我们直接选定<code>GUIDE/MinGW/bin</code>这个文件夹即可；<strong>对于fpc</strong>，请自行安装目录下带有的<code>fpc-2.2.2.i386-win32.exe</code>。</p>
<blockquote><p>
  为了让GUIDE的调试功能有效，请使用MinGW，如果您不需要gdb调试功能，可以选择MinGW_new
</p></blockquote>
<p><img src="https://s2.ax1x.com/2019/04/03/Ac4tr6.png" alt="Ac4tr6.png" /></p>
<p>这样子，我们的安装工作基本上就告一段落了。</p>
<hr />
<h3>GUIDE的基本设置</h3>
<p><del>俗话说得好，调好字体就是AC的一半。</del><br />
因此，我们可以在<code>编辑/选项/语法高亮显示/全部字体</code>中选择一个好看的字体及大小。<br />
<img src="https://s2.ax1x.com/2019/04/03/Ac4ozn.png" alt="Ac4ozn.png" /><br />
接下来，我们也可以在这里设置自动补全及括号匹配相关的问题。调整为自己习惯的设定即可。</p>
<p>我们发现GUIDE主程序界面下还有编译结果及文件夹两个额外的文本框。这些都是可以任意拖动的。<br />
<img src="https://s2.ax1x.com/2019/04/03/Ac5AoD.png" alt="Ac5AoD.png" /><br />
我一般习惯关闭文件夹这个文本框，将编译结果框拖到左边来，因为这样子方便调试代码。<br />
<img src="https://s2.ax1x.com/2019/04/03/AcIPpj.md.png" alt="AcIPpj.md.png" /></p>
<hr />
<h3>GUIDE的基本使用</h3>
<p>搞完上述事情后，我们就可以正常使用GUIDE啦。<br />
现在，我将以写<a href="https://www.luogu.org/problemnew/show/P1706" target="_blank"  rel="nofollow" >P1706 全排列问题</a>的事例来介绍如何使用GUIDE编辑，运行，调试一个程序。</p>
<p>首先，我们得创建一个新文件，左上角菜单<code>文件/新文件</code>或点击<img src="https://s2.ax1x.com/2019/04/03/AcI0gA.png" alt="AcI0gA.png" />或<code>CTRL+N</code>，来创建一个新的代码文件。<br />
接下来，<strong>我们必须先保存文件，这样GUIDE才知道要用什么语言的语法高亮</strong>。<br />
按下<code>CTRL+s</code>或点击<img src="https://s2.ax1x.com/2019/04/03/AcIIuq.png" alt="AcIIuq.png" />来保存文件<br />
<strong>注意，这里我们设置文件名时一定要打上后缀名，如c语言应该命名为：<code>xxx.c,c++: xxx.cpp,pascal: xxx.pas</code></strong><br />
<img src="https://s2.ax1x.com/2019/04/03/AcIbUU.png" alt="AcIbUU.png" /></p>
<p>接下来我们就可以在这个新文件里面打上我们的代码。<br />
对于这道题，我们可以发现这题我们做一个全排列即可完成。<br />
<img src="https://s2.ax1x.com/2019/04/03/AcoPUO.md.png" alt="AcoPUO.md.png" /></p>
<p>打完代码之后，你可以通过鼠标点击<img src="https://s2.ax1x.com/2019/04/03/AcoMa8.png" alt="AcoMa8.png" />编译你的代码，也可以通过按下<code>"F7"</code>来编译。<br />
在这里，我故意让程序有一点小问题无法通过编译。<strong>你可以通过双击编译结果中的某一个问题让你快速跳转到有问题的那一行。</strong>(截图并不能截到光标，大家意会一下光标在对应行即可)<br />
<img src="https://s2.ax1x.com/2019/04/03/AcoaZV.png" alt="AcoaZV.png" /><br />
在这里，我们发现我们漏写了一个分号，加上后就可以通过编译了。</p>
<p>接下来，我们可以通过鼠标点击<img src="https://s2.ax1x.com/2019/04/03/AcofIO.png" alt="AcofIO.png" />运行代码，也可以按下<code>CTRL+F5</code>来运行。<br />
我们输入样例：<br />
注意到，和DEVC++类似，GUIDE是会自动帮你在末尾加上“按任意键继续”来方便你的观察的。<br />
<img src="https://s2.ax1x.com/2019/04/03/Aco4iD.png" alt="Aco4iD.png" /></p>
<hr />
<h1>GUIDE的进一步使用</h1>
<p>接下来的才是笔者最想介绍的地方：GUIDE的可视化gdb调试功能</p>
<blockquote><p>
  如果您安装GUIDE时使用的是MinGW_new这个编译器，请在<code>设置/重设编译器路径</code>这里调整回MinGW以使用GUIDE的逐过程功能。（因为GUIDE并不资磁最新的编译器）
</p></blockquote>
<p>逐过程，顾名思义，我们可以按照我们的程序逐步逐步观察我们的代码的运行：<br />
通过按下<img src="https://s2.ax1x.com/2019/04/03/Ac7JH0.png" alt="Ac7JH0.png" />来进入gdb模式<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1pguyyg5sj31hc0sy0wo.jpg" alt="" /><br />
我们发现，在调试模式下，在行号标旁边出现了一个小蓝箭头，表示当前程序运行到了第几行。<br />
左边的编译信息框会自动切换到GDB信息框，同时，我们也可以通过点击小箭头以切换到不同的菜单夹并点击对应的按钮来进入对应的界面。<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1pgxb7vx2j307g05ddfo.jpg" alt="" /></p>
<p>接下来，由笔者来介绍如何逐过程：</p>
<h3>逐过程</h3>
<p>顾名思义，就是按照一个一个过程来进行你的程序。对于函数，它将不会带领你进入函数，而是直接运行完这个函数后继续(比如说，我现在要执行<code>cin</code>函数，它将不会带领我进入<code>iostream</code>库中cin的对应位置，而是直接运行完这个函数继续下一行)<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1pgyrpvhgj306i021web.jpg" alt="" /><br />
比如说，现在程序运行到了dfs这句话之前，如果按逐过程，则将直接执行完这个函数，然后进入下一行，而非把运行光标带到函数内部:<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph2eze4aj306601uq2r.jpg" alt="" /><br />
按下逐过程后，代码会执行到这一步：(即dfs函数完成之后的下一步)<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph3trl4dj31hc0ssjux.jpg" alt="" /></p>
<h3>逐语句</h3>
<p><img src="https://s2.ax1x.com/2019/04/03/AcHoz4.png" alt="AcHoz4.png" /><br />
顾名思义，按照一个一个语句来执行你的程序。<strong>它会带领你的程序进入函数</strong><br />
例如说：<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph2eze4aj306601uq2r.jpg" alt="" /><br />
按下逐语句后：<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1ph6ahrvdj30ap0dbdgj.jpg" alt="" /></p>
<p>跳出循环的功能我没用过，就不解释了。<br />
运行到光标处就是字面意思，也不多做解释。</p>
<h3>断点设置与运行到断点</h3>
<p>我们可以通过按下<code>F9</code>来在光标行设置断点(断点以红心表示)<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1phibvhqaj30cc06ojrm.jpg" alt="" /><br />
接下来，我们可以通过按下<code>F5</code>或<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1phj97bq8j307t01ia9v.jpg" alt="" /><br />
来让程序在断点处停下来(在此之前不能有别的中断事情，如别的断点，数据输入没有完成)</p>
<h3>变量查看</h3>
<p>我们在调试过程中，任何时刻都可以查看当前状态下每个变量的值，先点进变量查看栏<br />
<strong>可以通过按下那个加号来查看某个名字的变量的值/函数运行结果。</strong><br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1phc0i3zwj309k0qcq32.jpg" alt="" /><br />
也可以：先选定一段字符（变量名），右键：“添加到变量查看”<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1phdvx909j30bi05saa5.jpg" alt="" /><br />
效果如下：<strong>(对于长度大于500的数组，我们还能选定显示起始位置和终止位置)</strong><br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1phfsqqefj30890amgll.jpg" alt="" /><br />
对于结构体，我们还可以展开来看到所有的元素</p>
<h3>堆栈信息查看</h3>
<p>我们还可以查看我们程序的堆栈情况：<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1phh50fdxj30et038dfs.jpg" alt="" /></p>
<p>有了以上调试功能，相信读者一定能如虎添翼，更快的DE出自己程序的BUG，AC掉各种各样的神题。</p>
<hr />
<h1>Tips</h1>
<p>1.要让GUIDE的可视化调试能正常运行，必须使用5.1.4版本的(远古版本)的MinGW。但有时候，我们需要一些先进的编译器，可以暂时放弃调试，在<code>编辑/重设编译器路径</code>这里暂时设置为MinGW_new。这个时候，虽然不能调试，但是其他功能还是可以正常运行的。</p>
<p>2.请不要在展开巨大长度的数组时按下一步，这样操作很有可能导致GUIDE崩溃。</p>
<p>3.我们可以使用一切文本编辑常用快捷键<code>CTRL F、S、C、V、Z、Y</code>等</p>
<p>4.我们可以通过右键文件名来设置编译选项<br />
<img src="http://ww1.sinaimg.cn/large/0061bb0Wly1g1phqe10sgj306h04adft.jpg" alt="" /></p>
<hr />
<h1>后记</h1>
<p>希望大家能用好GUIDE，祝大家在OI赛事中取的理想的成绩！</p>
<p><del>以及别用DEVCPP了，来用GUIDE吧，DEVCPP的调试功能是在是emmmmmm</del><br />
当然，各位julao也可以使用各种各样更优秀的编辑器：如Emacas，Vim等。但是考虑到其陡峭的学习曲线，如果您不是julao的话，GUIDE一样能称为您的解题利器。</p>
