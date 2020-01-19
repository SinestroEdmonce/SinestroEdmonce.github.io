---
layout: post
title: Express中的静态资源服务
categories: [Express, Node.js]
description: 简要介绍了Express框架中使用静态资源服务的方法。可以通过使用API来指定static资源的相对路径，方便URL（统一资源定位符）进行定位。
keywords: Express, Node.js, static resources
---

``Express``中可以默认设置一些静态资源服务，这样方便统一资源定位符（url）做出比较简单的定位，既可以简化代码也可以精简url链中的信息内容。

## Express启动

首先需要创建一个``express``对象:  

```javascript
var express = require("express");
var app = express();
```

## 静态服务

使用方法是第一个参数是url链访问路径，第二个参数是静态资源的相对路径:  

```javascript
app.usc(args[0], args[1]) // 第一个是url访问路径，第二个是静态资源相对路径
```

1. ``/public/``静态资源服务开放，如果只有第二个参数，则在url链中无需加上``/public/xxx``，比如原先请求资源需要``http://localhost:8000/public/xxxx.css``，而现在只需要``http://localhost:8000/xxxx.css``即可。

    ```javascript
    app.use(express.static("public"))
    ```

2. ``/static``静态资源，任意静态资源开放都可以只用第二个参数表明路径。

    ```javascript
    app.use(express.static("files"))
    ```

3. ``/public/``放在第一个参数中表明在请求资源时必须加上``/public/xxx``。

    ```javascript
    app.use("/public", express.static("public"))
    ```

4. 如果第一个参数存在，但和实际静态资源存放位置的路径不一致，则相当于在url链中使用第一个参数作为路径别名。

    ```javascript
    app.use("/static", express.static("public"))
    ```

    如上述代码，若想要请求``./public/``路径下的静态资源，则需要``http://localhost:8000/static/xxxx.css``。

5. 使用绝对路径来实现。

    ```javascript
    app.use("/static", express.static(path.join(__dirname, "public")))
    ```