---
layout: post
title: 后端渲染和前端渲染的一些速记
categories: [Back-end, Front-end]
description: 简单介绍一下前后端渲染的不同与优劣。基础概念的一些速记。
keywords: Front-end, Back-end, Render
---

前端（客户端）渲染和后端（服务器）渲染有比较明显的差异。这两种渲染方式各有优劣，在前端（客户端）渲染往往可以局部刷新，异步执行；而如果要求后端（服务器）渲染，则往往是给定的渲染，每次重新渲染都需要再向服务器请求数据。

## 前端（客户端）渲染

- 前端渲染的一些模版或者习惯往往如下``html5``的大致所示:  
    ```html
    <html>
        <head></head>
        <script type="tpl">
        ...
        </script>
        <script>
        ...
        $.ajax({...})
        </script>
    </html>
    ```

- 前端渲染流程一般是:
    - 收到服务器响应的字符串
    - 解析字符串，在解析过程中发现有新的``ajax``请求，则再次对服务器发起请求。
    - 获得新请求的响应结果后，模版引擎进行渲染。

- 前端渲染笼统上来说往往最少有两次，第一次请求获取页面，第二次请求获取动态数据。

## 后端（服务器）渲染

- 后端渲染的一些模版或者习惯往往如下:  

    ```html
    <html>
        <head></head>
        <body>
            <h1>hello {{ name }}</h1>
        </body>
    </html>
    ```

- 后端渲染流程一般是:
    - 从数据库（资源文件）读取网页文本字符串，例如``index.html``
    - 使用模版引擎进行渲染，在将文本响应给前端前就已经渲染完成。

- 对于后端（服务器）渲染一般只会请求一次。

## 总结

- 前后端渲染的区别:

    - 客户端渲染不利于``SEO``搜索引擎优化，一般利用服务端渲染。
    - 服务端渲染可以被爬虫爬取，客户端异步渲染是很难被抓取的。
    - 客户端渲染往往响应的更快，用户体验较好，无需整体页面刷新。

- 前后端渲染的使用:
    - 一般来说，一个``Web App``基本上前后端渲染会同时运用，既注重``SEO``优化，也注重用户体验。