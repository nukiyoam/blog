---
title: "Win11 PowerShell 配置"
date: 2023-03-27T14:40:12+08:00
categories:
  - Windows
  - PowerShell
tags:
  - Windows
  - PowerShell
slug: "Win11 PowerShell 配置"
---

> 最近从Mac换到了Windwos做开发，最直观的感受就是终端体验和Mac上真的是差太远了，所以在网上找了些资料，将PowerShell做了一番配置，让Windows的终端能更顺手一点

## 升级 PowerShell

Windows 11 默认的 PowerShell 版本为 5.x，而现在最新的 PowerShell的版本已经到了 7.x，新增了很多新特性和很多的改进。

关于 PowerShell 5.x 和 PowerShell 7.x 的对比可以看[这里](https://learn.microsoft.com/zh-cn/powershell/scripting/whats-new/differences-from-windows-powershell)

查看当前 PowerShell 的版本信息可以在 PowerShell 终端输入以下命令查看

```powershell
$PSVersionTable
```

升级方式：

1. 下载最新的[安装包.msi](https://github.com/PowerShell/PowerShell/releases)
2. 下载好之后直接双击安装

安装之后重新打开终端，在终端下拉窗口看到增加了一个 PowerShell 的选项即为安装成功

![终端下拉框](img/windows/PowerShell/Win11-PowerShell-配置/终端下拉.png)

此时可以通过设置，将新安装的 PowerShell 作为默认选项

## Oh My Posh

Oh My Posh 是一个类似于 Oh My Zsh 的 PowerShell 插件，用于美化 PowerShell

详细信息可查看 [Oh My Posh 官网](https://ohmyposh.dev/)

### 安装：

- 通过 [winget](https://learn.microsoft.com/zh-cn/windows/package-manager/winget/) 安装
  
  ```powershell
  winget install JanDeDobbeleer.OhMyPosh -s winget
  ```

- 通过 [scoop](https://scoop.sh/) 安装
  
  ```powershell
  scoop install https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/oh-my-posh.json
  ```

- 手动安装
 
  ```powershell
  Set-ExecutionPolicy Bypass -Scope Process -Force; Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://ohmyposh.dev/install.ps1'))
  ```

详细安装教程可查看[这里](https://ohmyposh.dev/docs/installation/windows)

### 启用

启用 Oh My Posh 需要配置 PowerShell 的配置文件，配置文件可通过以下命令打开/创建

```powershell
# 使用记事本打开
notepad $PROFILE

# 使用 vscode 打开
code $PROFILE
```
在打开的配置文件中添加以下内容

```powershell
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/jandedobbeleer.omp.json" | Invoke-Expression
```
保存配置文件，重新打开终端，不出意外的话，可以看到如下的界面

![Oh My Posh 默认主题](img/windows/PowerShell/Win11-PowerShell-配置/OhMyPosh默认主题.png)

说明 Oh My Posh 配置成功

如果需要修改主题可参考

- [Oh My Posh 主题](https://ohmyposh.dev/docs/themes)
- [Oh My Posh 自定义](https://ohmyposh.dev/docs/installation/customize)

### 安装字体

由于 Oh My Posh 的主题需要 [Nerd Font 字体](https://www.nerdfonts.com/) ，不然会导致主题中一些图标显示不出来

字体可通过以下命令安装（需要通过管理员启动终端执行命令）

```powershell
  oh-my-posh font install
```

安装完字体之后，到终端设置，将字体修改为安装的 [Nerd Font 字体](https://www.nerdfonts.com/) 即可

## posh-git

posh-git 是一个提供 Windows Git 命令补全的 PowerShell 模块，虽然命令补全的体验不如 Mac 和 Linux，但是总比没有好

### 安装

- 通过 PowerShell 模块安装
  
  ```powershell
    # 安装
    PowerShellGet\Install-Module posh-git -Scope CurrentUser -Force

    # 升级
    PowerShellGet\Update-Module posh-git
  ```

- 通过 [scoop](https://scoop.sh/) 安装
  
  ```powershell
    scoop bucket add extras
    scoop install posh-git
  ```

### 配置

编辑 PowerShell 配置文件

```powershell
# 使用记事本打开
notepad $PROFILE

# 使用 vscode 打开
code $PROFILE
```
在打开的配置文件中添加以下内容

```powershell
Import-Module Posh-Git
```
保存配置文件，重新打开终端，这是使用 git 命令时按 tab 键就会有相应的代码补全了

## 配置命令提示

如果想要 PowerShell 能够有类似于 Zsh 的命令历史搜索功能的话，还需要做一些配置

编辑 PowerShell 配置文件

```powershell
# 使用记事本打开
notepad $PROFILE

# 使用 vscode 打开
code $PROFILE
```

添加以下内容

```powershell
# 配置历史命令提示
Import-Module PSReadLine
Set-PSReadLineOption -PredictionSource History

#Tab键会出现自动补全菜单
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete 

# 上下方向键箭头，搜索历史中进行自动补全，并且设置搜索历史之后，将光标设置到最后一行
Set-PSReadLineKeyHandler -Key UpArrow -ScriptBlock {
    [Microsoft.PowerShell.PSConsoleReadLine]::HistorySearchBackward()
    [Microsoft.PowerShell.PSConsoleReadLine]::EndOfLine()
}
Set-PSReadLineKeyHandler -Key DownArrow -ScriptBlock {
    [Microsoft.PowerShell.PSConsoleReadLine]::HistorySearchForward()
    [Microsoft.PowerShell.PSConsoleReadLine]::EndOfLine()
}
```

完成以上配置之后，Windows 的 PowerShell 就感觉要顺手一丢丢了

## 参考

- [给 PowerShell 带来 zsh 的体验](https://zhuanlan.zhihu.com/p/137251716)






