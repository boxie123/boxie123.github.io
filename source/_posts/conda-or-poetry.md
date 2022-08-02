---
title: Anaconda完全卸载与Poetry安装
author: 铂屑
date: 2022-08-02 19:22:33
updated: 2022-08-02 19:22:33
tags:
    - Python
    - 分享
    - 随笔
categories:
    - 后端编程
---

> 先说结论，选择 `poetry`

另：鉴于 `conda` 与 `poetry` 混合使用会造成一系列不可知隐患，暂不考虑。

在使用 `conda` 一段时间后，愈发觉得它的臃肿与低效，尤其在包管理方面的弱势。
且我并没有切换 `python` 版本的刚需，决定改用 `poetry`。

<!-- more -->

首先，卸载 `conda`（x）

## 卸载 anaconda

使用软件自带的`uninstall.exe`肯定是卸载不干净的，之后手动删除又苦于找不到所有配置文件位置，
会对下次安装或者其他 `python` 环境造成影响。

参考 [anaconda官网的卸载流程](https://docs.anaconda.com/anaconda/install/uninstall/)，
进行完全卸载：

1. 首先，打开 `anaconda` 的命令行工具`Anaconda Prompt`（可在菜单栏找到）。

1. 安装 `anaconda-clean` 包
    ```sh
    conda install anaconda-clean
    ```

1. 运行 `anaconda-clean`
    ```sh
    # If you want to confirm each file and directory you are deleting
    anaconda-clean

    # If you don't want to be asked about each file and directory
    anaconda-clean --yes
    ```

1. `anaconda-clean`这个命令可能会将 `Anaconda` 的配置文件等都移动到备份文件夹里面，备份文件夹的位置在`C:\Users\用户名.anaconda_backup`。**可直接删除**。

1. 打开 `Anaconda` 安装目录，删除用户文件夹中的环境`anaconda3\envs` 和包 `anaconda3\pkgs` 文件夹。（数G大小）
    > 记得及时清空回收站鸭

1. 打开添加或删除程序并卸载 `Anaconda` 安装或 `Python` 版本。

    > 打开<kbd>Ctrl</kbd> + <kbd>R</kbd>，输入`appwiz.cpl`快速打开

1. 检查环境变量中是否有残留。

    > 打开<kbd>Ctrl</kbd> + <kbd>R</kbd>，输入`sysdm.cpl`快速打开

    <span class="heimu" title="（确信）">~~其实不清理也没什么影响。~~</span>

至此， `Anaconda` 完全卸载完成！你的计算机又回到安装它之前的状态啦！

## Poetry 安装

> 在此仅介绍 ***Windows*** 安装，更多信息详见 [官方文档](https://python-poetry.org/docs/)

在powershell中输入：
```sh
(Invoke-WebRequest -Uri https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py -UseBasicParsing).Content | python -
```
因为`raw.githubusercontent.com`域名被墙，此方法需要科学上网。

此命令将自动添加环境变量，若想手动添加，可在安装时添加命令`--no-modify-path`。

> 可以看到用户变量的`Path`中添加了一条`C:\Users\用户名\.poetry\bin`

最后，打开一个新的`shell`或`cmd`，输入
```sh
poetry --version
```

若能看到`poetry`版本号则已安装成功。
