---
title: Ubuntu下忘记用户名和登录密码的解决方法
date: 2019-01-22 18:00:00
categories: 
- 软件开发
- 软件部署
tags: 
- Ubuntu
- 系统维护
---

![](/png/ubuntu_recovery/00.jpg)

### 环境 ###
- Ubuntu Server 16.04 x64

### 问题 ###

IT男在生活中总会被各种人误解，不懂IT的亲戚会认为IT行业就是修电脑的或者就是打字的，还有一些朋友会提出各种奇葩的请求，比如帮我写一个游戏外挂吧，帮我破解密码吧，帮我重装系统吧，帮我PS照片吧，甚至帮我清理一下垃圾吧....还有各种想什么就来什么的老板以及一天只能能够修改好几次需求的产品经理。  

IT男的生活中确实会面临着各种各样的难题。比如，你遇到过好久没登录的服务器，忘记密码了；或者同事离职，交接时忘记留下开发电脑的账号和密码。如果是Windows系统，IT男可能都有安装系统的U盘，一切还好办。如果是Linux系统，开机需要用户名和密码，这时怎么办呢？

- 其实，Linux类系统，只要能摸到服务器，进入系统比Windows还简单！

### 解决办法 ###

-  重新启动系统，直到出现启动菜单；

![](/png/ubuntu_recovery/01.png)
-  进入恢复模式(Recovery Mode)  
  -  **高级启动模式:** 选择“Advanced Options for Ubuntu”菜单，进入下一菜单页面；  
  -  **获取root权限:** 选择“Ubuntu，with Linux 4.4.0-87-generic(recovery mode)”菜单,按回车键，系统进入恢复模式，等待直到出现Recovery Menu界面。按向下方向键，选择Drop to root shell prompt ,也就是获取root shell权限。

![](/png/ubuntu_recovery/02.png)

- 修改用户密码  
passwd "user"  

- 查看用户名  
cat /etc/shadow  

- 建立新用户  
useradd user  

- 添加至sudo组
sudo usermod -aG sudo user  

- 如果以上命令执行出错，有可能是系统以只读模式加载，运行以下命令重新加载卷。
mount -o rw,remount /
