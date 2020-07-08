---
title: CentOS7下快速搭建LNMP环境
date: 2019-12-23 15:46:54
categories:
- 软件开发
- 软件部署
tags:
- LNMP
- -PHP
---

![.](/png/web/lnmp.jpg)
LNMP：Linux+Nginx+MySql+PHP

## 安装Nginx

Nginx的正确读法应该是Engine X。  
详细请参考我的另一篇文章: [CentOS下使用yum安装Nginx](/linux/nginx_setup/)。  
安装成功后，启动nginx应该就可以看到熟悉的欢迎界面.

```shell
#rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/
          nginx-release-centos-7-0.el7.ngx.noarch.rpm
#yum -y install nginx
#systemctl start nginx
```

![.](/png/web/welcome_nginx.png)

## 安装MariaDB

详细请参考我的另一篇文章:[CentOS7下使用yum安装MariaDB](/web/mariadb_setup/)。  
安装成功后，启动MariaDB；使用Navicat客户端连接数据库，应该就可以看到熟悉的连接成功提示信息。

```shell
#yum -y install mariadb-server
#systemctl start mariadb
```

![.](/png/web/navicat.png)

## 安装PHP7.2.24[remi版本]

### 配置yum源

安装php72w，是需要配置额外的yum源地址的。

- epel-release
- remi

```shell
yum install epel-release -y
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

注：  
remi源依赖epel-release源，安装之前必须先安装epel-release源。  
remi源版本更新比webtatic源快，并且是CentOS建议的yum源。

安装PHP和扩展包
PHP对于扩展库很多，根据需要安装PHP和相关扩展包。其中，cli、fpm、common和devel一般都要安装，其它模块看自己需要进行安装。

```shell
yum -y install php72
yum -y install php72-php-fpm php72-php-gd php72-php-json php72-php-mbstring
       php72-php-mysqlnd php72-php-xml php72-php-xmlrpc php72-php-opcache
       php72-php-crypt
```

启动php-fpm

```shell
#systemctl start php72-php-fpm
```

查看PHP版本，出现如下图提示，说明PHP已安装成功。可输入php72 -m命令，查看PHP已安装模块。

![.](/png/web/php72-v.png)

注：最好创建一个软链接，就可以直接执行”php -v”。

```shell
sudo ln -s /opt/remi/php72/root/usr/bin/php /usr/bin/php
```

## 安装PHP7.2.24[webtatic版本]

### 配置yum源4[webtatic版本]

安装php72w，是需要配置额外的yum源地址的。

- epel-release
- webtatic

```shell
yum install epel-release -y
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

注：webtatic源依赖epel-release源，安装之前，必须先安装epel-release源。

### 清除PHP历史版本

为了防止centos上面发生php冲突，清楚PHP历史版本。

```shell
yum -y remove php*
```

### 安装PHP和扩展包

PHP对于扩展库很多，根据需要安装PHP和相关扩展包。其中，cli、fpm、common和devel一般都要安装，其它模块看自己需要进行安装。

```shell
yum -y install php72w php72w-cli php72w-fpm php72w-common
    php72w-devel php72w-embedded php72w-gd php72w-mbstring
    php72w-mysqlnd php72w-opcache php72w-pdo php72w-xml
```

### 启动php-fpm

```shell
#systemctl start php-fpm
```

查看PHP版本，出现如下图提示，说明PHP已安装成功。可输入php -m命令，查看PHP已安装模块。

![.](/png/web/php-v.png)

## 配置nginx

切换到文件夹”/etc/nginx/conf.d/“，编辑文件default.conf。  
取消”location ~ .php”的注释，并修改root和fastcgi_param的值，具体如下：

```shell
... ...
location ~ \.php$ {
    root           /usr/share/nginx/html;
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
... ...
```

运行命令”nginx -s reload”，重新启动nginx。

切换至文件夹”/usr/share/nginx/html/“，创建文件info.php，内容如下：

```shell
<?php
phpinfo();
```

打开浏览器，输入该ngxin服务器的IP地址，可以看到熟悉的PHPInfo页面：

![.](/png/web/phpinfo.png)

## 设置开机启动

```shell
#systemctl enable nginx
#systemctl daemon-reload
#systemctl enable mariadb  
#systemctl daemon-reload        // webtatic版本
#systemctl enable php-fpm       // remi版本
#systemctl enable php72-php-fpm
#systemctl daemon-reload
```

## 服务启动、停止、状态查看

```shell
# 开启服务
#systemctl start nginx
#systemctl start mariadb  
#systemctl start php-fpm                // webtatic版本
#systemctl start php72-php-fpm          // remi版本
# 停止服务
#systemctl stop nginx
#systemctl stop mariadb  
#systemctl stop php-fpm                 // webtatic版本
#systemctl stop php72-php-fpm           // remi版本
# 查看状态
#systemctl status nginx
#systemctl status mariadb  
#systemctl status php-fpm               // webtatic版本
#systemctl status php72-php-fpm         // remi版本
```

## 常见错误

1. “File not found”错误  
请检查”/etc/nginx/conf.d/default.conf”文件中，root值是否修改正确，再检查fastcgi_param值是否已按要求修改;
2. “无法访问此网站”错误  
请检查CentOS防火墙80端口是否已设置为允许访问;

```shell
#配置防火墙，允许80，3306端口远程访问
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload  # 重启防火墙
```

## 常用路径

```shell
# nginx 配置文件
/etc/nginx/nginx.conf

# nginx 默认项目路径
/usr/share/nginx/html
```

## 参考

1、可供CentOS使用的软件库  
<https://wiki.centos.org/zh/AdditionalResources/Repositories>
