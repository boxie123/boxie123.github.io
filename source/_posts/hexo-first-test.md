---
title: 初识hexo
author: 铂屑
date: 2022-07-23 04:57:57
updated: 2022-07-26 16:06:42
tags:
    - 随笔
    - 笔记
    - hexo
categories: 
    - 页面开发
katex: true
---

不知从何时起有了建一个属于自己的页面的想法，碍于时间与金钱成本，久久未能实现。

## 相遇
不久前， ***GitHub*** 的 ***For You*** 功能为我推荐了[云游君](https://github.com/YunYouJun)，他的文章[《教你如何从零开始搭建一个属于自己的网站》](https://www.yunyoujun.cn/share/how-to-build-your-site/)让我拥有一个自己网站的想法前所未有地重燃起来。

<!-- more -->

## 开始搭建
值得庆幸的是，在此之前 $python、java$ 等众多语言已经教会我足够的基础，在搭建的过程中并未遇到太多波折。
> 主要是 $git$ 以及 $cmd$ 的使用

### Node.js
> 从开天辟地之 `JavaScript` 说起，`JavaScript` 简写为 `js` ，是大量应用于网页构建的**脚本语言**，一般通过浏览器运行，语法与 `Java` 相似。***Node.js***是基于**chrome v8**引擎的`js`运行环境，为`js`运行提供了除浏览器环境之外的选择。

***Node.js*** 的安装以及环境变量配置参见百度，与`python`类似，并不复杂。

其后`cmd`中`npm`也类似于`python`的`pip`。

### Git & GitHub
> Git 是一个开源的分布式版本控制系统，由 Linus Torvalds（同时也是 Linux 的作者）为了管理 Linux 开发而开发。
简而言之，是一个版本管理工具。

必备的基础工具，之前已安装过，不再赘述。

### Hexo
> 基于***Node.js***的博客框架，具有完善的生态，众多功能强大的插件，构建快速简洁。

```sh
# -g 意为为全局安装，否则将安装到当前文件夹
npm install hexo-cli -g
# 新建名为boxie123.github.io的文件夹并初始化框架
hexo init boxie123.github.io
```

云游君似乎正在构建另一套[代替 Hexo 的博客框架](https://github.com/YunYouJun/valaxy)，推荐大家去尝试一下。

```sh
# 定位工作目录到上一步新建的文件夹中
cd boxie123.github.io
# 默认安装所有 `package.json` 文件中提到的包
npm install
# 可以缩写成 hexo s，开启本地服务器
hexo server
```

打开浏览器，在地址栏中输入 `localhost:4000` 就可以看到本地的网页了。

### hexo-theme-yun

```sh
npm i hexo-theme-yun
```

编辑`_config.yml`配置文件，新建`_config.yun.yml`用户配置文件并编辑。

### 部署

```sh
# 清除缓存文件
hexo clean
# 生成静态文件
hexo g
# 初始化Git仓库（仅需一次）
git init
# 新建并切换到hexo分支
git checkout -b hexo
# 安装部署插件
npm install hexo-deployer-git
```

在正式部署前，需在`_config.yml`中编辑部署配置：
```yaml
deploy:
  type: git
  repo: 你此前新建的仓库的链接 # 比如：https://github.com/YunYouJun/yunyoujun.github.io
  branch: master # 默认使用 master 分支
  message: Update Hexo Static Content # 你可以自定义此次部署更新的说明
```

```sh
# 正式部署
hexo deploy
```

> 等待完成后，打开网址 https://boxie123.github.io 成功看到线上网站。
>
> 但是线上网站的内容却是`boxie123.github.io`库中`main`分支的`ReadMe.md`文件，而非自动生成的`master`分支内容。
>
> 打开`repo`设置，将`master`分支设置为默认分支，删除`main`分支，再次部署，打开网址，显示正常。

### 备份与自动部署

```sh
# 与远程 Git 仓库建立连接，只此一次即可
git remote add origin https://github.com/boxie123/boxie123.github.io
# 添加到缓存区
git add -A
git commit -m "这次做了什么更改，简单描述下即可"
# 推送至远程仓库
git push
# 第一次提交，你可能需设置一下默认提交分支
# git push --set-upstream origin hexo
```

编写`bash`脚本，方便之后提交。
> 详见[`update.sh`](https://github.com/boxie123/boxie123.github.io/blob/hexo/update.sh)文件。

同理，每次部署都需要三条`hexo`命令，尝试通过`GitHub Action`完成自动部署。

~~云游君提供的`gh-pages.yml`文件中使用`pnmp`进行包管理，但前述过程仅使用`npm`，且由于我较不熟悉`pnmp`使用，
目前本项目并未实现自动部署。~~

参考[《初探无后端静态博客自动化部署方案》](https://blog.ichr.me/post/automated-deployment-of-serverless-static-blog/#GitHub-Actions)实现自动部署啦。

<span class="heimu" title="（确信）">~~其实手动输三条命令也不太麻烦是吧~~</span>

### 开始创作！

```sh
# 新建文章
hexo new post xxx
# 新建页面
hexo new page xxx
```

至此，页面创作之旅正式开始啦！

## 一些附加功能
网页当然是在不断完善的啦！

### 图床
本地图片加载实在有点太慢了，而且考虑到`GitHub`抽风的可能，决定改用国内的图床。

经对比，暂时使用腾讯云，初次开通**对象存储（COS）**服务赠送半年的50G储存以及三个月的每月5G下行流量。

你问三个月之后怎么办？那就交给三个月之后的我考虑吧。

> 所以本网站搭建到现在没花过一分钱哒。

### 评论区

跳转`GitHub Issues`评论毕竟不太方便，而且**GFW**时不时的屏蔽更是让国内大多数用户望而却步。

> ~~所以又抄了云游君的评论区~~

同样，使用`Waline`和`Vercel`搭建，参考[`Waline`文档](https://waline.js.org/guide/get-started.html)。

> 文档中的`Deploy`按钮指向的链接不知为何无法选中`Git Scope`，将链接中的`repository-url`改为`s`后恢复正常。

**注意：**搭建完成后第一个注册的账号默认为管理员，请先在本地完成注册再挂载到网页。

表情总是加载不出来于是直接去掉了，一起用可爱的颜文字吧！（＞人＜；）


## 结语

再次感谢云游君所写的主题与详细教程，愿更多想拥有自己网站的小伙伴早日实现愿望。

> 如需帮助，向我提 [Issues](https://github.com/boxie123/boxie123.github.io/issues?q=is:issue+%E5%88%9D%E8%AF%86) 和 [Discussion](https://github.com/boxie123/boxie123.github.io/discussions/new) 吧！
