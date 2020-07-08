---
title: Windows远程桌面(mstsc)不能复制粘贴的解决办法
date: 2020-07-07 11:17:18
categories: 
- 软件开发
- 软件部署
tags:
- Windows
---

![.](/png/windows/windows10-laptop.png)

原来通过mstsc远程连接Windows能够直接在本地和服务器之间复制、粘贴一些文字和文件，最近突然无法直接复制粘贴了，只能通过远程映射的本地磁盘方式交换文件，太为繁琐。

首先，已经确认远程桌面连接选项“本地资源→本地设备和资源→剪贴板”中勾选了剪贴板。

![.](/png/windows/rdpclip-01.png)

解决办法：
在远程Windows服务器中重启“rdpclip.exe”进程即可。

1、打开资源管理器，杀掉rdpclip.exe进程；

![.](/png/windows/rdpclip-02.png)

2、开始→运行，输入：rdpclip.exe，回车重启该进程。

![.](/png/windows/rdpclip-03.png)

3、此时粘贴文件或者文字发现已经可以粘贴了。
