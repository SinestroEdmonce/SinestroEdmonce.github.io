---
layout: post
title: Web服务中Get和Post请求的区别
categories: [Web, Nodejs]
description: 在学Nodejs过程中对Get和Post请求的异同产生了一些疑惑，索性查了查资料，把自己了解到的相关概念与知识整理了一下，做了个速记。
keywords: Web, Nodejs, Post, Get
---

``Get``和``Post``请求还是有比较明显的不同的。在Web服务器设计和前后端交互中，需要比较熟悉地掌握这两个请求的区别，才能更加方便地进行接口设计和实现，下面记录了我自己了解和查阅资料知道的一些概念。

## Get和Post的区别

1. ``Get``请求一般用于请求获取数据；  
``Post``一般用于发送数据到后端服务器。

2. ``Get``请求也可传参到后台，但是其参数在浏览器的地址栏的url中可见，所以隐私性安全性较差，且参数长度也是有限制的；  
``Post``请求传递参数放在``Request Body``中，不会在url中显示，更安全，且参数长度无限制。 

3. ``Get``请求刷新浏览器或回退时没有影响；  
``Post``回退时会重新提交数据请求

4. ``Get``请求可被缓存；  
``Post``请求不会被缓存

5. ``Get``请求保留在浏览器历史记录中；  
``Post``请求不会保留在浏览器历史记录中

6. ``Get``请求可被收藏为书签；  
``Post``不能被收藏为书签

7. ``Get``请求只能进行url编码（application/x-www-form-urlencoded）；  
``Post``支持多种编码方式（application/x-www-form-urlencoded 或 multipart/form-data。为二进制数据使用多重编码。）

8. ``Get``请求比较常见的方式是通过url地址栏请求；  
``Post``最常见是通过form表单发送数据请求



## 更多理解

- ``Post``请求和``Get``请求都是``HTTP``的请求方式，本质上来说并无区别，底层实现都是基于``TCP/IP``协议。  

- ``Get``产生一个TCP数据包；``Post``产生两个TCP数据包。
    - 对于``Get``请求，浏览器会把http-header和data都发送出去，服务器响应200（返回数据）；
    - 对于``Post``请求，浏览器先发送header，服务器响应``100 CONTINUE``，浏览器再发送data，服务器响应``200 OK``（返回数据）。

- ``Get``与``Post``都有自己的语义，不能随便混用。

- 并不是所有浏览器都会在``Post``请求中发送两次包，``Firefox``就只发送一次。