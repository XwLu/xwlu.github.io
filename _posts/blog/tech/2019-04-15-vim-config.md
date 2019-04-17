---
layout: post
title: Vim Configuration
categories: [blog]
description: Vim 配置
keywords: vim vundle
---

## 前言
- vim配置说到底就是配置vim自身的特性+安装插件。
- vim自身特性的配置直接使用.vimrc就可以了。
- 插件管理工具很多，自己选一个好用的就行，这里介绍的是Vundle。


### vundle安装
- 创建文件夹，存放vundle源码和插件
```
sudo mkdir -p ~/.vim/bundle/vundle
git clone http://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
```

### 配置.vimrc.bundles文件
- 这里要解释一下，大部分的教程，在安装完vundle之后，直接在.vimrc文件中调用vundle指令就可以安装插件了。
- 但是.vimrc文件除了管理插件之外，还可以配置vim自身的特性，为了不让.vimrc过于臃肿，这里的做法是：
  - 新建[.vimrc.bundles文件](https://github.com/XwLu/vim-config)，该文件负责管理插件的安装
  - 在.vimrc文件中调用.vimrc.bundles文件：
    ```
    if filereadable(expand("~/.vimrc.bundles"))
      source ~/.vimrc.bundles
    endif
    ```

### 配置.vimrc文件
- 既然vundle对插件的管理已经交给了.vimrc.bundles文件，.vimrc文件就负责对vim自身特性的配置了
- 这边给一个[示例](https://github.com/XwLu/vim-config)（也就是我的配置）

### vundle操作
- 配置文件都搞定了，接下来就是具体的安装、卸载等管理操作了
  - 打开终端进入vim
  - 命令集：
    ```
    :BundleList        -列举出列表中(.vimrc中)配置的所有插件
    :BundleInstall     -安装列表中全部插件
    :BundleInstall!    -更新列表中全部插件
    :BundleSearch foo  -查找foo插件
    :BundleSearch! foo -刷新foo插件缓存
    :BundleClean       -清除列表中没有的插件(需要确认)
    :BundleClean!      -清除列表中没有的插件(无需确认)
    ```
