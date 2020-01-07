---
layout: post
title: Nodejs核心模块与模块化的一些笔记
categories: [Nodejs, Notes]
description: Nodejs核心模块的大致介绍。Nodejs模块化基础的一些速记。
keywords: Nodejs, Modulization
---

想要熟练掌握Node.js则必须熟悉Node为JavaScript提供的服务器层级的API。这些API绝大多数都被包装在一些具名的核心模块里。例如文件操作的``fs``核心模块，http服务构建的``http``模块，``path``路径操作模块，``os``操作系统信息模块等等……

## 核心模块

Node提供的模块一般称为核心模块。这些模块必须先使用``require``方法加载后才能使用:

```javascript
var fs = require("fs");
var http = require("http");
```

## 用户自定义模块

- 同样需要使用``require``方法进行加载，使用相对路径或者绝对路径。如果使用相对路径需要加上“./”来表示此模块是用户自定义模块，而不是Node提供的核心模块。  

- 在Node中，没有全局作用域，只存在模块作用域。（模块互相默认封闭）

- 在Node中，``require``方法有两个作用，一个是加载文件模块并执行代码，二是拿到被加载文件模块导出的接口对象。在每个文件模块中都提供了一个对象:``exports``。``exports``默认是一个空对象。所有需要被外部访问的成员挂载在``exports``上。

    ```javascript
    var foo = function(request, response) {
        console.log(request);
        console.log(response);
    }

    exports.foo = foo;
    ```