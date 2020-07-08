---
title: Win7下搭建C++ 编译环境(MSYS2+VSCode)
date: 2019-12-23 15:49:20
categories:
- 软件开发
- 软件部署
tags:
- C/Cpp
- VSCode
---

VS Code一个轻量且强大的代码编辑器，支持Windows，OSX和Linux。 内置JavaScript、TypeScript和Node.js支持，而且拥有丰富的插件生态系统，可通过安装插件来支持C++、C#、Python、PHP等其他语言。

　　这篇文章就为大家分享一下VS Code配置C/C++开发环境的具体步骤，也作为作者配置C/C++开发环境过程的备忘。

## 准备工作

1. 下载MSYS2  
前往MSYS2官网[下载页面](http://www.msys2.org/):  
![.](/png/windows/msys2.png)  
MSYS2安装请参考官网安装指南(64位系统只要安装MSYS2 MinGW 64-bit)。

2. 下载VS Code  
前往VS Code官网的[下载页面](https://code.visualstudio.com/Download):  
![.](/png/windows/vs_code.png)

## 安装步骤

1. 安装VS Code和MSYS2软件  
按VS Code和MSYS2官网安装指南进行安装。
2. 配置MSYS2  
国内镜像(清华大学开源软件镜像站)

```shell
pacman 的配置
编辑 /etc/pacman.d/mirrorlist.mingw32 ，在文件开头添加：

Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/mingw/i686
编辑 /etc/pacman.d/mirrorlist.mingw64 ，在文件开头添加：

Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/mingw/x86_64
编辑 /etc/pacman.d/mirrorlist.msys ，在文件开头添加：

Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/msys/$arch
然后执行 pacman -Sy 刷新软件包数据即可。
```

安装GNU工具包

```shell
pacman -S mingw-w64-i686-toolchain
pacman -S mingw-w64-x86_64-toolchain
```

设置环境变量

```shell
右键我的电脑-->属性-->高级系统设置-->点击环境变量-->选择Path-->编辑
```

验证开发环境

```shell
gcc --version
g++ --version
```

![.](/png/windows/gcc.png)

3. 配置VS Code  
安装C/C++扩展(微软)

![.](/png/windows/c_cpp_extension.png)

4. 环境验证  
编写Hello World程序

```shell
#include<iostream>
int main(void) {
    long int tag = __cplusplus;
    if(tag == 201703L) std::cout << "C++17\n";
    else if(tag == 201402L) std::cout << "C++14\n";
    else if(tag == 201103L) std::cout << "C++11\n";
    else if(tag == 199711L) std::cout << "C++98\n";
    else std::cout << "pre-standard C++\n"
    return 0;
}
```

配置tasks.json、launch.json、c_cpp_properties.json文件

5. 后续
以上是VS Code单文件的C/C++开发环境的搭建，适合写个HelloWorld程序；实际开发需要多文件开发环境，请继续关注！

## 备注

Cygwin、MinGw、mingw-w64,MSys msys2区别与联系

```conf
gcc.gnu.org
mingw-w64.org
msys2.org

a、从目标上说
MinGW 是让Windows 用户可以用上GNU 工具，比如GCC。
Cygwin 提供完整的类Unix 环境，Windows 用户不仅可以使用GNU 工具，理论 上Linux 上的程序
只要用Cygwin 重新编译，就可以在Windows 上运行。

b、从能力上说
如果程序只用到C/C++ 标准库，可以用MinGW 或Cygwin 编译。
如果程序还用到了POSIX API，则只能用Cygwin 编译。

c、从依赖上说
程序经MinGW 编译后可以直接在Windows 上面运行。
程序经Cygwin 编译后运行，需要依赖安装时附带的cygwin1.dll。
 
MSYS2是MSYS的一个升级版,准确的说是集成了pacman和Mingw-w64的Cygwin升级版, 提供了
bash shell等linux环境、版本控制软件（git/hg）和MinGW-w64 工具链。与MSYS最大的区别是
移植了 Arch Linux的软件包管理系统 Pacman(其实是与Cygwin的区别)
```
