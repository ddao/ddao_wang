---
title: Centos7安装高版本Cmake
p: /linux/cmake
date: 2019-02-03 09:54:46
categories: 
- 软件开发
tags:
- Cmake
- Linux
---

环境：
- 代码编写：Windows 7 x64，Visual Studio 2015
- 运行环境：CentOS 7.6.1810 (查看CentOS版本：cat /etc/redhat-release)  

　　不知道有没有和我一样，一直混迹于Windows，然后转到Linux编程的同学。尽管网络上对微软产品的喷子很多，但习惯了在Visual Studio编写代码，切换至Linux实在不习惯。尝试了微软的VC_Linux插件，写个Hello World基本没什么问题，但如果用来项目开发，确实觉得它就是个小朋友的玩具而已。
　　好了，假设你已安装好VC_Linux更好的替代品VisualGDB，使用Visual Studio新建Linux项目，一步一步往下走。
![](/png/linux/01.png)
默认是MSBuild，都已经跨到Linux平台，还是选择CMake跨平台编译。
![](/png/linux/02.png)
![](/png/linux/003.png)
将会提示没有安装Cmake软件，输入密码后，VisualGDB将自动帮你安装(需要当前用户有执行sudo权限)。
![](/png/linux/004.png)
似乎一切顺利，点击"Finish"按钮。
![](/png/linux/005.png)
悲催了，CMake版本过低，虽然提示为3.8以上，最好是安装3.10以上版本。
![](/png/linux/06.png)
VisualGDB还是很人性化，点击确认按钮，弹出[解决办法的网页](https://visualgdb.com/documentation/cmake/annotations/)，可以跟着网页教程一步一步完成CMake的高版本安装。这里我想安装指定版本的CMake，步骤如下：  

- 下载Cmake，[下载列表](https://cmake.org/files/dev/?C=M;O=D)
```
wget https://cmake.org/files/dev/cmake-3.13.20190131-g92bf7.tar.gz
```
- 解压Cmake，看到这么长的文件名，是不是很担心打错了(使用Linux的Tab自动补全功能吧！用过一次，你就会喜欢上它)。
```
tar xvf cmake-3.13.20190131-g92bf7.tar.gz   
cd cmake-3.13.20190131-g92bf7  
```
- 编译安装cmake，编译过程耗时较长，喝杯茶，耐心等待吧。
```
./bootstrap
gmake
sudo gmake install
```
- 查看编译后的cmake版本
```
/usr/local/bin/cmake --version
```
- 移除原来的cmake版本
```
sudo yum remove cmake -y
```
- 新建软连接
```
sudo ln -s /usr/local/bin/cmake /usr/bin/
```
- 终端查看版本，确认安装无误。
```
$ cmake --version
cmake version 3.13.20190131
```
出现你安装的CMake版本表示成功！CMake编译、安装完成。

注：如果哪个步骤出错了，可能是没有安装相应的软件包，比如wget、gcc等。缺啥软件包就补安装啥软件包吧，然后继续跟着步骤走，祝一切顺利。

【TODO】创建新项目提示错误：
CMake exited with code 1
System.Exception: Failed to create a Unix domain socket. Please install ‘socat’ on your Linux machine via ‘sudo apt-get install socat’ or ‘yum install socat’