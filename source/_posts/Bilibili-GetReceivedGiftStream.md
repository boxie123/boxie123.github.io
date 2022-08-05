---
title: Bilibili GetReceivedGiftStream
author: 铂屑
date: 2022-08-03 23:47:36
updated: 2022-08-03 23:47:36
tags:
    - Python
    - 分享
    - 文档
categories:
    - 后端编程
---

简单记录一下 Python 项目 [Bilibili_GetReceivedGiftStream](https://github.com/boxie123/Bilibili_GetReceivedGiftStream) 的开发思路。

某天听到[鸽宝](https://space.bilibili.com/1485569/)说b站查询礼物记录的功能相当反人类，需要一天一天找。

我心想：*这不简单，直接全都爬下来。*

于是诞生了此项目。

<!-- more -->

## login.py

首先，肯定要登录b站账号。

本模块主要功能是获取登录二维码，扫码登陆后记录`cookies`，并在每次运行时判断`cookies`是否有效。

那么问题来了：

### 什么是 Cookies ？

> Cookie，有时也用其复数形式 Cookies。类型为“小型文本文件”，是某些网站为了辨别用户身份，进行Session跟踪而储存在用户本地终端上的数据（通常经过加密），由用户客户端计算机暂时或永久保存的信息。（摘自百度百科）

简单理解，`cookies`就是包含在你发送给服务器的请求数据中的一小段文本，用于识别你的身份。比如 bilibili 的`cookies`包含的是账号登陆信息。

### 如何获取 Cookies ？

>当用户试图访问某Web站点中需要认证的资源时，Web服务器会检查用户是否提供了认证Cookie，如果没有，则将用户重定向到登录页面。在用户成功登录后，Web服务器会产生认证Cookie，并通过HTTP响应中的Set-Cookie头发送给客户端，用于对用户随后的请求进行检查和验证，接着将用户重定向到初始请求的资源

通过扫码、账号密码、验证码……等方法登录账号后，服务器会产生唯一的一串`cookies`文本，并返回到你的计算机储存起来。下次再访问此网页的时候，发送给服务器的请求就会带上储存的`cookies`，服务器经过比对可识别已登录。

而在已经登陆过的浏览器上，打开开发者工具，同样可以看到本网页对应的`cookies`：

![cookies-example](https://boxie123-1305125924.cos.ap-beijing.myqcloud.com/images/post/cookies-example.png)

让用户自己去网页复制`cookies`虽然可行，~~但会显得我技术不行！~~ 所以登录模块应运而生。

### 模拟登录

本文件采用模拟扫码登陆，获取返回的`cookies`值。

首先来到 bilibili 的[登陆页面](https://passport.bilibili.com/login)，通过开发者工具的网络日志，可以看到，首先向网址`https://passport.bilibili.com/qrcode/getLoginUrl`发送了`Get`请求，返回了`oauthKey`和`url`等数据，其中`oauthKey`就是此二维码的**验证密钥**，我们暂时储存下来。

再次向刚刚得到的`url`发送`Get`请求，得到二维码对应的链接。

> 咕咕咕 什么时候有时间再继续写

## getGift.py

## main.py
