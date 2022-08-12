---
title: 从 Requests 转向 Httpx
author: 铂屑
date: 2022-08-10 00:24:27
updated: 2022-08-10 16:35:53
toc: false
tags:
    - Python
    - 分享
    - 笔记
categories:
    - 后端编程
---

在上一个项目（[《Bilibili GetReceivedGiftStream》](https://boxie123.github.io/Bilibili-GetReceivedGiftStream/)）中，登陆部分使用单线程的 Requests ，爬取数据部分采用异步的 Aiohttp ，今日有时间删掉这两个，改成既能同步又能异步的 **Httpx** ，在此记录一下需要修改的部分。

主要是 Requests 与 Httpx 的 Cookies 部分。

<!-- more -->

## Client 与 Session

在官方文档中写道，`Client`可代替`Session`：

> If you are coming from Requests, `httpx.Client()` is what you can use instead of `requests.Session()`.

但同时他们也有一些区别（详见官方文档 [兼容性](https://www.python-httpx.org/compatibility/) 一节）

> We prefer enforcing a stricter API here because it provides clearer expectations around cookie persistence, particularly when redirects occur.

这使得一些参数仅可在 `Client` 中设置，而不可基于每个请求单独设置，例如：

```py
# 这样是允许的
client = httpx.Client(cookies=...)
client.post(...)
```

```py
# 这样是禁止的
client = httpx.Client()
client.post(..., cookies=...)
```

同理，像`verify`等参数仅可在`Client`中设置。

## Cookie 和 Cookie.jar

首先，我们先看`http.cookiejar`都有哪些特征：

```py
import http.cookiejar as cookielib

cookies = cookielib.LWPCookieJar("bzcookies.txt")
print(cookies)
print(type(cookies))
```

输出：
```py
<LWPCookieJar[]>
<class 'http.cookiejar.LWPCookieJar'>
```

可以看到，此时cookiejar中内容为空，我们需要通过`load`方法加载 Cookie 的值：

```py
cookies.load()

print(cookies)
print(type(cookies))
```

输出中包含 Cookie 值内容。此时，我们可以用`cookies.save()`来将 Cookies 值保存到文件`bzcookies.txt`中。

当使用 Requests 时，新建`session`后，将`LWPCookieJar`赋值给`session`的`cookies`属性：

```py
cookies = cookielib.LWPCookieJar("bzcookies.txt")

session = requests.Session()
print(session.cookies)
print(type(session.cookies))

session.cookies = cookies
print(session.cookies)
print(type(session.cookies))
```

输出：
```py
<RequestsCookieJar[]>
<class 'requests.cookies.RequestsCookieJar'>
<LWPCookieJar[]>
<class 'http.cookiejar.LWPCookieJar'> 
```

可见，赋值直接改变了`session.cookies`的类型，使其从`RequestsCookieJar`变为`LWPCookieJar`。

但在 httpx 中不同：

```py
cookies = cookielib.LWPCookieJar("bzcookies.txt")

client = httpx.Client()
print(client.cookies)
print(type(client.cookies))

client.cookies = cookies
print(client.cookies)
print(type(client.cookies))
```

输出：

```py
<Cookies[]>
<class 'httpx.Cookies'>
<Cookies[]>
<class 'httpx.Cookies'>
```

赋值并未改变`client.cookies`的类型，那传递的值保存在哪里了呢？

```py
print(client.cookies.jar)
print(type(client.cookies.jar))
```

输出：

```py
<LWPCookieJar[]>
<class 'http.cookiejar.LWPCookieJar'>
```

`httpx.Cookies`将 CookieJar 保存在`client.cookies.jar`中。

同理，要使用`load()`或`save()`，需写为：

```py
client.cookies.jar.load()
client.cookies.jar.save()
```

诸如此类的差异很难在一一列出，如果你有其他想法，在下面评论区留言吧！

<br>

<br>