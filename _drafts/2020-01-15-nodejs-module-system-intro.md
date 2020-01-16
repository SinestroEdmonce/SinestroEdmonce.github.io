---
layout: post
title: Node.js模块系统浅探的一些笔记
categories: [Node.js, Notes]
description: 对Node.js模块系统再稍微详细的探究。对模块系统的用法和机制的简单速记。
keywords: Node.js, Modulization
---

这篇速记是对Node.js的模块系统浅层次的探究，不涉及到Node.js模块化和模块系统的底层实现以及模块通信机制。速记了一些个人认为比较常见的一些模块系统的语法和概念。

## 加载模块: ``require``

1. 语法:  

    ```javascript
    var xxx = require("xx"); //xxx是自定义的变量名称，xx是模块名称
    ```

2. 两个作用:  
    - 执行被加载模块中的代码
    - 得到被加载模块中的``exports``接口对象

## 导出模块: ``exports``

1. 导出多个成员（成员必须在对象中）

    ```javascript
    exports.a = 123;
    exports.b = "hello";
    exports.c = function () {
        console.log("ccc");
    }
    exports.d = {
        "foo": "bar"
    }
    ```

2. 导出单个成员

    ```javascript
    module.exports = "hello";

    // 二次赋值会覆盖前一次的赋值结果
    module.exports = function (x,y) {
        return x+y;
    }
    ```

3. 也可以通过第二种方法导出多个成员

    ```javascript
    module.exports = {
        add: function () {
            return x+y;
        }
        str: "hello"
    }
    ```

## 模块导出的原理简析

``exports``是``module.exports``的一个引用，即在底层实现上Node.js使用了类似下面的语句:  

```javascript
var exports = module.exports; // 即声明了exports变量，并且使其成为module.exports的一个引用
```

然而在最终模块返回值上，Node.js返回的实际是:  

```javascript
return module.exports;
```

所以如果``exports``重新被赋值了新对象，则不会对``module.exports``指向的实际内存空间产生影响；而如果``module.exports``被重新赋值，则原本绑定（挂载）到``exports``中的成员将不会被导出。