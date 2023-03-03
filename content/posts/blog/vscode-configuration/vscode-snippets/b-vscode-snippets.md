---
title: VSCode Snippets 使用
date: 2022-05-20
lastmod: 2023-03-03
type: blog
series:
- 
tags:
- VSCode
description:
draft: false
cover:
    image: 
    caption: 
    alt: 
    relative: false
---

## 介绍

VS Code 允许用户自定义代码段，通过自定义指令快速创建代码块。（[官方文档](https://code.visualstudio.com/docs/editor/userdefinedsnippets)）

代码块分两种：

1. 全局代码块（在`snippets`目录下生成后缀名为`.code-snippets`的文件）
2. 特定语言的代码块（生成`对应语言.json`文件）

> 可以在项目根目录的`.vscode`目录下添加后缀名为`.code-snippets`文件来定制当前工作环境下的代码块。

## 使用

通过命令`Preferences: Configure User Snippets`管理和创建代码块。

一个我[[Notes-in-VSCode]]元数据的例子如下：

```json
{
    "Metadata": {
        "scope": "markdown",
        "prefix": "/meta",
        "description": "创建这条笔记的元数据",
        "body": [
            "---",
            "date: $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE",
            "type: ${1|blog,idea,life|}",
            "tags: $2",
            "---"
        ]
    }
}
```

其中，Metadata是代码块的名字，包含以下字段：

- `scope`指定markdown文件使用
- `prefix`设定调用的命令，也可以是`for`等形式，触发该代码块提示
- `description`代码块说明
- `body`代码块内容，当输入命令回车后会替换命令

在`body`中有`$CURRENT_YEAR`这种以`$`开头的变量，都是VS Code内置变量，具体可以查看官方文档

还有`$`后面跟数字的是提示用户输入的占位符，调用命令后我们可以使用`Tab`键在这些地方切换输入。其中`${1|blog,idea,life|}`可以在输入的时候提供这几个选项。

> 如果觉得写`body`十分麻烦，这里还有[snippet生成器网站](https://snippet-generator.app/)。

## 一些应用

### 生成博客的元信息

因为可以在新笔记的模板中设置上文元信息，`/meta`用来生成博客元信息，按需求改为

```json
{
    "Metadata": {
        "scope": "markdown",
        "prefix": "/meta",
        "description": "创建这条博客的元数据",
        "body": [
            "---"
            "title: WSL 2 的安装与配置"
            "date: $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE"
            "lastmod: $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE"
            "type: $1"
            "series:"
            "- "
            "tags:"
            "- $2"
            "description:"
            "draft: true"
            "cover:"
            "    image: "
            "    caption: "
            "    alt: "
            "    relative: false"
            "---"
        ]
    }
}
```

还有其他语言的用法值得探究。