---
layout: post
title: HTML5中form表单响应提交但是不发生跳转的方法
categories: [Html5, Javascript]
description: 在Web前端设计中经常需要在表单提交前进行一些对表单提交内容的操作，比如检查以确保合法性，但是在HTML5中form表单响应提交后会默认发生跳转，无论提交的内容是否通过了合法性检查或者完成了某些处理。通过结合Javascript操作DOM可以实现响应提交并取消跳转。
keywords: HTML5, Javascript, form
---

在Web前端设计中经常需要对表单提交的内容进行检查以确保合法性，或者是对表单将要提交的数据进行一系列处理，或者是单纯想要取消跳转。但是在HTML5中form表单响应提交后会默认发生跳转，无论提交的内容是否通过了合法性检查或者完成了某些处理。通过结合Javascript操作DOM可以实现响应提交并取消跳转。  

## 需求描述

将表单数据提交到后端服务器，但是不进行跳转，即保持当前页面不变，同时在提交到后端服务器之前做一些数据处理操作。

## 解决方法

1. 利用jQuery的`ajaxSubmit`函数和`form`的`onsubmit`函数来实现：

    ```html
    <form id="form-1" action="/comment" method="post" οnsubmit="return comment();"> 
        <input type="submit" value="comment"/> 
    </form>
    ```

    `form`需要拥有`id`属性，用于在jquery中调用。并且`form`需要增加一个`onsubmit`函数用于`submit`前进行数据处理和提交后端。`comment()`函数如下：

    ```javascript
    function comment() { 
        // jQuery 表单提交 
        $("#form-1").ajaxSubmit(function (message) { 
            // TODO: something to handle after successful response
            // ...
        });
        return false; // 必须返回false，否则表单会自己再做一次提交操作，并且页面跳转
    }
    ```

2. 不使用jQuery，通过操作DOM来实现（实际作用机制和上述的方法相同），`form`部分的HTML代码不变。操作DOM的代码如下：

    ```javascript
    function comment() {
        let form = document.getElementById("form-1");
        // TODO：deal with the data
        // ...
        let xhr = new XMLHttpRequest();
        // TODO: async submit

        return false; // 必须返回false，否则表单会自己再做一次提交操作，并且页面跳转
    }
    ```
