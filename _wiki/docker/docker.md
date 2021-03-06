---
layout: wiki
title: Docker常用指令 
categories: [Docker]
description: Docker commands
keywords: docker
---

# 镜像
## 查看本地镜像
```
$ docker images
```

## 拉取远程仓库的镜像
```
$ docker pull ubuntu:13.10
```

## 删除镜像
```
$ docker rmi ubuntu<REPOSITORY>
```

## 更新镜像
```
//首先利用image创建一个container
$ docker run -t -i ubuntu:15.10 /bin/bash
//对container进行操作，修改了image
$ apt-get update 
//将对image的修改更新上去
$ docker commit -m="has update" -a="looyifan" e218edb10161 looyifan/ubuntu:v2
```
> **-m:** 提交的描述信息
> **-a:** 指定镜像作者
> **e218edb10161:** 容器 ID
> **looyifan/ubuntu:v2:** 指定要创建的目标镜像名

## 设置镜像标签
```
$ docker tag 860c279d2fec looyifan/ubuntu:dev
```

---

# 容器
## 创建容器
- ### 创建后进入终端
```
$ docker run -it ubuntu /bin/bash
```
- ### 创建后挂在后台
```
$ docker run -itd --name ubuntu-test ubuntu /bin/bash
```
> **-i:** 交互式操作。
> **-t:** 终端。
> **ubuntu:** ubuntu 镜像。
> **/bin/bash:** 放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash。
> **-d:** 挂在后台

## 查看容器
```
$ docker ps -a
```

## 停止容器
```
$ docker stop b750bbbcfd88<container ID> 
```

## 启动已经停止的容器
```
$ docker start b750bbbcfd88<container ID> 
```

## 进入容器（终端）
```
$ docker exec -it 243c32535da7 /bin/bash
```

## 退出容器终端
```
$ exit
```
**注意:** 通过**exec**进入容器的终端，在退出后并不会stop容器，利用**attach**则会。

## 导出容器快照
- 将容器导出为镜像，存储到本地文件ubuntu.tar
```
$ docker export 1e560fca3906 > ubuntu.tar
```

## 导入容器快照
- 以下实例将快照文件 ubuntu.tar 导入到镜像 test/ubuntu:v1:
```
$ cat docker/ubuntu.tar | docker import - test/ubuntu:v1
$ docker import http://example.com/exampleimage.tgz example/imagerepo //支持url导入
```
**注意:** 导入后会生成一个新的镜像，你需要自行重启该镜像的容器

## 删除容器
```
$ docker rm -f 1e560fca3906
```

## 进入容器的同时挂载本地目录
```
docker run -itd -v /Users/looyifan/workspace:/root/home/workspace ${IMAGE_ID} /bin/bash
```
