---
title: 外挂硬盘（U盘）中安装ubuntu
date: 2017-03-26 17:11:51
tags: linux
---
### 准备
笔记本、U盘（用作刻录ubuntu启动盘）、硬盘（用于安装ubuntu）、ubuntu的ISO文件、Diskgenius、Ultraiso

<!--more-->
### 安装
1. 用Ultraiso将ubuntu的ISO刻录到U盘中
2. 用Ultraiso将硬盘格式化成fat32的分区
3. 插入U盘重启，将电脑bios改成U盘启动，就可以看见进入了U盘刻录的ubuntu的界面，选择试用ubuntu，插入硬盘，点击安装ubuntu
4. 根据安装提示，一步一步的安装，注意在安装类型选择界面要选择其他选项即自己创建分区，将之前在硬盘上创建的fat32的分区删除，然后根据需要分区，下方安装启动引导器应选择boot所在分区。
5. 安装完成之后选择重启，然后拔出U盘，就能看见启动了硬盘中的ubuntu。


### 注意
1. 硬盘应该是MBR格式（水平比较菜GPT没安装成功，由于原本win7用的MBR，如果要支持GPT，可能要改bios，这样的话每次切换系统都要修改bios了）
2. 需要先用Diskgenius将硬盘格式化成fat32的分区，在安装ubuntu的时候再删除，不然可能启动时出现“J”的错误界面