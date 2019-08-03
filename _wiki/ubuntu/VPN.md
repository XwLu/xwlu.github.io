---
layout: wiki
title: FQ Service
categories: [wiki]
description: SS服务器搭建教程
keywords: ss, server, fq
---

# 服务器搭建
- ## 购买
  - 搬瓦工等
  - 获取服务器的公网ip
- ## 安装
  - ### root用户登陆，执行如下命令
  ```
    wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
    chmod +x shadowsocks-all.sh
    ./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
  ```
  - ### 一步一步按照指示安装

- ## 卸载
  - ### root用户登陆，执行如下命令
  ```
    ./shadowsocks-all.sh uninstall
  ```

- ## 启动脚本
  - ### Shadowsocks-Python
  > /etc/init.d/shadowsocks-python start | stop | restart | status
  - ### ShadowsocksR
  > /etc/init.d/shadowsocks-r start | stop | restart | status
  - ### Shadowsocks-Go
  > /etc/init.d/shadowsocks-go start | stop | restart | status
  - ### Shadowsocks-libev
  > /etc/init.d/shadowsocks-libev start | stop | restart | status

- ## 配置文件
  - ### Shadowsocks-Python
  > /etc/shadowsocks-python/config.json
  - ### ShadowsocksR
  > /etc/shadowsocks-r/config.json
  - ### Shadowsocks-Go
  > /etc/shadowsocks-go/config.json
  - ### Shadowsocks-libev
  > /etc/shadowsocks-libev/config.json

- ## [参考链接](https://blog.csdn.net/u011293129/article/details/82991535)

---

# 本地客户端配置
- ## 文件编辑
  - ### 编写ipv_4.json:
  ```
    {
      "server":"45.76.197.197",
      "server_port":8888,
      "local_address":"127.0.0.1",
      "local_port":1080,
      "password":"lalala",
      "timeout":300,
      "method":"aes-256-cfb"
    }
  ```

  - ### 编写start_ipv4.sh:
  ```
    #!/bin/sh
    sslocal -c /home/luyifan/VPN/ipv_4.json start
  ```
- ## 启动文件
  - ### 打开终端
  ```
    cd xx
    ./start_ipv4.sh
  ```

---

# 配置浏览器
