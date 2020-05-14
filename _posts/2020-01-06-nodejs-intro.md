---
layout: post
title: Nodejs基础概念的一些笔记
categories: [Nodejs, Notes]
description: “Nodejs是什么”相关的一些基础知识点笔记，关于Nodejs的基础概念的速记。
keywords: Nodejs, Concepts
---

这篇笔记更像是一个对Node.js的基础概念的速写。简单回答了一些问题，例如“Node.js是什么”，“Node.js有哪些性质”，“Node.js能做什么”等等。这篇笔记实则是对Node.js的概览，宏观上理解Node.js的相关基础知识。

## Node.js是什么？

1. Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine.
    - Node.js不是一门语言。
    - Node.js不是库，也不是框架。
    - Node.js是一个JavaScript的运行时环境。
    - Node.js可以解析和执行JavaScript代码。
        - 浏览器中的JavaScript:
            - EcmaScript
            - BOM
            - DOM
        - Node.js中的JavaScript:
            - **没有BOM、DOM**
            - EcmaScript
            - 在Node这个JavaScript的运行时环境中为JavaScript提供了一系列服务器级别的操作API。

2. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient.
    - Node.js是事件驱动。
    - Node.js使用非阻塞IO模型（异步）。
    - Node.js轻量高效。

3. Node.js's package ecosystem, ``npm``, is the larges ecosystem of open source libraries in the world.
    - ``npm``是世界上最大的开源库生态系统。
    - 绝大多数JavaScript相关的包/库都存放在``npm``上。

## Node.js能做什么？

1. Web服务器（后端开发工具）。

2. 命令行工具（如``git``）。

3. 甚至在前端开发中也有所出现:
    - ``webpack``
    - ``gulp``
    - ``npm``