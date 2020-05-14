---
layout: post
title: Cookie和Session关系和区别
categories: [Web, Front-end, Back-end]
description: 本文分别对Cookie与Session做一个介绍和总结，并分别对两个知识点进行对比分析，让大家对Cookie和Session有更深入的了解。
keywords: Web, Cookie, Seesion
---

如果被问到“Cookie和Session的区别”，大家基本都会知道：Session比Cookie安全；Session是存储在服务器端的，Cookie是存储在客户端的。本文分别对Cookie与Session做一个介绍和总结，并分别对两个知识点进行对比分析，让大家对Cookie和Session有更深入的了解。

## 什么是HTTP

首先介绍什么是HTTP，因为Cookie和Session都和HTTP有密切关系。

**HTTP：HyperText Transfer Protocol（超文本传输协议）** 是一种用于分布式、协作式和超媒体信息系统的应用层协议。HTTP是万维网的数据通信的基础。设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法。通过HTTP或者HTTPS协议请求的资源由统一资源标识符（Uniform Resource Identifiers，URI）来标识。HTTP是无状态协议，说明它不能以状态来区分和管理请求和响应。也就是说，服务器单从网络连接上无从知道客户身份。可是怎么办呢？就给客户端们颁发一个通行证吧，每人一个，无论谁访问都必须携带自己通行证。这样服务器就能从通行证上确认客户身份了。这就是Cookie的工作原理。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/cookie_mechanism.png">
</div>

## Cookie

### 什么是Cookie

Cookie翻译过来是“小甜饼”，Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，实际上Cookie是服务器在**本地机器**上存储的一小段文本，并随着每次请求发送到服务器。**Cookie技术通过请求和响应报文中写入Cookie信息来控制客户端的状态。**

Cookie会根据响应报文里的一个叫做`Set-Cookie`的首部字段信息，通知客户端保存Cookie。当客户端再向服务端发起请求时，客户端会自动在请求报文中加入Cookie值之后发送出去.之后服务端发现客户端发送过来的Cookie后，会检查是那个客户端发送过来的请求，然后比对服务器上的记录，最后得到了之前的状态信息。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/cookie_process_1.png">
</div>

客户端保存了Cookie之后的发起请求。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/cookie_process_2.png">
</div>

下图很清晰地展示了发生Cookie交互的情景，HTTP请求报文和响应报文的内容如图所示。第一可以很明显的可出首部字段内没有Cookie的相关信息，其次也能看到`set-Cookie`里的信息，这就是服务器端生成的Cookie信息。看之后请求，请求报文里都自动发送Cookie信息了。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/communication_with_cookie.png">
</div>

### `set-Cookie`的字段的属性

```
Set-Cookie: logcookie=3qjj; expires=Wed, 13-Mar-2019 12:08:53 GMT; Max-Age=31536000; path=/; domain=fafa.com;secure; HttpOnly;
```

以上面的`set-cookie`为例，说一下`set-cookie`的属性。

- `logcookie=3qjj`，赋予Cookie的名称和值，`logcookie`是名字，`3qjj`是值。
- `expires`，是设置Cookie有效期。当省略`expires`属性时，Cookie仅在关闭浏览器之前有效。可以通过覆盖已过期的Cookie，设置这个Cookie的过期时间是过去的时间，实现对客户端Cookie的实质性删除操作。
- `path`，是限制指定Cookie的发送范围的文件目录。不过另有办法可避开这项限制，对其作为安全机制的效果不能抱有期待。
- `domain`，通过`domain`属性指定的域名可以做到与结尾匹配一致。比如，指定`domain`是`foo.com`，除了`foo.com`，`www.foo.com`等都可以发送Cookie。
- `secure`，设置Web页面只有在HTTPS安全连接时，才可以发送Cookie。HTTP则不可以进行回收。
- `HttpOnly`，它使Javascript脚本无法获得Cookie，通过上述设置，通常从Web页面内还可以对Cookie进行读取操作。但使用Javascript的`document.cookie`就无法读取附加`HttpOnly`属性后的Cookie的内容了

## Session管理和Cookie应用

### 什么是Session

服务端执行Session机制时候会生成Session的`id`值，这个`id`值会发送给客户端，客户端每次请求都会把这个`id`值放到http请求的头部发送给服务端，而这个`id`值在客户端会保存下来，保存的容器就是Cookie，因此当我们完全禁掉浏览器的Cookie的时候，服务端的Session也会不能正常使用。 

PHP中的Session在默认情况下是使用客户端的Cookie来保存`Session id`的，所以当客户端的cookie出现问题的时候就会影响Session了。必须注意的是：Session不一定必须依赖Cookie，这也是Session相比Cookie的高明之处。当客户端的Cookie被禁用或出现问题时，PHP会自动把`Session ID`附着在URL中，这样再通过`Session ID`就能跨页使用Session变量了。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/session_process.jpg">
</div>

- 客户端把信息放入报文的实体部分，通常是以POST 方法把请求发送给服务器。
- 服务器会发放用以识别用户的`Session ID`。通过验证从客户端发送过来的信息进行验证，然后把用户的认证状态与`Session ID` 绑定后记录在服务器端。向客户端返回响应时，会在首部字段Set-Cookie 内写入`Session ID`（如`PHPSESSID=l128ogl...`）。你可以把`Session ID` 想象成一种用以区分不同用户的唯一`id`。
- 客户端接收到从服务器端发来的`Session ID` 后，会将其作为Cookie 保存在本地。下次向服务器发送请求时，浏览器会自动发送Cookie，所以`Session ID` 也随之发送到服务器。服务器端可通过验证接收到的`Session ID` 验证状态。

### Cookie与Session的区别

- Cookie数据存放在客户的浏览器（客户端）上，Session数据放在服务器上，但是服务端的Session的实现对客户端的Cookie有依赖关系的；
- Cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗，考虑到安全应当使用Session；
- Session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能。考虑到减轻服务器性能方面，应当使用Cookie；
- 单个Cookie在客户端的限制是3K，就是说一个站点在客户端存放的Cookie不能超过3K；

## 参考

[Cookie和Session关系和区别](https://juejin.im/post/5aa783b76fb9a028d663d70a)