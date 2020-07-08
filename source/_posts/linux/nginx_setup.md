---
title: CentOS下使用yum安装Nginx
date: 2020-07-07 14:36:22
categories:
- 软件开发
- 软件部署
tags:
- nginx
- linux
---

　　首先让我们认识一下，Nginx的创始人伊戈尔·赛索耶夫

![.](/png/linux/nginx.jpg)

　　Nginx 可以在大多数 UnixLinux OS 上编译运行，并有 Windows 移植版。 Nginx 的1.4.0稳定版已经于2013年4月24日发布，一般情况下，对于新建站点，建议使用最新稳定版作为生产版本，已有站点的升级急迫性不高。Nginx 的源代码使用 2-clause BSD-like license。  
　　Nginx 是一个很强大的高性能Web和反向代理服务，它具有很多非常优越的特性：  
　　在连接高并发的情况下，Nginx是Apache服务不错的替代品：Nginx在美国是做虚拟主机生意的老板们经常选择的软件平台之一。能够支持高达 50,000 个并发连接数的响应，感谢Nginx为我们选择了 epoll and kqueue作为开发模型。  

![.](/png/linux/nginx_logo.png)

当使用以下命令安装Nginx时，发现无法安装成功。

```shell
yum install -y nginx
```

需要做一点处理。

安装Nginx源
执行以下命令：

```shell
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/
nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

安装该rpm后，我们就能在/etc/yum.repos.d/ 目录中看到一个名为nginx.repo 的文件。

安装Nginx  
安装完Nginx源后，就可以正式安装Nginx了。

```shell
yum install -y nginx
```

Nginx默认目录  
输入命令：

```shell
whereis nginx
```

即可看到类似于如下的内容：

```shell
nginx: /usr/sbin/nginx /usr/lib64/nginx /etc/nginx /usr/share/nginx
```

以下是Nginx的默认路径：  
(1) Nginx配置路径：/etc/nginx/  
(2) PID目录：/var/run/nginx.pid  
(3) 错误日志：/var/log/nginx/error.log  
(4) 访问日志：/var/log/nginx/access.log  
(5) 默认站点目录：/usr/share/nginx/html  

事实上，只需知道Nginx配置路径，其他路径均可在/etc/nginx/nginx.conf 以及/etc/nginx/conf.d/default.conf 中查询到。

常用命令  

```shell
(1) 启动：
nginx

(2) 测试Nginx配置是否正确：
nginx -t

(3) 优雅重启：
nginx -s reload

(4) 查看nginx的进程号：
ps -ef |grep nginx

(5)nginx服务停止
nginx -s stop
kill -9 pid
```

当然，Nginx也可以编译源码安装，步骤相对要繁琐一些。总的来说，使用yum安装Nginx还是比较简单的，如无特殊需求，Nginx完全没有必要源码编译安装。
