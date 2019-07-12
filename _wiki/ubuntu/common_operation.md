---
layout: wiki
title: Common Operation in Ubuntu
categories: [wiki]
description: source list file for ubuntu16.04
keywords: source, ubuntu16.04
---

### 查看版本信息
> cat /etc/issue

### alias终端宏定义(.bashrc)
> alias ssh_pcduino='ssh linaro@192.168.1.241'

### 查看USB口的使用
> dmesg | grep usb*

### 给USB口附权限
> sudo chmod +777 /dev/ttyUSB0

### 在当前路径下寻找相关目标
> grep -r xxx ./

### 键盘调整终端
> alt+space
