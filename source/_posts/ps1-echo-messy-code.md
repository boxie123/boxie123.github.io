---
title: 记一次Powershell乱码
author: 铂屑
date: 2025-02-16 01:04:00
updated: 2025-02-16 01:04:00
tags:
    - PowerShell
    - 分享
    - 笔记
categories:
    - 后端编程
---

今天尝试编写一个简单PowerShell脚本时，发现echo出的中文内容显示乱码，一番查找资料才得到解决，遂记录在这里。

<!-- more -->

> 省流：将文件编码改为`UTF-8-BOM`

### 原因说明

> 如果需要在脚本中使用非 Ascii 字符，请将它们保存为带有 BOM 的 UTF-8 文件。 如果没有 BOM，Windows PowerShell 会将脚本误解为是在旧的“ANSI”代码页中编码的。 
>
> 相反，具有 UTF-8 BOM 的文件在类 Unix 平台上可能会出现问题。 许多 Unix 工具（如 cat、sed、awk）以及一些编辑器（如 gedit）不知道如何处理 BOM。       ——[PowerShell参考文档](https://learn.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/about/about_character_encoding?view=powershell-7.5&viewFallbackFrom=powershell-7.2)

即：Windows默认使用BOM来区分Unicode和非Unicode编码，对非Unicode就会使用程序指定的ANSI编码页来解读，而Unicode则会使用另一套称为宽字符的方法。

此历史遗留问题在 PowerShell 6.0 以上的版本中已得到修正，但截至目前（Windows 11 26100.3194），系统自带的 PowerShell 仍然只是 5.1 版本，因此更新PS版本也可以解决此问题。

### 错误方法

我开始时编写的.ps1文件内容十分简单，仅用于快速启动一些程序：
```powershell
# 桌面路径
$desktopPath = "$env:USERPROFILE\Desktop"
# 需要启动的应用程序路径
$apps = @(
    "Internet\QQ.lnk",
    "Internet\微信.lnk",
    "Internet\Google Chrome.lnk"
)  

foreach ($app in $apps) {
    try {
		    echo "正在启动：$desktopPath\$app"
        Start-Process "$desktopPath\$app" -ErrorAction Stop
    }
    catch {
        Write-Warning "无法启动应用程序：$_"
    }
}
pause
```
但此时其中中文出现乱码。

我自然认为是编码页不同导致的，于是在文件开头添加了如下两行：
```powershell
chcp 65001
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
```

问题未能得到解决。

后在中文互联网搜索，结果基本都是更改编码页，而且大量同一篇文章的复制粘贴。

后找到一篇大佬的[博客](https://www.curious.host/posts/4/)才得以解决，博客文末附有介绍字符编码和Windows这部分处理的历史包袱的文章链接，感兴趣可以点进去看一下。
