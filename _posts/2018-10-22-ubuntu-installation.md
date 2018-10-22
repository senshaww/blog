---
layout: post
title: win7下安装ubuntu
toc: true
cover: /img/cover/3.jpg
tags: ['ubuntu']
---
win7下安装ubuntu18.04双系统。
<!-- more -->
## 双系统实现原理

![](/assets/img/2018-10/双系统原理.jpg)

### BIOS  
Basic Input Output System。结合英文全称来说一下，基本输入输出系统。BIOS 是开机的时候计算机执行的第一个程序，他会决定你开启电脑后的下一步工作。BIOS知道你的磁盘里哪些可以开机，并且会读取第一个扇区的MBR。

### MBR  
Master boot record, 我也不太明确这个定义，不过我确切的知道它是用来干嘛的。MBR是可以执行自己内部的开机管理程序的。

### 开机管理程序  
开机管理程序会加载操作系统的核心文件。一个电脑里装上两个系统，只需要有两个开机管理程序就行了。   
开机管理程序的其他功能：  
>选单、载入核心文件以及转交其它开机管理程序。转交这个功能可以用来实现了多重引导，只需要将另一个操作系统的开机管理程序安装在其它分区的启动扇区上，在启动 MBR 中的开机管理程序时，就可以选择启动当前的操作系统或者转交给其它开机管理程序从而启动另一个操作系统。

### 过程总结  
电源开启，电脑启动BIOS，BIOS读取第一个扇区的MBR，MBR看自己内部有没有开机管理程序（没有的话就废了，没装系统嘛），有的话，看看选单里面有啥东西，（如果安装了Linux和Windows），MBR自身的开机管理程序是Windows的，并且可以转发到另一个开机管理程序。那么选单里就有两个选项，载入Windows和载入Linux。而载入Linux实际上是两个步骤，先转发到Linux开机管理程序的位置，然后由Linux的开机管理程序，载入Linux系统核心。

**以上可能会有部分表述不当或错误**


## U盘安装ubuntu

利用rufus制作ubuntu启动盘，并在bios下设置u盘启动，由于实验室电脑出于某些原因不能u盘启动系统，故采用下面方法。

## 硬盘安装ubuntu

### 准备工具
easybcd  
ubuntu-18.04.1-desktop-amd64.iso

### 可用空间准备  
开始+R 打开运行界面，输入 diskmgmt.msc打开磁盘管理，右键一个不使用的磁盘删除，得到一块可用空间。

### 配置引导

将.iso中的initrd.lz、vmlinuz.efi 解压出来 与iso 一同放在C盘 或D盘根目录（必须根目录）下：

打开EasyBCD,添加新条目-》NeoGrub -》安装：

配置，文件内容如下：

注意

* 上面的文件放在C盘根目录则为 hd0,0 ，D盘则为hd0,4

* （hdx,y） x代表对应磁盘，y代表对应分区

```
# NeoSmart NeoGrub Bootloader Configuration File
#
# This is the NeoGrub configuration file, and should be located at C:\NST\menu.lst
# Please see the EasyBCD Documentation for information on how to create/modify entries:
# http://neosmart.net/wiki/display/EBCD/
 
title Install Ubuntu 
root (hd0,0) 
kernel (hd0,0)/vmlinuz.efi boot=casper iso-scan/filename=/ubuntu-18.04.1-desktop-amd64.iso ro quiet splash locale=zh_CN.UTF-8 
initrd (hd0,0)/initrd.lz
```

在EasyBCD 编辑引导菜单可看到NeoGrub引导加载器，记得勾选等待用户选择，保存设置。

### 进入引导

配置完成后重启计算机，选择NeoGrub引导加载器，若上诉配置无误即可进入ubuntu界面的引导：

### 开始Ubuntu系统安装

打开终端，输入以下命令卸载分区：
```
sudo umount -l /isodevice
```

然后点击桌面的安装程序进行安装

### 删除引导

安装完之后，重启进入Windows界面，打开EasyBCD，添加Ubuntu启动项，顺便删除NeoGrub引导项,删除根目录下的iso 等三个文件：

![](/assets/img/2018-10/引导界面.png)

## 一些问题

### grub error: unknown filesystem的解决办法
改变磁盘分区时可能出现

https://www.linuxidc.com/Linux/2012-06/61983.htm

### 增大ubuntu磁盘空间

https://www.cnblogs.com/zalebool/p/5814907.html

## END





