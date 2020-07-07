---
title: 在Windows下配置HackRF One环境
date: 2019-02-01 09:44:14
categories: 
- 无线电
- 开发环境
tags: 
- HackRF
---

![](/png/hackrf/00.png)
　　初次接触到HackRF One是2014年暑假，CSDN正式推出“开源夏令营” 技术公益活动。对于工作多年的我，实际上能接触到这个活动，也是意外。主要原因是平常也常常混迹于CSDN，另外，当时伪基站比较猖獗，知己知彼，在网络搜索无意间，看到这条消息。  

多年过去了，现在只在[hackrf.net]找到当时的时间安排和hackrf的项目提案：
![](/png/hackrf/csdn_hackrf.png)

　　扯远了，言归正传；先利用HackRF接收无线广播FM信号，验证HackRF版是完好无损的。有硬件的同学，一步一步跟着做，保证你能收到FM解调的音频信号。

- 通过USB连接HackRF和PC，并使用zadig安装驱动。如果识别到设备HackRF One，说明驱动已经安装好了。  
![](/png/hackrf/zadig.png)

- **安装SDR软件：**现在还不是编码开发的时候，先验证设备完好尝尝鲜。  
　　谈到Windows下的SDR软件，大家肯定会想到SDR#，今天给大家介绍PothosSDR，安装包下载：[PothosSDR]。至于为什么选择它，具体原因后面会谈到。安装完毕，可以在开始菜单看到有多个的工具包。
![](/png/hackrf/pothos_sdr.png)
打开CubicSDR工具包，选择HackRF One设备，设置中心频率91.3MHz，就可以看到文章开始的截图，以及FM调频广播音频信号。  

　　HackRF One如果只是拿来收听调频广播，那可就是大材小用。这块开发板可以拿来在很多场合都能发挥出关键作用的。作为一款开源硬件，首先，我们访问GitHub的[HackRF项目]，认真阅读一遍[wiki页]。因为你在使用过程中，可能遇到的种种问题，很可能已经有人遇到了，并在wiki中记录解决的方法。  
　　接着，我们就要进行HackRF的编译工作：  
- **下载源码：**源码下载，可以使用git cloent，或者直接下载源码包；如果是更新固件，建议下载作者的预编译包，在firmware-bin目录有编译好的固件升级包。
![](/png/hackrf/hackrf_git_clone.png)
源码包目录结构：
![](/png/hackrf/hackrf_files.png)

- **源码编译：**我的开发环境如下：  
  - Win7 x64
  - Visual Studio 2015
  - CMakek 3.0+，libusb 1.0，pthreads 2.9.1，fftw 3.3.5
以上环境供大家参考，如果编译过程中出现错误，大家可以根据错误信息实际进行相应调整。
打开CMake GUI程序，设置源码目录和编译目录，并选择x64位编译。  

![](/png/hackrf/cmake01.png)
接着设置libusb、pthreads、fftw库的位置。  

![](/png/hackrf/cmake02.png)

分别点击“Configure”，“Generate”，“Open Project”按钮，打开Visual Studio 2015后，选择编译配置(Debug，Release...)，按F7快捷键，编译整个解决方案。
如果一切都无误的情况下，将看到如下提示：
```
15>------ 已跳过全部重新生成: 项目: INSTALL, 配置: Release x64 ------
15>没有为此解决方案配置选中要生成的项目 
========== 全部重新生成: 成功 13 个，失败 0 个，跳过 2 个 ==========
```
INSTALL和uninstall项目需要单独编译生成。生成INSTALL项目，提示如下：
```
1>------ 已启动生成: 项目: INSTALL, 配置: Release x64 ------
1>  -- Install configuration: "Release"
1>  -- Installing: D:/hackrf/host/INSTALL_x64/lib/pkgconfig/libhackrf.pc
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf.lib
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf.dll
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_static.lib
1>  -- Installing: D:/hackrf/host/INSTALL_x64/include/libhackrf/hackrf.h
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_transfer.exe
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_spiflash.exe
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_cpldjtag.exe
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_info.exe
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_debug.exe
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_clock.exe
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_sweep.exe
1>  -- Installing: D:/hackrf/host/INSTALL_x64/bin/hackrf_operacake.exe
========== 生成: 成功 1 个，失败 0 个，最新 13 个，跳过 0 个 ==========
```
在CMake配置的安装目录下，将生成include、lib、bin三个文件夹：
![](/png/hackrf/libhackrf_folds.png)
其中bin目录下，有以下这些文件，把libusb、pthreads、fftw库的动态链接库文件，拷贝过来，就可以运行这些小工具，进一步熟悉HackRF One硬件了。
![](/png/hackrf/libhackrf_files.png)

生成uninstall项目，提示如下：
```
1>------ 已启动生成: 项目: uninstall, 配置: Release x64 ------
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/lib/pkgconfig/libhackrf.pc"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf.lib"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf.dll"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_static.lib"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/include/libhackrf/hackrf.h"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_transfer.exe"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_spiflash.exe"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_cpldjtag.exe"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_info.exe"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_debug.exe"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_clock.exe"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_sweep.exe"
1>  -- Uninstalling "D:/hackrf/host/INSTALL_x64/bin/hackrf_operacake.exe"
========== 生成: 成功 1 个，失败 0 个，最新 1 个，跳过 0 个 ==========
```

好了，Windows下配置HackRF One环境，按照以上步骤，基本能顺利完成。如果遇到什么问题，也可以到博主QQ技术群[(181849313)]进行讨论。

[hackrf.net]:http://www.hackrf.net/2014/06/csdn_os_camp/
[HackRF项目]:https://github.com/mossmann/hackrf/
[wiki页]:https://github.com/mossmann/hackrf/wiki/
[PothosSDR]:http://downloads.myriadrf.org/builds/PothosSDR/?C=M;O=D
[(181849313)]:https://jq.qq.com/?_wv=1027&k=5PaJ8B4
