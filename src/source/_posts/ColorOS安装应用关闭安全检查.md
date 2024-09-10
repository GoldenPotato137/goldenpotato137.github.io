---
title: ColorOS关闭应用安装安全检查
date: 2024-07-11 23:22:51
updated: 2024-07-11 23:22:51
tags:
---
如果你擅长使用adb，请直接跳转到STEP5

## STEP1 下载ADB
如果你电脑上有ADB可以跳过此步

在google官网上下载adb
https://dl.google.com/android/repository/platform-tools-latest-windows.zip?hl=zh-cn

将其解压到你喜欢的位置即可，建议把解压出来的目录加到path里面方便powershell里面直接调用adb而不是夹带一长串的完整路径
![](/images/ColorOS安装应用关闭安全检查_234000.png)

可选步骤：把adb添加到path里：![](/images/ColorOS安装应用关闭安全检查_233455.png)
如何添加环境变量: https://jingyan.baidu.com/article/47a29f24610740c0142399ea.html

后续步骤会假设你已经把adb添加到path里了。如果你没有添加并且在后续步骤中出现了类似`无法将“adb”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正确，然后再试一次。`的报错并且你不知道这个报错是什么意思，请把adb添加到path里来解决这个报错。

## STEP2 开启开发者模式
https://jingyan.baidu.com/article/ca00d56c49a8b1a89eebcfe8.html

## STEP3 打开无线调试
操作前请确保手机与电脑在同一网络环境下（比如说连上相同的wifi）

设置/其他设置/开发者选项/无线调试
![](/images/ColorOS安装应用关闭安全检查_234301.png)

记下里面的ip地址与端口
在我的例子中，ip地址与端口为：192.168.20.127:38143

## STEP4 使用ADB链接手机
按下`WIN+R`打开运行窗口，输入`wt`后回车
> 如果显示你的电脑上没有wt，那么可以改为输入：powershell

在打开的窗口中输入`adb connect 刚刚你看到的ip:端口`
![](/images/ColorOS安装应用关闭安全检查_234822.png)

## STEP5 禁用绿厂的软件安装器与安全管家

禁用安全管家：`adb shell pm disable-user com.coloros.phonemanager`
![](/images/ColorOS安装应用关闭安全检查_234951.png)

禁用软件安装器：`adb shell pm disable-user com.oplus.appdetail`
![](/images/ColorOS安装应用关闭安全检查_235020.png)

DONE! 现在可以自由无阻地安装软件啦，撒花~