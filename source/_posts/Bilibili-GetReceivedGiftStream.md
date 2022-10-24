---
title: Bilibili GetReceivedGiftStream
author: 铂屑
date: 2022-08-03 23:47:36
updated: 2022-08-07 21:08:25
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

![cookies-example](/images/post/cookies-example.png)

让用户自己去网页复制`cookies`虽然可行，~~但会显得我技术不行！~~ 

更优雅的方式应是通过模拟登录自动获取`cookies`。

### 模拟登录

本文件采用模拟扫码登陆，获取返回的`cookies`值。

首先来到 bilibili 的[登陆页面](https://passport.bilibili.com/login)，通过开发者工具的网络日志，可以看到，首先向网址`https://passport.bilibili.com/qrcode/getLoginUrl`发送了 GET 请求，返回了`oauthKey`和`url`等数据，其中`oauthKey`就是此二维码的**验证密钥**，我们暂时储存下来。

但是在网络日志中并未找到二维码获取的网址，查找源代码，可看到以下代码：

```javascript
getQrcode: function() {
                    var e = this;
                    $.ajax({
                        url: "https://passport.bilibili.com/qrcode/getLoginUrl",
                        dataType: "json"
                    }).done(function(t) {
                        if (t.status) {
                            e.refer ? (e.refer.clear(),
                            e.refer.makeCode(t.data.url)) : e.$nextTick(function() {
                                e.refer = new QRCode(e.$refs.qrcode,{
                                    text: t.data.url,
                                    width: 140,
                                    height: 140
                                })
                            }),
                            e.key = t.data.oauthKey;
                            var i = decodeURIComponent(Object(f.h)("gourl"));
                            clearTimeout(e.cd),
                            e.cd = setTimeout(e.expire, e.cdTime),
                            clearInterval(e.loop),
                            e.loop = setInterval(function() {
                                $.ajax({
                                    url: "https://passport.bilibili.com/qrcode/getLoginInfo",
                                    dataType: "json",
                                    type: "POST",
                                    data: {
                                        oauthKey: e.key,
                                        gourl: i || (document.referrer || "")
                                    }
                                }).done(function(t) {
                                    t.status ? (reportMsgObj.qrcodescan_login = "success",
                                    window.reportObserver && window.reportObserver.forceCommit && window.reportObserver.forceCommit(),
                                    window.location.href = t.data.url) : t.status || -2 != t.message ? t.status || -5 != t.data || e.scanSucess() : e.expire()
                                })
                            }, e.loopTime)
                        }
                    })
                },
```

可看到是再次向刚刚得到的`url`发送 GET 请求，得到二维码对应的链接，并通过 js 直接生成 Base64 发送到前端。

转化为 Python 代码即为引入`qrcode`库将链接转为二维码。

> 使用`showpng`类新建线程，可不存储到本地，直接显示二维码图片。

同样，通过网络日志可以看到，每两秒浏览器就会向`https://passport.bilibili.com/qrcode/getLoginInfo`发送 POST 请求来确定登陆状态，其中：

> ‘data’: -4：二维码未失效
> 
> ‘data’: -5：已扫码
>
> ‘data’: -2：二维码已失效
>
> ‘status’: True：已登录

构建`while True`循环，并在每次循环后等待两秒。

访问登陆成功后返回的带参数的`url`，获取`cookies`值并存储。

### 验证登陆状态

同样，向`https://api.bilibili.com/x/web-interface/nav`发送 GET 请求，通过返回数据判断：

> 'code': -101 即为未登录；
>
> 'code': 0 即为已登录。

### Cookies 的储存与读取

因 requests 库的官网已无法访问（详见[此文](https://www.163.com/dy/article/H8QB97VK0511CUMI.html)），此处仅大致介绍 CookieJar 相关用法，并建议读者尽量转用`httpx`或`aiohttp`等代替。

其中`httpx`在几乎完美代替`requests`的基础上支持了异步操作。

什么是 CookieJar 呢？

> CookieJar 是 requests 定义的类，requests 只能保持 cookiejar 类型的 cookie ，而我们手动构建的 cookie 是 dict 类型的。所以要把 dict 转为 cookiejar 类型。

其中又包含四种子类：

- `CookieJar`：管理HTTP cookie值、存储HTTP请求生成的cookie、向传出的HTTP请求添加cookie的对象。整个cookie都存储在内存中，对CookieJar实例进行垃圾回收后cookie也将丢失。
- `FileCookieJar` (filename,delayload=None,policy=None)：从CookieJar派生而来，用来创建FileCookieJar实例，检索cookie信息并将cookie存储到文件中。filename是存储cookie的文件名。delayload为True时支持延迟访问访问文件，即只有在需要时才读取文件或在文件中存储数据。
- `MozillaCookieJar` (filename,delayload=None,policy=None)：从FileCookieJar派生而来，创建与Mozilla浏览器 cookies.txt兼容的FileCookieJar实例。
- `LWPCookieJar` (filename,delayload=None,policy=None)：从FileCookieJar派生而来，创建与libwww-perl标准的 Set-Cookie3 文件格式兼容的FileCookieJar实例。

> 一般来说我们仅会用到后两种。

此处采用`LWPCookieJar`保存获得的 Cookies 。首先导入 cookiejar 类：

```py
import http.cookiejar as cookielib
```

创建 session 链接并从文件读取 cookies：

```py
session = requests.session()
session.cookies = cookielib.LWPCookieJar(filename=filename)
```

最后，获取到 cookies 后，通过`session.cookies.save()`保存到本地，下次直接读取。

## getGift.py

因爬取礼物流水数据可能需要多次发送请求，本模块采用异步操作提高效率，引入`aiohttp`库。



> 咕咕咕 什么时候有时间再继续写

## main.py

## agent.py
