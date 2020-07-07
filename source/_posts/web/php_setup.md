---
title: VS Code配置PHP开发环境的方法
p: /web/php_setup
date: 2019-02-24 13:19:41
categories: 
- 软件开发
tags: 
- PHP
- VS Code
---

　　VS Code一个轻量且强大的代码编辑器，支持Windows，OSX和Linux。 内置JavaScript、TypeScript和Node.js支持，而且拥有丰富的插件生态系统，可通过安装插件来支持C++、C#、Python、PHP等其他语言。

　　这篇文章就为大家分享一下VS Code配置PHP开发环境的具体步骤，也作为作者配置PHP开发环境过程的备忘。注意了，VS Code和XAMPP都是跨平台，理论上，该教程同样适合OSX、Linux平台。

准备工作：
1.下载VS Code
前往VS Code官网的[下载页面][1]:
![](/png/web/vs_code.png)
2.下载xampp，因为套装省事，对于个人使用是很方便。
前往XAMPP官网的[下载页面][2]，sourceforge的[下载页面][3]:
![](/png/web/xampp_download.png)
3.下载xdebug，记得要对应php版本的，否则无效
前往Xdebug官网的[下载页面][4]，下载XAMPP对应版本的Xdebug动态链接库。比如，作者选择“xampp-portable-win32-7.3.2-0-VC15.7z”，对应下载7.3版本、32位、NTS的Xdebug动态链接库："php_xdebug-2.7.0RC2-7.3-vc15.dll"。(Xdebug加TS，标识线程安全)
![](/png/web/Xdebug.png)

安装步骤：
1.在VS Code安装php相关插件，在首选项里面配置php路径
![](/png/web/php_tools.png)
![](/png/web/php_setting.png)
2.安装Xdebug库
编辑[xampp安装目录]\xampp\php\php.ini，在文件末尾添加以下代码：
```
[xdebug]
zend_extension="[xampp安装目录]/xampp/php/ext/php_xdebug-2.5.5-7.1-vc14.dll"
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```
把Xdebug动态链接库拷贝至[xampp安装目录]\xampp\php\ext\目录下，运行php.exe -v，验证安装是否正确。你讲看到如下信息：
```
[xampp安装目录]\xampp\php>php -v
PHP 7.3.2 (cli) (built: Feb  5 2019 23:16:38) 
    ( ZTS MSVC15 (Visual C++ 2017) x86 )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.2, Copyright (c) 1998-2018 Zend Technologies
    with Xdebug v2.7.0RC2, Copyright (c) 2002-2019, by Derick Rethans
```
3. 最后就可以在VS Code里面设置断点调试了，注意，一定要以打开文件夹的形式才能成功设置断点调试，单个文件无效。
![](/png/web/php_debug_01.png)
![](/png/web/php_web_01.png)
![](/png/web/php_debug_02.png)
![](/png/web/php_web_02.png)


[1]:https://code.visualstudio.com/Download
[2]:https://www.apachefriends.org/download.html
[3]:https://sourceforge.net/projects/xampp/files/
[4]:https://xdebug.org/download.php
