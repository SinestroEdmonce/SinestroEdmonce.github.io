---
layout: post
title: template page
categories: [cate1, cate2]
description: some word here
keywords: keyword1, keyword2
---

史上最全的AJAX之XMLHttpRequest方法和属性详解

安卓程序员小黄
0.101
2017.02.05 20:48:58
字数 2,089
阅读 1,913
转载请标明出处

本文出自HCY的博客

概述
AJAX是“Asynchronous Javascript And XML”的缩写，中文译作“异步JavaScript和XML”。使用AJAX可以通过HTTP协议与服务器交互数据，可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
传统的网页如果需要更新内容，必须重新加载整个网页页面。此外，它也是实现前端与后端解耦的重要技术手段。

为了实现AJAX技术，早期微软的IE5、IE6浏览器内嵌了XMLHTTP组件，其它浏览器比如Opera、Mozila的早期版本则内嵌了XMLHttpRequest组件。XMLHTTP与XMLHttpRequest有很多相同的属性和方法，因此XMLHTTP也被一起叫做XMLHttpRequest，简称XHR。后来XHR被W3C组织标准化。各浏览器也逐渐按照W3C制定的标准来实现XHR，到目前为止，仍然有部分的属性和方法不被部分浏览器支持。下面的图片截取于 Can I Use网站，它描述了目前各浏览器对XHR的兼容情况。因此，在使用XHR开发时需要注意兼容性，不过笔者认为，老版本浏览器的占有量会越来越少，新版本的浏览器可能会更加严格的按照W3C制定的标准来实现XHR，兼容性问题就会被慢慢淡化，Jquery框架在2.2的版本在实例化XHR时就不考虑IE5、IE6的兼容性问题了。

各浏览器对XMLHttpRequest的兼容情况各浏览器对XMLHttpRequest的兼容情况
XHR实例的创建
为了实现兼容IE5、IE6浏览器版本，所以可以用下面的代码创建XHR实例。

var xhr;
if (window.XMLHttpRequest)
{
    //  IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xhr=new XMLHttpRequest();
}
else
{
    // IE6, IE5 浏览器执行代码
    xhr=new ActiveXObject("Microsoft.XMLHTTP");
}
XHR请求
XHR可以通过HTTP协议与服务器交换数据,要发送HTTP请求要使用到下面这些方法和属性：

open方法
open方法用于创建HTTP请求，但是请求并未发送，其定义如下：

open(method, url [, async = true [, username = null [, password = null]]])
参数method定义请求的类型，如GET、POST方法等等，大小写不敏感
参数url定义请求的URL地址
参数async定义是否异步处理请求，true(异步)或者false(同步)，默认为true
参数username定义用户名，不常用，默认为null
参数password定义密码，不常用，默认为null
setRequestHeader方法
setRequestHeader方法用于向请求添加HTTP头，其定义如下:

setRequestHeader(name, value)
参数name定义HTTP请求头部的名称
参数value定义HTTP请求头部的值
注意:

setRequestHeader方法必须在open方法调用之后、send方法之前调用，否则会出现异常
setRequestHeader方法可以连续调用多次，如果已经存在同名的HTTP头时，最终结果是追加而不是覆盖
//例子1
client.setRequestHeader('X-Test', 'one');
client.setRequestHeader('X-Test', 'two');
//最后的结果为X-Test: one, two

//例子2
client.setRequestHeader('X-Test', 'one');
client.setRequestHeader('X-Test', 'one');
//最后的结果为X-Test: one, one
timeout属性
timeout属性用于设置HTTP请求的超时时间，单位毫秒。当发生超时时，会触发ontimeout事件。在IE中，超时属性只能在调用 open() 方法之后且在调用 send() 方法之前设置。

upload属性
upload用于在数据传输到服务器时收集一些传输信息，比如上传了多少字节，总共多少字节等，其里面还包含了一些事件回调。

send方法
send方法用于发送open方法创建的HTTP请求，其定义如下：

send([body = null])
参数body定义HTTP请求的数据，当HTTP请求的方法为GET、HEAD时，该参数被忽略。body的类型可以为ArrayBuffer(二进制缓冲数组)、Blob(二进制大对象)、Document(类似XML格式的数据)、DOMString（字符串）、FormData(表单)。
上面几种数据类型的介绍可参考:

ArrayBuffer、Blob、Document、DOMString、FormData类型的数据介绍

abort方法
当请求发送后如果想终止这个请求，则可以调用abort方法，其定义如下：

abort()
XHR事件回调
当XHR发送异步请求后，我们无法知道请求是否发生了异常、请求何时到达服务器、服务器何时返回响应数据。XHR为我们提供了很多的事件回调，用来通知我们请求及响应状态的改变。下面的接口定义语言描述了跟XHR相关的事件接口定义。

interface XMLHttpRequestEventTarget : EventTarget {
  // event handlers
  attribute EventHandler onloadstart;
  attribute EventHandler onprogress;
  attribute EventHandler onabort;
  attribute EventHandler onerror;
  attribute EventHandler onload;
  attribute EventHandler ontimeout;
  attribute EventHandler onloadend;
};

interface XMLHttpRequestUpload : XMLHttpRequestEventTarget {
};

interface XMLHttpRequest : XMLHttpRequestEventTarget {
  // event handler
  attribute EventHandler onreadystatechange;
  // states
  //XHR的状态定义
  const unsigned short UNSENT = 0;
  const unsigned short OPENED = 1;
  const unsigned short HEADERS_RECEIVED = 2;
  const unsigned short LOADING = 3;
  const unsigned short DONE = 4;
  //用于描述XHR的状态
  readonly attribute unsigned short readyState;
  // request
  [SameObject] readonly attribute XMLHttpRequestUpload upload;
};
readyState属性用于描述XHR的状态，它有下面五种状态：
值	状态	描述
0	UNSENT	最初始状态，还未调用open方法
1	OPENED	已经调用了open方法
2	HEADERS_RECEIVED	已经调用了send方法，响应的HTTP头部和状态可以获取
3	LOADING	正在下载数据，下载的数据还不完整
4	DONE	数据下载完成
onreadystatechange属性可以指定一个回调函数，当XHR的状态（即readyState）发生改变时就会调用该函数，可以在这个回调函数中判断请求是否成功。
   xhr.onreadystatechange = function() {
        if (xhr.readyState == 4) {
            //当XHR的状态为4时判断请求成功与否，然后处理响应的数据，虽然当XHR的状态为2或者3时可以获取到响应状态，但是此时的数据还未下载完全，不能处理响应数据
            if (xhr.status == 200) {
                //请求成功，处理响应数据
            } else {
                //请求失败
            }
        }
    }
upload属性代表上传数据的过程，它是XMLHttpRequestUpload的实例，XMLHttpRequestUpload继承XMLHttpRequestEventTarget接口，XMLHttpRequestEventTarget有7个回调方法，在数据上传的过程中会调用相应的方法。XHR也继承XMLHttpRequestEventTarget接口，因此它也拥有这些回调方法，但是它的回调方法大多都是从服务器下载响应数据的过程中触发的。
事件触发的时机
upload的回调方法会在数据上传的过程中触发，XHR的回调方法大多在响应数据下载的过程中触发，具体的触发时机见下表：

事件	触发时机
onreadystatechange	当readyState的值改变时触发，除了当它从非0变成0时
onloadstart	当调用send方法时会触发xhr.onloadstart,然后会触发xhr.upload.onloadstart，代表开始上传数据
onprogress	上传数据过程中会触发xhr.upload.onprogress，下载数据过程中会触发xhr.onprogress，onprogress每50ms会触发一次
onabort	调用abort方法后会触发
onerror	当发生网络异常的时候会触发，如果上传数据的过程还未结束，此时会先触发xhr.upload.onerror，然后再触发xhr.onerror；如果上传数据的过程已经结束，此时只会触发xhr.onerror
onload	上传数据成功，会触发xhr.upload.onload；下载数据成功会触发xhr.onload
ontimeout	当服务端响应的时间超过指定的timeout时间时，会触发此事件
onloadend	上传数据完成（成功或者失败）时会触发xhr.upload.onloadend；下载数据完成（成功或失败）会触发xhr.onloadend
事件触发的顺序
通过下面的代码验证，可以得出事件触发的顺序。

 var xhr = new XMLHttpRequest();
    xhr.timeout = 1;
    //xhr events
    xhr.onreadystatechange = function() {
        console.log("onreadystatechange(),readyState=" + xhr.readyState);
    }
    xhr.onloadstart = function(event) {
        console.log("onloadstart()");
    }
    xhr.onprogress = function(event) {
        console.log("onprogress()");
    }
    xhr.onabort = function(event) {
        console.log("onabort()");
    }
    xhr.onerror = function(event) {
        console.log("onerror()");
    }
    xhr.onload = function(event) {
        console.log("onload()");
    }
    xhr.ontimeout = function(event) {
        console.log("ontimeout()");
    }
    xhr.onloadend = function(event) {
            console.log("onloadend()");
        }
        //upload events
    xhr.upload.onloadstart = function(event) {
        console.log("upload.onloadstart()");
    }
    xhr.upload.onprogress = function(event) {
        console.log("upload.onprogress()");
    }
    xhr.upload.onabort = function(event) {
        console.log("upload.onabort()");
    }
    xhr.upload.onerror = function(event) {
        console.log("upload.onerror()");
    }
    xhr.upload.onload = function(event) {
        console.log("upload.onload()");
    }
    xhr.upload.ontimeout = function(event) {
        console.log("upload.ontimeout()");
    }
    xhr.upload.onloadend = function(event) {
        console.log("upload.onloadend()");
    }
    try {
        xhr.open("POST", "http://localhost:8080/Server/test", true);
        xhr.send("hello");
    } catch (e) {
        alert(e.toString());
    }
打印的顺序如下：

//调用了open方法
onreadystatechange(),readyState=1
//调用了send方法
onloadstart()
//上传数据过程中的事件回调
upload.onloadstart()//开始上传请求数据
upload.onprogress()//正在上传请求数据
upload.onload()//成功上传请求数据
upload.onloadend()//完成上传请求数据
//下载响应数据过程中的事件回调
onreadystatechange(),readyState=2//已经获取到响应头部和响应状态码
onreadystatechange(),readyState=3//正在下载响应数据，改变状态
onprogress()//正在下载响应数据
onreadystatechange(),readyState=4//响应数据下载完成，改变状态
onload()//成功下载响应数据
onloadend()//完成下载响应数据
事件回调方法的参数
XMLHttpRequestEventTarget里面的回调方法的参数类型为ProgressEvent，ProgressEvent的定义如下：

interface ProgressEvent : Event {
  readonly attribute boolean lengthComputable;//数据长度是否可计算的
  readonly attribute unsigned long long loaded;//已经下载或者上传了多少字节
  readonly attribute unsigned long long total;//需要下载或者上传的总字节数
};
所以在onprogress方法回调中，可以通过loaded和total这两个属性来实现上传或者下载的进度条功能。

事件回调方法的添加方式
一种方式是直接为它指定函数的引用，如下方式：
    xhr.onloadstart = function(event) {
        console.log("onloadstart()");
    }
从上面的定义中可以看出，事件接口都继承自EventTarget，EventTarget的定义如下，其中有addEventListener方法，第一个参数时事件名称，没有“on”前缀，第二个参数时回调方法，其它参数为可选。
interface EventTarget {
  void addEventListener(DOMString type, EventListener? callback, optional (AddEventListenerOptions or boolean) options);
  void removeEventListener(DOMString type, EventListener? callback, optional (EventListenerOptions or boolean) options);
  boolean dispatchEvent(Event event);
};
因此下面的代码实现的效果与方式一相同

    xhr.addEventListener("loadstart", function(event) {
        console.log("loadstart()");
    });
XHR响应
了解了XHR的请求、XHR的事件回调之后，就剩下处理XHR响应的工作了，比如解析数据等等，要处理响应，需要了解下面的方法和属性。

getResponseHeader方法
getResponseHeader方法可以获取HTTP响应头指定键值的数据，其定义如下：

getResponseHeader(ByteString name);
参数name为HTTP响应头部的键值
getAllResponseHeaders方法
getAllResponseHeaders方法可以获取所有的HTTP响应头的数据，其定义如下：

getAllResponseHeaders()
status和statusText属性
status属性表示HTTP响应状态码，即200、404等；statusText属性表示HTTP响应状态的描述文本，即OK、Not Found等。

responseType、response、responseText、responseXML属性
可以在发送请求之前设置responseType，用于指定返回的响应数据的类型，responseType的类型有以下几种：

类型	描述
empty string	空字符串，这是默认值
arraybuffer	二进制缓冲数组
blob	二进制大对象
document	文档类型
json	JSON类型
text	文本类型
处理响应数据时，需要根据responseType来判断返回的数据类型。当responseType为text或者empty string类型时可以使用responseText属性，为其它类型时调用responseText会发生异常；当responseType为document或者empty responseXML属性，为其它类型时调用responseXML会发生异常；当responseType不是empty string、text、document类型时，需要转换成具体的类型进行解析。

处理响应数据的时机
方式一：
    xhr.onreadystatechange = function() {
            if (xhr.readyState === 4) {
                if (xhr.status === 200) {
                    //处理数据
                } else {
                    //其它操作
                }
            }
        }
方式二：
    xhr.onload = function(event) {
        if (xhr.status === 200) {
            //处理数据
        } else {
            //其它操作
        }
    }
通过XHR回调事件的触发时机我们可以知道，onreadystatechange回调会触发多次，因此方式二更优。

参考资料
原汁原味的官方规范，W3C制定的XHR规范
发送数据的类型详解，DOMString、Document、FormData、Blob、File、ArrayBuffer数据类型介绍
辅助阅读的规范文档，MDN的Web api