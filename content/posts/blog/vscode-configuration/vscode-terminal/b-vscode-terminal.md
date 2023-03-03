---
title: Win10 VSCode 终端美化
date: 2023-03-02
lastmod: 2023-03-03
type: blog
series:
- 
tags:
- terminal
- Windows
- VSCode
description:
draft: false
cover:
    image: 
    caption: 
    alt: 
    relative: false
---

## 想法

VSCode 的终端会调用电脑装好的 shell，例如 cmd 和 Win10 自带的 PowerShell，想要美化就得从 shell 入手。关于这个 Win10 下有两套方案：

1. **使用zsh**。安装 WSL，在 Ubuntu 子系统中使用 oh-my-zsh 配置 zsh 命令解释器，然后在 VSCode 中安装 Remote-WSL 插件，连接子系统从而使用 zsh，属于是曲线救国。
2. **使用PowerShell**。微软的 PowerShell 也可也安装模组提升体验，使用 oh-my-posh 或是 [starship](https://github.com/starship/starship) 来美化命令行，使用 PSReadline 来优化操作。

两种方法各有利弊，使用 zsh 好处是有丰富好用的插件库，活跃的社区能让终端足够酷炫，但是我还是选择了第二种，可能是觉得微软味更冲。

## 美化命令行

### 准备

Win10 自带的 PowerShell 应该是 5.x 版本，可能不支持一些 Module 的效果，因此先安装最新版本的 PowerShell（[github下载地址](https://github.com/PowerShell/PowerShell)）。**使用默认地址安装，千万不要改地址，会导致后续无法识别**。

也可以使用`winget`安装，更方便之后的更新等操作：

```shell
winget install Microsoft.PowerShell
```

安装完后`Win+R`使用指令`pwsh`打开。VSCode 默认打开终端也是最新版，我们只用在一个地方配置好即可。

在配置之前，推荐尝试微软的新终端 Windows Terminal，可配置高颜值，在微软商店就能下载。正常打开就能看到自带PowerShell和安装好的最新版，我们设置默认打开最新版。

不知道什么原因我另一台电脑只有识别到老版 PowerShell，因此我在终端设置中将老版的可执行文件地址改为新版地址（默认安装地址是`C:\Program Files\PowerShell\7\pwsh.exe`）。

设置完重启终端就能查看到。

### 安装模组

首先，前文提到的 PSReadline 随最新版 PowerShell 附赠，不用特意安装。使用`Get-Module`指令就可以查看安装好的模组列表。在 PowerShell 中安装 posh-git 和 oh-my-posh：

```shell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

其中安装的 oh-my-posh 在执行`Import-Module oh-my-posh`时提示不成功，需要按照[官网指引](https://ohmyposh.dev/docs/installation/windows)进行安装。指引中可以使用以下两条命令任选一条进行安装：

```shell
winget install JanDeDobbeleer.OhMyPosh #winget
# or
scoop install https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/oh-my-posh.json #scoop
```

oh-my-posh 的默认版本是高于 2 的，是以 Go 语言编写，适用于多平台。虽然看到说法是 v2 版本打开速度更快，但现在版本有更丰富的主题选择，可以根据个人需求选择。

接下来设置配置各个模组。

### 设置 PSReadline

对模组的设置可以编辑设置文档，打开方式：

```shell
notepad $Profile
```

一开始没有可能会提醒新建一个，点击确定即可。

在文件中输入以下指令设置 PSReadline：

```shell
Set-PSReadLineOption -PredictionSource History # 设置预测文本来源为历史记录
 
Set-PSReadlineKeyHandler -Key Tab -Function MenuComplete # tab 显示命令菜单
Set-PSReadLineKeyHandler -Key "Ctrl+z" -Function Undo # 设置 Ctrl+z 为撤销
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward # 设置向上键为后向搜索历史记录
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward # 设置向下键为前向搜索历史纪录
```

这些设置都可以参考[官方文档](https://docs.microsoft.com/en-us/powershell/module/psreadline/?view=powershell-7.2)按需求进行修改。

### 设置oh-my-posh

#### 使用

以前使用`Import-Module oh-my-posh`可以导入，但按照最新文档，需要使用：

```shell
oh-my-posh init pwsh | Invoke-Expression
```

posh-git 模块仍然可以使用`Import-Module posh-git`导入。

添加并保存好设置文档后，重启终端，可以看到乱码但有颜色的命令行。接下来需要添加 Nerd Fonts 字体。

可以从[Nerd Fonts网站](https://www.nerdfonts.com/font-downloads)选择喜欢的下载安装，我使用的是 Jetbrains 的 JetBrainsMono Nerd Font，下载的压缩包里可能有很多字体，选择一款 Windows Compatible 的安装即可。

安装后在终端设置里的默认值外观设置该字体，保存设置切回去就能看到好看的样式。

输入`Get-PoshThemes`查看已经预载的样式，我这里使用的是 powerlevel10k_classic 主题。参照[官网文档](https://ohmyposh.dev/docs/installation/windows)，使用 winget 安装的话需要修改启动指令为：

```shell
oh-my-posh init pwsh --config $env:POSH_THEMES_PATH\powerlevel10k_classic.omp.json | Invoke-Expression
```

使用 scoop 安装的话则需要修改启动指令为：
```shell
oh-my-posh init pwsh --config "$(scoop prefix oh-my-posh)\themes\jandedobbeleer.omp.json" | Invoke-Expression
```

重启终端就能看到新主题。

#### 自定义主题

因为我是通过`Powershell Module`的形式安装，用指令导出主题无效，只好采用手动复制的方式。

如上设置所示，主题都被存放在`$env:POSH_THEMES_PATH\`中，Windows下地址为`C:\Users\(user_name)\AppData\Local\Programs\oh-my-posh\themes`。

复制想要修改的主题，修改后在设置文档中改为自己的主题名即可。

我主要添加对 conda 的显示。

**支持conda等环境：**

参考[文档](https://ohmyposh.dev/docs/segments/python)对 Python 类型 Segment 的设置，将`desplay_mode`属性设置为`environment`，只有使用虚拟环境时才显示 Python 信息。

设置的样式如下

```json
{
    "type": "python",
    "style": "plain",
    "background": "#546E7A",
    "foreground": "#98C379",
    "properties": {
    "display_default": true,
    "display_mode": "environment"
    },
    "template": "\uE235 {{ if .Error }}{{ .Error }}{{ else }}{{ if .Venv }}{{ .Venv }}({{ end }}{{ .Full }}{{ end }}) "
},
```

#### 加载太慢问题

参考：https://wdd.js.org/posts/2022/07/igur01/

### 设置Vi编辑模式

如果想使用 Vim 模式操作命令行，PSReadline 也提供方法，在设置文档中添加:

```shell
Set-PSReadlineOption -EditMode Vi # 设置Vim编辑模式
```

单单这样设置，插入模式和正常模式切换都是竖线光标，无法区分，添加以下代码区分不同模式光标:

```shell
function OnViModeChange {
    if ($args[0] -eq 'Command') {
        # Set the cursor to a blinking block.
        Write-Host -NoNewLine "`e[1 q"
    } else {
        # Set the cursor to a blinking line.
        Write-Host -NoNewLine "`e[5 q"
    }
}
Set-PSReadLineOption -ViModeIndicator Script -ViModeChangeHandler $Function:OnViModeChange
```

**玄学问题**：这部分代码要放到之前 PSReadline 的设置上面，否则tab功能会失效，应该是被覆盖了。

## 美化 VSCode Terminal

以上步骤设置好，基本打开 VSCode 的终端就是设置好的 PowerShell，这里再提醒一下，**安装PowerShell的时候一定要使用默认地址，否则这里会找不到最新版**。

还需要设置终端字体，在设置的`terminal.integrated.fontFamily`中设置，我设置为`JetBrainsMono NF`。

## 美化 Windows Terminal

未完待续。。。