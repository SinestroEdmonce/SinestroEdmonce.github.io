---
layout: post
title: 深入理解XMLHttpRequest
categories: [Web, AJAX]
description: AJAX技术的核心是XMLHttpRequest对象。我们使用XMLHttpRequest对象来发送一个AJAX请求。本文旨在详细介绍XMLHttpRequest对象和其相关的函数方法。
keywords: Web, AJAX, XMLHttpRequest
---

## `XMLHttpRequest`的发展历程

这是由微软首先引入的一个特性，其他浏览器提供商后来都提供了相同的实现。`XMLHttpRequest`已经得到广泛接受，后来W3C对它进行了标准化，提出了`XMLHttpRequest`标准。`XMLHttpRequest`标准又分为`Level 1`和`Level 2`。并非所有浏览器都完整地实现了`XMLHttpRequest Level 2`级规范，但所有浏览器都实现了它规定的部分内容。

`XMLHttpRequest Level 1`主要存在以下缺点：

- 不能发送二进制文件（如图片、视频、音频等），只能发送纯文本数据。
- 在发送和获取数据的过程中，无法实时获取进度信息，只能判断是否完成。
- 受同源策略的限制，不能发送跨域请求。

`Level 2`对`Level 1`进行了改进，`XMLHttpRequest Level 2`中新增了以下功能：

- 在服务端允许的情况下，可以发送跨域请求。
- 支持发送和接收二进制数据。
- 新增`formData`对象，支持发送表单数据。
- 发送和获取数据时，可以获取进度信息。
- 可以设置请求的超时时间。

下面的一行代码就可以创建`XMLHttpRequest`对象。

```javascript
const xhr = new XMLHttpRequest()
```

## `XMLHttpRequest`对象发送请求相关API

### 请求头相关

- `Accept`：客户端可以处理的内容类型。比如：`Accept: */*`。
- `Accept-Charset`：客户端可以处理的字符集类型。比如：`Accept-Charset: utf8`。
- `Accept-Encoding`：客户端可以处理的压缩编码。比如：`Accept-Encoding: gzip, deflate, br`。
- `Accept-Language`：客户端当前设置的语言。比如：`Accept-Language: zh-CN,zh;q=0.9,en;q=0.8`。
- `Connection`：客服端与服务器之间连接的类型。比如：`Connection: keep-alive`。
- `Cookie`：当前页面设置的任何`Cookie`。
- `Host`：发出请求页面所在的域。
- `Referer`：表示当前请求页面的来源页面的地址，即当前页面是通过此来源页面里的链接进入的。
- `User-Agent`：客户端的用户代理字符串。一般包含浏览器、浏览器内核和操作系统的版本型号信息。
- `Content-Type`：客户端告诉服务器实际发送的数据类型。比如：`Content-Type: application/x-www-form-urlencoded`。

### `open()`方法

`open()`方法用于初始化一个请求。`open()`方法接收三个参数：

- 第一个参数`method`：要发送的请求的类型。比如`GET`、`POST`、`PUT`、`DELETE`等。
- 第二个参数`url`：请求的`URL`。
- 第三个参数`async`：是否异步发送请求的布尔值。`true`为异步发送请求。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('get', '/userInfo', true)
```

调用`open()`方法并不会真正发送请求，而只是启动一个请求以备发送。

### `send()`方法

`send()`方法用于发送HTTP请求。`send()`方法接收一个参数：

- 第一个参数`data`：作为请求主体发送的数据。如果不需要通过请求主体发送数据，则必须传入`null`。该参数可以接收字符串、`FormData`、`Blob`、`ArrayBuffer`等类型。

```javascript
const xhr = new XMLHttpRequest()
xhr.send(null)
```

### `setRequestHeader()`方法

`setRequestHeader()`方法可以设置自定义的请求头部信息。`setRequestHeader()`方法接收二个参数：

- 第一个参数`header`：头部字段的名称。
- 第二个参数`value`：头部字段的值。

要成功发送请求头部信息，此方法必须在`open()`和`send()`之间调用。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('get', '/server', true)
xhr.setRequestHeader('MyHeader', 'MyValue')
xmlhttp.send()
```

### `readyState`属性和`onreadystatechange`事件

`readyState`属性表示请求/响应过程的当前活动阶段。这个属性的值如下：

- 0（`UNSENT`）未初始化。尚未调用`open()`方法。
- 1（`OPENED`）启动。已经调用`open()`方法，但没有调用`send()`方法。
- 2（`HEADERS_RECEIVED`）发送。已经调用`send()`方法，但尚未接收到响应。
- 3（`LOADING`）接收。已经接收到部分响应数据。
- 4（`DONE`）完成。已经接收到全部响应数据。

只要`readyState`属性的值发生变化，都会触发一次`onreadystatechange`事件。利用这个事件来检测每次状态变化后`readyState`的值。一般情况下只对`readyState`值为4的阶段做处理，这时所有数据都已经就绪。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('get', '/server', true)
xhr.onreadystatechange = function () {
  if(xhr.readyState !== 4) {
    return  
  }
  if(xhr.status >= 200 && xhr.status < 300) {
    console.log(xhr.responseText)
  }
}
xhr.send(null)
```

### `timeout`属性和`ontimeout`事件

`timeout`属性表示请求在等待响应多少毫秒之后就终止。如果在规定的时间内浏览器还没有接收到响应，就会触发`ontimeout`事件处理程序。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('get', '/server', true)
//将超时设置为3秒钟
xhr.timeout = 3000 
// 请求超时后请求自动终止，会调用 ontimeout 事件处理程序
xhr.ontimeout = function(){
    console.log('请求超时了')
}
xhr.send(null)
```

### `overrideMimeType()`方法

`overrideMimeType()`方法能够重写服务器返回的`MIME`类型，从而让浏览器进行不一样的处理。假如服务器返回的数据类型是`text/xml`，由于种种原因浏览器解析不成功报错，这时就拿不到数据。为了拿到原始数据，我们可以把`MIME`类型改成`text/plain`，这样浏览器就不会去自动解析，从而我们就可以拿到原始文本了。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('get', '/server', true)
xhr.overrideMimeType('text/plain')
xhr.send(null)
```

### `responseType`属性

`responseType`属性是一个字符串，表示服务器返回数据的类型。使用`xhr.response`属性来接收。这个属性是可写的，可以在调用`open()`方法之后，`send()`方法之前设置这个属性的值，告诉服务器返回指定类型的数据。如果`responseType`设为空字符串，等同于默认值`text`。`responseType`属性可以设置的格式类型如下：

| responseType属性的值 | response属性的数据类型 | 说明 |
| :----------------- | :------------------- | :--- |
| "" | String字符串 | 默认值，等同于text(在不设置responseType时) |
| "text" | String字符串 | 服务器返回文本数据 |
| "document" | Document对象 | 希望返回XML格式数据时使用 |
| "json" | javaScript对象 | IE10/IE11不支持 |
| "blob" | Blob对象 | 服务器返回二进制对象 |
| "arrayBuffer" | ArrayBuffer对象 | 服务器返回二进制数组 |

当将`responseType`设置为一个特定的类型时，你需要确保服务器所返回的类型和你所设置的返回值类型是兼容的。那么如果两者类型不兼容，服务器返回的数据就会变成`null`，即使服务器返回了数据。给一个同步请求设置`responseType`会抛出一个`InvalidAccessError`的异常。

```javascript
// 获取一张图片代码示例
const xhr = new XMLHttpRequest()
xhr.open('get', '/server/image.png', true)
xhr.responseType = 'blob'
xhr.onload = function(e) {
  if (xhr.status >= 200 && xhr.status < 300) {
    const blob = this.response
    // ...
  }
}
xhr.send(null)
```

### `withCredentials`属性

`withCredentials`属性是一个布尔值，表示跨域请求时是否协带凭据信息（Cookie、HTTP认证及客户端SSL证明等），默认为`false`。如果需要跨域AJAX请求发送Cookie，需要`withCredentials`属性设为`true`。如果在同域下配置`xhr.withCredentials`，无论配置`true`还是`false`，效果都会相同。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('get', '/server', true)
xhr.withCredentials = true
xhr.send(null)
```

当配置了`withCredentials`为`true`时，必须在后端增加`response`头信息`Access-Control-Allow-Origin`，且必须指定域名，而不能指定为`*`。还要添加`Access-Control-Allow-Credentials`这个头信息为`true`。

```javascript
response.addHeader("Access-Control-Allow-Origin", "http://example.com")
response.addHeader("Access-Control-Allow-Credentials", "true")
```

### `abort()`方法和`onabort`事件

在接收到响应之前调用`abort()`方法用来取消异步请求。当一个请求被终止后，它的`readyState`属性将被置为0。在终止请求之后，还应该对`XMLHttpRequeat`对象进行解引用操作。当调用`abort()`后，会触发`onabort`事件。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('get', '/server', true)
xmlhttp.onabort = function () {
  console.log('请求被中止')
}
xmlhttp.send()
// 将会调用我们上面定义的 onabort 回调函数
xmlhttp.abort()
```

### `GET`请求

将查询字符串参数追加到`URL`的末尾，将信息发送给服务器。`GET`参数的编码方式是无法人为干涉的，这导致了不同浏览器有不同的编码方式，因此最稳妥的方案是人工预编码，人工解码，从而禁止浏览器编码的干涉。

```javascript
const xhr = new XMLHttpRequest()
// 使用encodeURIComponent()进行编码
const tempParam = encodeURIComponent('age')
const tempValue = encodeURIComponent('20')
xhr.open('get', '/server?tempParam=tempValue&money=100', true)
```

### `POST`请求

`POST`请求把数据作为请求的主体（请求的`body`）提交。下面是四种常见的`POST`请求提交数据方式。

#### `application/x-www-form-urlencoded`

浏览器的原生`<form>`表单，如果不设置`enctype`属性，那么最终就会以`application/x-www-form-urlencoded`方式提交数据。

#### `multipart/form-data`

表单上传文件时，必须让`<form>`表单的`enctype`等于`multipart/form-data`。

#### `application/json`

当发送AJAX请求时，把`application/json`作为请求头，用来告诉服务端消息主体是序列化后的JSON字符串。

#### `text/xml`

使用HTTP作为传输协议，XML作为编码方式的远程调用规范。

### 使用`XMLHttpRequest`模拟表单提交

将`Content-Type`头部信息设置为`application/x-www-form-urlencoded`。可以使用`XMLHttpRequest`对象来模仿表单提交。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('post', '/server', true)
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
const form = document.getElementById('myForm') 
// serialize()为表单序列化方法
xhr.send(serialize(form))
```

也可以使用`XMLHttpRequest level 2`的`FormData`来序列化表单数据。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('post', '/server', true)
const form = document.getElementById('myForm')
const formData = new FormData(form)
formData.append("id", "123456")
xhr.send(formData)
```

使用`FormData`不必明确地在`XMLHttpRequest`对象上设置请求头部。`XMLHttpRequest`对象能够识别传入的数据类型是`FormData`的实例，并配置适当的头部信息。

## `XMLHttpRequest`进度事件相关API

### `onloadstart`

在`XMLHttpRequest`对象开始传送数据时被触发，也就是调用`send()`方法（HTTP 请求发出）的时候。

```javascript
xhr.onloadstart = function () {
  console.log('开始发出请求...')
}
```

### `onprogress`

在接收响应期间持续不断地触发。`onprogress`事件处理程序会接收到一个`event`对象，它的`target`属性是`XMLHttpRequest`对象，并且`event`包含着三个额外的属性：`loaded`、`total`和`lengthComputable`。

- `event.loaded`：已传输的数据量（已经接收的字节数）。
- `event.total`：总共的数据量（根据`Content-Length`响应头部确定的预期字节数）。
- `event.lengthComputable`：进度信息是否可用的布尔值。

有了这些信息，就可以创建一个AJAX请求进度条了。

```javascript
const xhr = new XMLHttpRequest()
xhr.onprogress = function (event) {
    if (!event.lengthComputable) {
        return console.log('无法计算进展')
    }
    const percentComplete = event.loaded / event.total * 100
    console.log(`进度百分比：${percentComplete}%`)
}
xhr.open('post', '/server', true)
xhr.send(null)
```

### `onerror`

在请求发生错误时触发。只有发生了网络层级别的异常才会触发此事件，对于应用层级别的异常，比如响应返回的`statusCode`是`4xx`时，并不属于`NetWork Error`，所以不会触发`onerror`事件，而是会触发`onload`事件。

```javascript
xhr.onerror = function(e) {
 console.log('数据接收出错')
}
```

### `onabort`

调用`abort()`方法而终止请求时触发。

### `onload`

当请求成功，接收到完整的响应数据时触发。可以使用`onload`事件可以用来替代`readystatechange`事件。因为响应接收完毕后将触发`onload`事件，因此也就没有必要去检查`readyState`属性了。只要浏览器接收到服务器的响应，不管其状态如何，都会触发`load`事件。

```javascript
const xhr = new XMLHttpRequest()
xhr.onload = function onload() {
  console.log('数据接收完毕')
  if(xhr.status >= 200 && xhr.status < 300) {
    console.log(xhr.responseText)
  }
}
xhr.open('post', '/server', true)
xhr.send(formData)
```

为确保正常执行，必须在调用`open()`方法之前添加`onprogress`事件处理程序。

### `onloadend`

在请求结束（包括请求成功和请求失败），或者触发`error`、`abort`或`load`事件后触发。

```javascript
xhr.onloadend = function(e) {
  console.log('请求结束，状态未知')
}
```

每个请求都从触发`loadstart`事件开始，接下来是一或多个`progress`事件，然后触发`error`、`abort`或`load`事件中的一个，最后以触发`loadend`事件结束。

### `upload`

`XMLHttpRequest`不仅可以发送请求，还可以发送文件，就是AJAX文件上传。发送文件以后，通过`XMLHttpRequest.upload`属性可以得到一个`XMLHttpRequestUpload`对象。通过这个对象，可以得知上传的进展。实现方案就是监听这个对象的各种事件：`onloadstart`、`onprogress`、`onabort`、`onerror`、`onload`、`ontimeout`、`onloadend`。当文件上传时，对`upload`属性指定`progress`事件的监听函数，可获得上传的进度。

```javascript
const xhr = new XMLHttpRequest()
if (xhr.upload) {
    xhr.upload.onprogress = function progress(e) {
        if (e.total > 0) {
            e.percent = e.loaded / e.total * 100
        }
    }
}
```

### `XMLHttpRequest`对象接收响应相关API

在接收到响应后，第一步是检查`status`属性。以确定响应已经成功返回。将HTTP状态代码为`200`作为成功的标志。状态代码为`304`表示请求的资源并没有被修改，可以直接使用浏览器中缓存的版本，也被认为是有效的。

### 响应头相关

- `Content-Type`：服务器告诉客户端响应内容的类型和采用字符编码。比如：`Content-Type: text/html; charset=utf-8`。
- `Content-Length`：服务器告诉客户端响应实体的大小。比如：`Content-Length: 8368`。
- `Content-Encoding`：服务器告诉客户端返回的的压缩编码格式。比如：`Content-Encoding: gzip, deflate, br`。

### `status`属性

`status`属性返回一个整数，表示服务器回应的HTTP状态码。如果服务器没有返回状态码，那么这个属性默认是`200`。请求发出之前，该属性为0。该属性只读。

```javascript
if (xhr.readyState === 4) {
  if (xhr.status >= 200 && xhr.status < 300) {
    // 处理服务器的返回数据
  }
}
```

### `statusText`属性

`statusText`属性返回一个字符串，表示服务器发送的状态说明。比如`OK`和`Not Found`。在请求发送之前，该属性的值是空字符串。如果服务器没有返回状态提示，该属性的值默认为`OK`。该属性为只读属性。要通过检测`status`属性来决定下一步的操作，不要依赖`statusText`，因为`statusText`在跨浏览器使用时不太可靠。

### `response`属性

`response`属性表示服务器返回的数据。它可以是任何数据类型，比如字符串、对象、二进制对象等等，具体的类型由`XMLHttpRequest.responseType`属性决定。该属性只读。如果本次请求没有成功或者数据不完整，该属性等于`null`。

```javascript
const xhr = new XMLHttpRequest()
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    console.log(xhr.response)
  }
}
```

### `responseText`属性

`responseText`属性返回从服务器接收到的字符串，该属性为只读。

```javascript
if (xhr.readyState === 4) {
  if (xhr.status >= 200 && xhr.status < 300) {
    // 处理服务器的返回数据
    console.log(xhr.responseText)
  }
}
```


### `responseXML`属性

如果响应的内容类型是`text/xml`或`application/xml`，这个属性中将保存包含着响应数据的HTML或XML文档对象。该属性是只读属性。无论内容类型是什么，响应主体的内容都会保存到`responseText`属性中。而对于非XML数据而言，`responseXML`属性的值将为`null`。

### `responseURL`属性

`responseURL`属性是字符串，表示发送数据的服务器的网址。如果`URL`为空则返回空字符串。如果`URL`有锚点，则位于`URL#`后面的内容会被删除。如果服务器端发生跳转，这个属性返回最后实际返回数据的网址。

```javascript
const xhr = new XMLHttpRequest()
xhr.open('GET', 'http://example.com/test', true)
xhr.onload = function () {
  // 返回 http://example.com/test
  console.log(xhr.responseURL)
}
xhr.send(null)
```

### `getResponseHeader()`方法

`getResponseHeader()`方法返回HTTP头信息指定字段的值，如果还没有收到服务器的响应或者指定字段不存在，返回`null`。该方法的参数不区分大小写。

```javascript
const xhr = new XMLHttpRequest()
xhr.onload = function onload() {
   console.log(xhr.getResponseHeader('Content-Type'))
}
xhr.open('post', '/server', true)
xhr.send(null)
```

如果有多个字段同名，它们的值会被连接为一个字符串，每个字段之间使用逗号+空格分隔。

### `getAllResponseHeaders()`方法

`getAllResponseHeaders()`方法返回一个字符串，表示服务器发来的所有HTTP头信息。格式为字符串，每个头信息之间使用`CRLF`分隔（回车+换行），如果没有收到服务器回应，该属性为`null`。如果发生网络错误，该属性为空字符串。

```javascript
const xhr = new XMLHttpRequest()
xhr.onload = function onload() {
 const responseHeaders = 'getAllResponseHeaders' in xhr ? xhr.getResponseHeaders() : null
}
xhr.open('post', '/server', true)
xhr.send(null)
```

上面代码用于获取服务器返回的所有头信息。返回值可能是下面这样的字符串。

```
content-encoding: gzip\r\n
content-length: 2020\r\n
content-type: text/html; charset=utf-8\r\n
```

需要对这个字符串进行处理才能正确使用。

```javascript
const str = 'date: Fri, 08 Dec 2017 21:04:30 GMT\r\n' + 'content-encoding: gzip\r\n'

function trim(str) {
    return str.replace(/^\s*/, '').replace(/\s*$/, '')
}
function parseHeaders(headers) {
    if (!headers) {
      return {}
    }
    const parsed = {}
    let key, val, i
    const arr = headers.split(/[\r\n]+/)
    arr.forEach((line) => {
      i = line.indexOf(':')
      key = trim(line.substr(0, i)).toLowerCase()
      val = trim(line.substr(i + 1))
      if (key) {
        parsed[key] = parsed[key] ? parsed[key] + ', ' + val : val
      }
    })
    return parsed
}
//{date: "Fri, 08 Dec 2017 21:04:30 GMT", content-encoding: "gzip"}
console.log(parseHeaders(str))
```

## 参考

转载自[深入理解XMLHttpRequest](https://juejin.im/post/5e05c6c8e51d4558206031ca)