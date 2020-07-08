---
title: CentOS 7搭建本地yum源
date: 2020-07-07 11:26:05
categories:
- 软件开发
- 软件部署
tags:
- linux
- yum
---

![.](/png/linux/yum_01.jpg)

## 前言

　　大家在工作中肯定或多或少的会遇到使用yum源；比如前几天在部署CentOS测试环境，在安装各种中间件的时候，发现各种依赖都没有；后来一检查，发现安装的操作系统是CentOS Mini版，好吧，我认了； 为了完成测试环境的搭建，我就搭建了一个局域网的yum源。本来搭建本地的yum源也没有多大的难度，但是服务器都在内网，所以就需要搭建局域网的yum源。说到yum源搭建，主要有以下两种方式：

- 搭建本地yum源
- 搭建局域网yum源

　　无非就这两种，下面就分别对这两种yum源的搭建方式进行总结。

## 搭建本地yum源

　　本地yum源，就意味着只有搭建该yum源的这台服务器能使用，其它的服务器都不能使用该yum源。搭建本地yum源的所有步骤如下：

``` shell
# 在/mnt目录创建挂载镜像的文件夹
cd /mnt
mkdir iso

# 将iso镜像挂载到/mnt/iso目录
mount -o loop CentOS-7-x86_64-DVD-1810.iso /mnt/iso
# 如果iso镜像已经加载至CentOS系统，采用如下命令，加载光驱至/mnt/iso目录
mount -t iso9660 /dev/sr0 /mnt/iso
# 当然你也可以把所有文件都复制到/mnt/iso目录

# 挂载成功后可以使用df -h命令查看
[root@*.*.*.*]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   29G  5.3G   23G  19% /
devtmpfs                 484M     0  484M   0% /dev
tmpfs                    496M     0  496M   0% /dev/shm
tmpfs                    496M  6.8M  489M   2% /run
tmpfs                    496M     0  496M   0% /sys/fs/cgroup
/dev/sda1               1014M  132M  883M  13% /boot
tmpfs                    100M     0  100M   0% /run/user/0
tmpfs                    100M     0  100M   0% /run/user/1000
/dev/loop0               4.3G  4.3G     0 100% /mnt/iso

# 修改yum配置文件
cd /etc/yum.repos.d

# 先备份配置文件
mkdir bak
mv *.repo ./bak

# 将备份的CentOS-Media.repo文件拷贝出来
cp ./bak/CentOS-Media.repo ./

# 修改CentOS-Media.repo配置文件，修改后内容如下：
[c7-media]
name=CentOS-$releasever - Media
#baseurl=file:///media/CentOS/
#        file:///media/cdrom/
#        file:///media/cdrecorder/
baseurl=file:///mnt/iso/
gpgcheck=1
enabled=1  # 设置为1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

　　至此搭建本地yum源的操作完成，接下来我们清除原有的yum信息：

```shell
yum clean all
```

　　这样，一个崭新的本地yum源就可以使用了。

## 搭建局域网yum源

　　除了可以搭建本地yum源外，我们还可以基于HTTP方式配置yum源，这样我们就可以配置一个局域网内都可以使用的yum源。

　　一般都是通过Nginx来搭建HTTP的yum源，首先搭建Nginx，这里就不细说如何搭建Nginx了。接下来将iso镜像文件解压缩到一个目录。现在，我们就可以配置Nginx配置文件了。不建议直接修改nginx.conf，而是在/etc/nginx/conf.d/目录下创建一个新的conf文件，如yum.conf，文件内容如下：

```conf
server {
    listen       8080;
    server_name  localhost;
    location / {
        autoindex  on;
        root   /home/jelly/iso/; # （这里请换成你的实际目录路径）
    }
}
```

　　最后，在使用该yum源的机器上，我们开始配置yum的配置文件，在/etc/yum.repos.d新建一个名为Nginx-yum.repo的配置文件，内容如下：

```conf
[Nginx-yum]
name=Nginx-yum
# mirrorlist=http://mirrorlist.centos.org/?release=$releasever
# &arch=$basearch&repo=os&infra=$infra
baseurl=http://*.*.*.*:8080
enabled=1
gpgcheck=1
gpgkey=http://*.*.*.*:8080/RPM-GPG-KEY-CentOS-7
```

　　至此搭建局域网yum源的操作完成，接下来我们清除原有的yum信息：

```shell
yum clean all
```

　　这样，一个崭新的局域网yum源就可以使用了。

## 如何在yum源中，添加自定义rpm包

　　首先，看看有外网链接时，如何使用yum安装Nginx。[请移步我的另外一篇文章](/2019/10/31/linux/nginx_setup/)。  
　　比如，内网机器需要安装nginx服务器，想使用yum安装Nginx，如何把nginx包添加到本地yum源中呢？也许有人建议，使用reposync命令同步阿里yum源、网易yum源或者其它yum源，再复制到内网服务器上。网络上也有很多类似教程，真实施起来费时费力，而且需要很大的储存空间。  
　　这里介绍一种办法，既能使用yum解决软件依赖问题，又不用同步庞大的yum源。即使用yum的downloadonly插件来下载rpm包和依赖包。

```shell
# 下载安装yumdownloadonly插件
yum install yum-plugin-downloadonly

# 下载到指定目录（依赖包会一起下载）
yum install --downloadonly --downloaddir=/root/nginx nginx
```

　　把rpm包及依赖包通过光盘等方式复制到内网服务器上，制作本地yum源。

```shell
# 安装createrepo命令。
yum -y install createrepo
# 重建yum源的索引，/mnt/ios/目录下会多出一个repodata的目录
createrepo -v /mnt/ios/ 
# createrepo命令耗时较长，如果只是添加少量rpm包，可以使用--updata参数，
# 大大减少建立索引的时间。
createrepo --update /mnt/iso/
```

　　使用该局域网yum源的机器，执行以下命令，就可以更新yum源数据了。

```shell
#yum clean all
#yum makecache
```

## 总结

这篇文章详细的总结了两种搭建yum源的方法，并介绍了如何添加自定义的rpm包，以及建立yum源的rpm索引。通过这篇文章中总结的方法，足以应对我们日常工作中的需要。好了，东西不多，但是非常实用，希望我这里总结的内容对大家有帮助。
