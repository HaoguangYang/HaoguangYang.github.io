---
layout: post
category: Setup
title: Linux 与 Windows 双系统工作站搭建工作记录
tagline: by Yang
tags: 
  - Setup
  - Dual Boot
published: true
---

本工作站作为医学影像的机器视觉服务器使用，同时兼顾科学计算和部分实验室娱乐的需求，采用如下配置：

<!--more-->

|项目|值|
|---|---|
|主板|SuperMicro X10DAL-i|
|处理器|Intel® Xeon(R) CPU E5-2667 v3 QEYA Engineering Sample × 2 @ 2.90GHz, 3.50GHz Max × 4 Cores, 8 Threads|
|RAM|64.0 GiB DDR4 2133 RDIMM 四通道内存|
|GPU|NVidia GeForce GTX 1080 × 2 /PCIe/SSE2，通过修改BIOS实现SLI|
|操作系统|Ubuntu 16.04 LTS amd64, Windows 10 Education|
|硬盘|256 GiB 操作系统盘，3000 GiB HDD × 2|

## 磁盘分区方案（磁盘空间为大致值）
* /dev/sda:
/dev/sda1	500MB NTFS, Windows Boot
/dev/sda2	138500MB NTFS, Windows
/dev/sda3	100000MB Ext4. Linux /
* /dev/sdb:
/dev/sdb1	350000MB NTFS, WindowsUsers
/dev/sdb2	2000MB Swap, Linux Swap
/dev/sdb3	50000MB Ext4, Linux /tmp
/dev/sdb4	450000MB Ext4, Linux /home
/dev/sdb5	1000000MB NTFS. Software
/dev/sdb6	1000000MB NTFS, Developer
* /dev/sdc:
/dev/sdc1	1400000MB NTFS, Data
/dev/sdc2	1450000MB Ext4, Backup

## 安装双系统
### 安装Windows操作系统

Windows 操作系统按正常安装流程安装，安装完成后关闭并删除系统分页文件，关闭快速启动，关闭自动睡眠，将用户文件移动至sdb1分区，并通过mklink /J命令创建符号链接。

### 安装Ubuntu 16.04

按磁盘分区挂载各Linux分区，注意将Grub引导安装在/dev/sda.

* 引导出现问题的解决办法

由于误操作，将Grub安装在/dev/sda1，导致引导Windows时进入无限循环。通过GRUB4DOS引导盘引导进入Windows，使用Bootice.exe 工具移除/dev/sda1的Grub，重启并装入Ubuntu Live CD，终端执行如下命令：
```sh
sudo update-grub; sudo grub-install /dev/sda
```
完成Grub修复。

## 软件配置如之前记录所述。