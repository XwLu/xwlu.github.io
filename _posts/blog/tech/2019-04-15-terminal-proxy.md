---
layout: post
title: Terminal Proxy
categories: [blog]
description: Terminal 配置
keywords: terminal proxy
---

## 终端fq
- 在终端中直接运行命令
```
export http_proxy=http://proxyAddress:port
```

- 如果你用的是ss代理，在当前终端运行以下命令，那么wget curl这类网络命令都会经过ss代理
```
export ALL_PROXY=socks5://127.0.0.1:1080
```

- 直接在.bashrc或者.zshrc添加下面内容
```
export http_proxy="http://localhost:port"
export https_proxy="http://localhost:port"
```

- 或者直接设置ALL_PROXY
```
export ALL_PROXY=socks5://127.0.0.1:1080
```

- [链接](https://blog.fazero.me/2015/09/15/%E8%AE%A9%E7%BB%88%E7%AB%AF%E8%B5%B0%E4%BB%A3%E7%90%86%E7%9A%84%E5%87%A0%E7%A7%8D%E6%96%B9%E6%B3%95/)
