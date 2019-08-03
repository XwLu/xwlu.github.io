---
layout: wiki
title: Source List Configuration
categories: [wiki]
description: source list file for ubuntu16.04
keywords: source, ubuntu16.04
---


### 打开源文件
> sudo gedit /etc/apt/sources.list

### 填写相关源(只适用于16.04)

```
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse

deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse

# 163
#deb http://mirrors.163.com/ubuntu/ xenial main restricted universe multiverse
#deb http://mirrors.163.com/ubuntu/ xenial-security main restricted universe multiverse
#deb http://mirrors.163.com/ubuntu/ xenial-updates main restricted universe multiverse
#deb http://mirrors.163.com/ubuntu/ xenial-proposed main restricted universe multiverse
#deb http://mirrors.163.com/ubuntu/ xenial-backports main restricted universe multiverse

#deb-src http://mirrors.163.com/ubuntu/ xenial main restricted universe multiverse
#deb-src http://mirrors.163.com/ubuntu/ xenial-security main restricted universe multiverse
#deb-src http://mirrors.163.com/ubuntu/ xenial-updates main restricted universe multiverse
#deb-src http://mirrors.163.com/ubuntu/ xenial-proposed main restricted universe multiverse
#deb-src http://mirrors.163.com/ubuntu/ xenial-backports main restricted universe multiverse


# zhong ke da ipv4
# deb http://debian.ustc.edu.cn/ubuntu/ xenial main multiverse restricted universe
# deb http://debian.ustc.edu.cn/ubuntu/ xenial-backports main multiverse restricted universe
# deb http://debian.ustc.edu.cn/ubuntu/ xenial-proposed main multiverse restricted universe
# deb http://debian.ustc.edu.cn/ubuntu/ xenial-security main multiverse restricted universe
# deb http://debian.ustc.edu.cn/ubuntu/ xenial-updates main multiverse restricted universe
# deb-src http://debian.ustc.edu.cn/ubuntu/ xenial main multiverse restricted universe
# deb-src http://debian.ustc.edu.cn/ubuntu/ xenial-backports main multiverse restricted universe
# deb-src http://debian.ustc.edu.cn/ubuntu/ xenial-proposed main multiverse restricted universe
# deb-src http://debian.ustc.edu.cn/ubuntu/ xenial-security main multiverse restricted universe
# deb-src http://debian.ustc.edu.cn/ubuntu/ xenial-updates main multiverse restricted universe
```
