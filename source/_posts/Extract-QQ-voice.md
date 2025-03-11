---
title: 提取 QQ 语音消息文件的方法
author: 铂屑
date: 2025-03-11 17:29:30
updated: 2025-03-11 17:29:30
toc: false
tags:
    - 分享
    - 笔记
categories:
    - 小玩具
---

记录并尝试教会群友怎么提取 QQ 语音消息到 mp3 文件，不再录屏全损音质

<!--more-->

## 前置知识

> 如果你对原理不感兴趣，仅想赶快获取方法，请转到[快速使用](#快速使用)

QQ 和 VX 的语音消息都采用`.amr`格式封装，这种音频使用`silk-v3`编码，`silk-v3`编码是 Skype 向第三方开发人员和硬件制造商提供免版税认证(RF)的 Silk 宽带音频编码器，Skype 后来将其开源。具体可见[Wikipedia](https://zh.wikipedia.org/wiki/SILK)。

这种编码类型在其他地方很少见，所以绝大部分播放器无法直接播放。为了方便，可以将它转为`.mp3`格式，~~然后收藏枕宝语音偷偷听~~。

幸运的是，已经有大佬找齐了源码，并制作了批量转化脚本，开源到了Github项目：[silk-v3-decoder](https://github.com/kn007/silk-v3-decoder)。

## 快速使用

首先，需要找到语音消息对应的`.amr`文件所在位置。

由于 QQ 和 VX 版本繁多，且各个版本默认存储位置不同，还能自定义更改，在这里更推荐使用[Everything](https://www.voidtools.com/zh-cn/)查找`.amr`文件位置：

![everything-example](/images/post/everything-example.png)

根据文件创建日期排序，可以轻松找到特定语音消息对应的文件，将其复制出来，到一个方便找到的位置。

然后，我们下载前面所说的项目[silk-v3-decoder](https://github.com/kn007/silk-v3-decoder)。可以使用`git clone`或直接[点击此处下载](https://github.com/kn007/silk-v3-decoder/archive/refs/heads/master.zip)并解压。

如果你是Linux系统，想必可以根据Readme使用，不必再在此赘述。如果你是Windows系统，打开上述解压后的文件目录中`silk-v3-decoder-master\windows`路径，双击`silk2mp3.exe`运行。（此处windows系统自带的杀毒软件可能报毒，如果不放心可以自行删除）

![silk-example](/images/post/silk-example.png)

界面十分简单易用，也不再详细介绍。
