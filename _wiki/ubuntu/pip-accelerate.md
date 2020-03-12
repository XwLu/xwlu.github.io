---
layout: wiki
title: Pip accelerate
categories: [Linux]
description: Pip accelerate in ubuntu
keywords: pip, accelerate
---

### 在 ~/.pip/ 下创建文件 pip.conf
```
  [global]
  timeout = 6000
  index-url = http://pypi.douban.com/simple/ 
  [install]
  use-mirrors = true
  mirrors = http://pypi.douban.com/simple/ 
  trusted-host = pypi.douban.com
```
