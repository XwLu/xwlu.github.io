---
layout: post
title: Vscode Vim配置
categories: [blog]
description: Vscode Vim setting json
keywords: vscode vim
---

### 背景
- vscode vim插件的个人简单配置模版

---
 
### 配置内容
  ```
  {
    // vim config
    "editor.lineNumbers": "relative",
    "workbench.list.automaticKeyboardNavigation": false,
    // 绑定vim前导键
    "vim.leader": "<space>",
    // 启用easymotion插件
    "vim.easymotion": true,
    // 启用系统粘贴板作为vim寄存器
    "vim.useSystemClipboard": true,
    // 由vim接管ctrl+any的按键，而不是vscode
    "vim.useCtrlKeys": true,
    // 突出显示与当前搜索匹配的所有文本
    "vim.hlsearch": true,
     // 普通模式下的非递归按键绑定
    "vim.normalModeKeyBindingsNonRecursive": [
      // zz展开收起括号内的内容
      {
        "before": ["z", "z"],
        "commands": ["editor.toggleFold"]
      },
      // 保存&退出当前文本
      {
        "before": ["<leader>", "s"],
        "commands":[":w!"]
      },
      {
        "before": ["<leader>", "q"],
        "commands": [":q!"]
      },
      {
        "before": ["<leader>", "sq"],
        "commands": [":wq!"]
      },
      // 上下跳5行
      {
        "before": ["<C-j>"],
        "after": ["5", "j"]
      },
      {
        "before": ["<C-k>"],
        "after": ["5", "k"]
      },
      // 行首行尾
      {
        "before": ["<C-h>"],
        "after": ["^"]
      },
      {
        "before": ["<C-l>"],
        "after": ["$"]
      },
    ],
    // 插入模式下的非递归按键绑定
    "vim.insertModeKeyBindings": [],
    // 命令模式下的非递归按键绑定
    "vim.commandLineModeKeyBindingsNonRecursive": [],
    // 可视模式下的非递归按键绑定
    "vim.operatorPendingModeKeyBindings": [],
    // 下面定义的按键将交由vscode进行处理，而不是vscode-vim插件
    "vim.handleKeys": {
      "<C-a>": false,
      "<C-f>": false,
      "<C-p>": false,
     // "<C-k>": false
    },
  }
  ```
