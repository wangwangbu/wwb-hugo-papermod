---
title: VSCode Extension (2) 插件能力
date: 2022-05-21
lastmod: 2023-03-03
type: blog
series:
- VSCode Extension
tags:
- VSCode Extension
description:
draft: false
cover:
    image: 
    caption: 
    alt: 
    relative: false
---

## 创建指令

开发者可以为动作指定指令，通过两步操作：

- 在`package.json`的`contributes.commands`定义命令。
- 在主要文件中使用`vscode.commands.registerCommand`注册命令。

关于 **Command** 更详细的介绍可以看这个[文档](https://code.visualstudio.com/api/extension-guides/command)。

使用`vscode.commands.executeCommand`指令可以主动调用指令，并且命令也可以有输入和输出参数。

[[Command-URIs]] 