---
title: CentOS下使用yum安装MariaDB
date: 2019-12-23 15:47:29
categories:
- 软件开发
- 软件部署
tags:
- MariaDB
- CentOS
---

首先让我们认识一下，MySQL的创始人Michael Widenius，1962年3月3日出生于芬兰赫尔辛基，MySQL的CTO及共同创始人。Monty Widenius撰写了MySQL的95%服务器端代码。

![.](/png/web/Michael_Widenius.jpg)

MariaDB由MySQL的创始人Michael Widenius（英语：Michael Widenius）主导开发，他早前曾以10亿美元的价格，将自己创建的公司MySQL AB卖给了SUN。此后，随着SUN被甲骨文收购，MySQL的所有权也落入Oracle的手中。MariaDB名称来自Michael Widenius的女儿Maria的名字。  
MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可 MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。在存储引擎方面，使用XtraDB（英语：XtraDB）来代替MySQL的InnoDB。

![.](/png/web/mariadb.png)

MariaDB直到5.5版本，均依照MySQL的版本。因此，使用MariaDB5.5的人会从MySQL 5.5中了解到MariaDB的所有功能。  
从2012年11月12日起发布的10.0.0版开始，不再依照MySQL的版号。10.0.x版以5.5版为基础，加上移植自MySQL 5.6版的功能和自行开发的新功能。

## 安装环境

有的centos7已经默认安装了Mariadb，可以查看自己的有没有安装，没有安装的再进行安装，已经安装了可以不用安装，也可以卸载了重装。

```shell
#卸载命令
# yum remove mariadb-server
# cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)
```

## 安装MariaDB

通过yum安装就行了。简单快捷，安装mariadb-server，默认依赖安装mariadb，一个是服务端、一个是客户端。

```shell
# yum -y install mariadb-server
```

## 配置MariaDB

- 安装完成后首先要把MariaDB服务开启，并设置为开机启动

```shell
# systemctl start mariadb  # 开启服务
# systemctl enable mariadb  # 设置为开机自启动服务
```

- 首次安装需要进行数据库的配置，命令都和mysql的一样

```shell
# mysql_secure_installation
```

- 配置选项说明

```shell
# 输入数据库超级管理员root的密码(注意不是系统root的密码)，
# 第一次进入还没有设置密码则直接回车
Enter current password for root (enter for none):  
Set root password? [Y/n]  # 设置密码，y
New password:  # 新密码
Re-enter new password:  # 再次输入密码
Remove anonymous users? [Y/n]  # 移除匿名用户， y
# 拒绝root远程登录，n，不管y/n，都会拒绝root远程登录
Disallow root login remotely? [Y/n]  
# 删除test数据库，y：删除。n：不删除，数据库中会有一个test数据库，一般不需要
Remove test database and access to it? [Y/n]  
Reload privilege tables now? [Y/n]  # 重新加载权限表，y。或者重启服务也许
```

- 测试是否能够登录成功，出现”MariaDB [(none)]>”， 就表示已经能够正常登录使用MariaDB数据库了。

```shell
# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;'or'\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

## 设置字符集

设置MariaDB字符集为utf-8，修改”/etc/my.cnf”，”/etc/my.cnf.d/client.cnf”，”/etc/my.cnf.d/mysql-clients.cnf”三个文件。

```conf
#/etc/my.cnf
在[mysqld]标签下添加
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake

#/etc/my.cnf.d/client.cnf
在[client]标签下添加
default-character-set=utf8

#/etc/my.cnf.d/mysql-clients.cnf
在[mysql]标签下添加
default-character-set=utf8

#重启服务
#systemctl restart mariadb
```

进入mariadb查看字符集

```shell
#配置字符集前
MariaDB [(none)]> show variables like "%character%";
show variables like "%collation%";
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.01 sec)

+----------------------+-------------------+
| Variable_name        | Value             |
+----------------------+-------------------+
| collation_connection | utf8_general_ci   |
| collation_database   | latin1_swedish_ci |
| collation_server     | latin1_swedish_ci |
+----------------------+-------------------+
3 rows in set (0.00 sec)

#配置字符集后
MariaDB [(none)]> show variables like "%character%";
show variables like "%collation%";
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)

+----------------------+-----------------+
| Variable_name        | Value           |
+----------------------+-----------------+
| collation_connection | utf8_unicode_ci |
| collation_database   | utf8_unicode_ci |
| collation_server     | utf8_unicode_ci |
+----------------------+-----------------+
3 rows in set (0.00 sec)
```

## 远程访问数据库

MariaDB默认是拒绝root远程登录的。  
先查看mysql数据库中的user表：

```shell
MariaDB [(none)]> select host,user from mysql.user;
+-----------------------+------+
| host                  | user |
+-----------------------+------+
| 127.0.0.1             | root |
| ::1                   | root |
| localhost             | root |
| localhost.localdomain | root |
+-----------------------+------+
5 rows in set (0.00 sec)
```

127.0.0.1，::1，localhost三个都是指本机；

- 给root用户添加远程访问权限

```shell
# 所有IP都可以使用root连接；"%"表示针对所有IP，"password"表示将用这个密码登录，
# 可以和本机登录root的密码不一样(root在不同IP登录，采用不一样的密码)。
MariaDB [(none)]> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED
BY 'password' WITH GRANT OPTION;
#只让某个IP段(192.168.1.xx)的主机连接，可以修改为"192.168.1.%"；
MariaDB [(none)]> GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.1.%'
IDENTIFIED BY 'password' WITH GRANT OPTION;

#刷新权限
MariaDB [(none)]> flush privileges;

#或者重启MariaDB服务也可以(二选一即可)
#systemctl restart mariadb
```

- 创建新的用户，并赋予远程访问权限

```shell
#创建用户user,可远程登陆,密码为"password"；
CREATE USER 'user'@'%' IDENTIFIED BY "password";
#*.*是指所有库的所有表，可以限制权限指定数据库，或指定数据库的哪些表
GRANT ALL PRIVILEGES ON *.* TO 'user'@'%' IDENTIFIED BY 'password' WITH
GRANT OPTION;
```

## 访问客户端

使用Navicat客户端进行访问MariaDB数据库;  
如果上面配置都没出错,你应该可以看到以下页面:
![.](/png/web/navicat.png)
