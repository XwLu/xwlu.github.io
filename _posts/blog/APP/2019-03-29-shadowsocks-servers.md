---
layout: post
title: SS server 一键搭建
categories: [blog]
description: SS服务器搭建教程
keywords: ss, server 
---

## 安装
### root用户登陆，执行如下命令
```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```
### 一步一步按照指示安装

---

## 卸载
### root用户登陆，执行如下命令
```
./shadowsocks-all.sh uninstall
```

---

## 启动脚本
- ### Shadowsocks-Python
> /etc/init.d/shadowsocks-python start | stop | restart | status
- ### ShadowsocksR
> /etc/init.d/shadowsocks-r start | stop | restart | status
- ### Shadowsocks-Go
> /etc/init.d/shadowsocks-go start | stop | restart | status
- ### Shadowsocks-libev
> /etc/init.d/shadowsocks-libev start | stop | restart | status

---

## 配置文件
- ### Shadowsocks-Python
> /etc/shadowsocks-python/config.json
- ### ShadowsocksR
> /etc/shadowsocks-r/config.json
- ### Shadowsocks-Go
> /etc/shadowsocks-go/config.json
- ### Shadowsocks-libev
> /etc/shadowsocks-libev/config.json

### [参考链接](https://blog.csdn.net/u011293129/article/details/82991535)
