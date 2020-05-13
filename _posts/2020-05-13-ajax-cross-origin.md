---
layout: post
title: 跨域错误和AJAX跨域的解决方案
categories: [Cross-origin， Javascript， AJAX]
description: 这篇文章简单介绍了在使用AJAX跨域时几种常见的跨域错误（简单介绍分析了浏览器控制台显示的跨域错误相关的信息），并且以AJAX跨域为例，介绍一种跨域的解决方案
keywords: Cross-origin, Javascript, AJAX
---

AJAX是前端开发中很常用的框架（库），这篇文章是对Web前端开发常见的AJAX跨域问题出现的各类错误信息的一个简单概括，以AJAX跨域的解决方法来说明跨域策略。

在前端开发中，跨域问题是很常见的问题之一，在我的博客中也有对各种常见的跨域策略（跨域解决方案）的介绍，详见：[Web前端常见的跨域（cross-origin）解决方案](https://sinestroedmonce.github.io/2020/05/12/front-end-cross-origin-strategies/)

本文的目的是分析AJAX跨域的相关问题，专注于AJAX跨域会出现的错误信息和AJAX跨域的解决方法，参考了这篇博客文章：[ajax跨域，这应该是最全的解决方案了](https://segmentfault.com/a/1190000012469713)

## 什么是AJAX跨域

### AJAX跨域的原理

AJAX出现请求跨域错误问题，主要原因就是因为浏览器的“同源策略”，可以参考：[浏览器同源政策及其规避方法(阮一峰)](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)

### CORS请求原理

CORS是一个W3C标准，全称是“跨域资源共享”（Cross-origin resource sharing）。它允许浏览器向跨源服务器，发出`XMLHttpRequest`请求，从而克服了AJAX只能同源使用的限制。

基本上目前所有的浏览器都实现了CORS标准，其实目前几乎所有的浏览器AJAX请求都是基于CORS机制的，只不过可能平时前端开发人员并不关心而已（所以说其实现在CORS解决方案主要是考虑后台该如何实现的问题）。

关于CORS，可以参考：
[跨域资源共享CORS详解(阮一峰)](http://www.ruanyifeng.com/blog/2016/04/cors.html)。另外，这里也整理了一个实现原理图(简化版):

![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/cors_mechanism.png)

### 如何判断CORS是否为简单请求?

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。只要同时满足以下两大条件，就属于简单请求。

- 请求方法是以下三种方法之一：`HEAD`，`GET`，`POST`
- HTTP的头信息不超出以下几种字段：

    - Accept
    - Accept-Language
    - Content-Language
    - Last-Event-ID
    - Content-Type(只限于三个值application/x-www-form-urlencoded、 multipart/form-data、text/plain)

凡是不同时满足上面两个条件，就属于非简单请求。

### AJAX跨域的表现

AJAX请求时，如果存在跨域现象，并且没有进行解决，会有如下表现：（注意，是AJAX请求，请不要说为什么http请求可以，而AJAX不行，因为AJAX是伴随着跨域的，所以仅仅是http请求状态为`OK`是不行的）

- 第一种现象：`No 'Access-Control-Allow-Origin' header is present on the requested resource`，并且`The response had HTTP status code 404`

    ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/no-access-control-allow-origin-404.png)

    出现这种情况的原因如下：
    
    - 本次AJAX请求是“非简单请求”，所以请求前会发送一次预检请求(`OPTIONS`)
    - 服务器端后台接口没有允许`OPTIONS`请求，导致无法找到对应接口地址

    解决方案：后端允许`OPTIONS`请求。

- 第二种现象：`No 'Access-Control-Allow-Origin' header is present on the requested resource`，并且`The response had HTTP status code 405`

    ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/no-access-control-allow-origin-405.png)



    这种现象和第一种有区别，这种情况下，后台方法允许`OPTIONS`请求，但是一些配置文件中（如安全配置），阻止了`OPTIONS`请求，才会导致这个现象。


    解决方案：后端关闭对应的安全配置。

- 第三种现象：`No 'Access-Control-Allow-Origin' header is present on the requested resource`，并且`status 200`

    ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/no-access-control-allow-origin-200.png)

    这种现象和第一种和第二种有区别，这种情况下，服务器端后台允许`OPTIONS`请求，并且接口也允许`OPTIONS`请求，但是头部匹配时出现不匹配现象。比如`origin`头部检查不匹配，比如少了一些头部的支持（如常见的`X-Requested-With`头部），然后服务端就会将response返回给前端，前端检测到这个后就触发`XHR.onerror`，导致前端的浏览器控制台报错。
    
    解决方案：后端增加对应的头部支持。

- 第四种现象：`header contains multiple values '*，*'`

    ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/multiple-access-control-allow-origin.jpg)

    表现现象是，后台响应的http头部信息有两个`Access-Control-Allow-Origin:*`。这种问题出现的主要原因就是进行跨域配置的人不了解原理，导致了重复配置，如：
    
    - 常见于`.Net`后台(一般在`web.config`中配置了一次`origin`，然后代码中又手动添加了一次`origin`，例如代码手动设置了返回`*`
    - 常见于`.Net`后台（在IIS和项目的`web.config`中同时设置`Origin:*`）

    解决方案（一一对应）:

    - 建议删除代码中手动添加的`*`，只用项目配置中的即可
    - 建议删除IIS下的配置`*`，只用项目配置中的即可

## 如何解决AJAX跨域

一般AJAX跨域解决就是通过JSONP解决或者CORS解决。（需要注意的是：现在已经几乎不会再使用JSONP了）

### JSONP方式解决跨域问题

JSONP解决跨域问题是一个比较古老的方案（实际中不推荐使用），这里做简单介绍（实际项目中如果要使用JSONP，一般会使用jQuery等对JSONP进行了封装的类库来进行AJAX请求）

#### 实现原理

JSONP之所以能够用来解决跨域方案，主要是因为`<script>`脚本拥有跨域能力，而JSONP正是利用这一点来实现。具体原理如图：

![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/jsonp_mechanism.png)

#### 实现流程

JSONP的实现步骤大致如下（可以参考我博客中详解跨域解决方案的那篇文章）：

- 客户端网页网页通过添加一个`<script>`元素，向服务器请求JSON数据，这种做法不受同源政策限制：

    ```javascript
    function addScriptTag(src) {
        var script = document.createElement('script');
        script.setAttribute("type", "text/javascript");
        script.src = src;
        document.body.appendChild(script);
    }

    window.onload = function () {
        addScriptTag('http://example.com/ip?callback=foo');
    }

    function foo(data) {
        console.log('response data: ' + JSON.stringify(data));
    };
    ```

    请求时，接口地址是作为构建出的脚本标签的`src`的，这样，当脚本标签构建出来时，最终的`src`是接口返回的内容

- 服务端对应的接口在返回参数外面添加函数包裹层：

    ```javascript
    foo({
        "test": "testData"
    });
    ```

    由于`<script>`元素请求的脚本，直接作为代码运行。这时，只要浏览器定义了`foo`函数，该函数就会立即调用。作为参数的JSON数据被视为Javascript对象，而不是字符串，因此避免了使用`JSON.parse`的步骤。注意，一般的JSONP接口和普通接口返回数据是有区别的，所以接口如果要做JSONO兼容，需要进行判断是否有对应`callback`关键字参数，如果有则是JSONP请求，返回JSONP数据，否则返回普通数据。

#### Hint

基于JSONP的实现原理，所以JSONP只能是`GET`请求，不能进行较为复杂的`POST`和其它请求，所以遇到那种情况，就得参考下面的CORS解决跨域了。

### CORS解决跨域问题

CORS的原理上文中已经介绍了，这里主要介绍的是，实际项目中，后端应该如何配置以解决问题（因为大量项目实践都是由后端进行解决的），这里以Nodejs为例:

- Nodejs的后台也相对来说比较简单就可以进行配置。只需用`express`进行如下配置:

    ```javascript
    import express = require("express");
    let app = express();

    app.all('*', function(req, res, next) {
        res.header("Access-Control-Allow-Origin", "*");
        res.header("Access-Control-Allow-Headers", "X-Requested-With");
        res.header("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
        res.header("X-Powered-By", '3.2.1')
            //这段仅仅为了方便返回json而已
        res.header("Content-Type", "application/json;charset=utf-8");
        if(req.method == 'OPTIONS') {
            //让options请求快速返回
            res.sendStatus(200);
        } else {
            next();
        }
    });
    ```

#### OPTIONS预检的优化

加上下面这个头部后，可以缓存此次请求的秒数：

```
Access-Control-Max-Age:
```

在这个时间范围内，所有同类型的请求都将不再发送预检请求而是直接使用此次返回的头作为判断依据，可以大幅优化请求次数。

## 如何分析AJAX跨域

上述已经介绍了跨域的原理以及如何解决，但实际过程中，发现仍然有很多人对照着类似的文档无法解决跨域问题，主要体现在，前端人员不知道什么时候是跨域问题造成的，什么时候不是，因此这里稍微介绍下如何分析一个请求是否跨域。

### 抓包请求数据

第一步得知道我们的AJAX请求发送了什么数据，接收了什么，基于Chrome即可查看：

- Chrome浏览器打开对应发生AJAX的页面，打开`Dev Tools`（开发者工具）
- 发送AJAX请求
- `Dev Tools` -> `NetWork` -> `XHR`，然后找到刚才的AJAX请求

下面是一个正常的请求：

![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/normal_response.png)

上述请求是一个正确的请求，为了方便，我把每一个头域的意思都表明了，我们可以清晰的看到，接口返回的响应头域中，包括了：

```
Access-Control-Allow-Headers: X-Requested-With,Content-Type,Accept
Access-Control-Allow-Methods: Get,Post,Put,OPTIONS
Access-Control-Allow-Origin: *
```

所以浏览器接收到响应时，判断的是正确的请求，自然不会报错，成功的拿到了响应数据。

### Hint

基本上分析一个AJAX请求，通过Chrome就可以知道了发送了什么数据，收到了什么数据，然后再一一比对就知道问题何在了。
