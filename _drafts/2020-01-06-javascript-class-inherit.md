---
layout: post
title: Javascript对象继承
categories: [Javascript, OOP]
description: Javascript面向对象编程相关的一些基础知识点笔记，关于对象和原型之间的关系等概念的速记。
keywords: Javascript, JS, OOP
---

我们知道Javascript的对象模型是基于原型实现的，特点是简单，缺点是理解起来比传统的类－类实例模型要困难，最大的缺点是继承的实现需要编写大量代码，并且需要正确实现原型链。有没有更简单的写法？有（ES6标准中引入）！

## 关键字class

新的关键字`class`从ES6开始正式被引入到Javascript中。`class`的目的就是让定义类更简单。我们先回顾用函数实现Student的方法：

```javascript
function Student(name) {
    this.name = name;
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
}
```

如果用新的`class`关键字来编写`Student`，可以这样写：

```javascript
class Student {
    constructor(name) {
        this.name = name;
    }

    hello() {
        alert('Hello, ' + this.name + '!');
    }
}
```

比较一下就可以发现，`class`的定义包含了构造函数`constructor`和定义在原型对象上的函数`hello()`（**注意没有function关键字**）。而创建一个`Student`对象代码和前面章节完全一样：

```javascript
var xiaoming = new Student('Xiaoming');
xiaoming.hello();
```

class继承

用class定义对象的另一个巨大的好处是继承更方便了。想一想我们从Student派生一个PrimaryStudent需要编写的代码量。现在，原型继承的中间对象，原型对象的构造函数等等都不需要考虑了，直接通过extends来实现：

class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }

    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}

注意PrimaryStudent的定义也是class关键字实现的，而extends则表示原型链对象来自Student。子类的构造函数可能会与父类不太相同，例如，PrimaryStudent需要name和grade两个参数，并且需要通过super(name)来调用父类的构造函数，否则父类的name属性无法正常初始化。

PrimaryStudent已经自动获得了父类Student的hello方法，我们又在子类中定义了新的myGrade方法。

ES6引入的class和原有的JavaScript原型继承有什么区别呢？实际上它们没有任何区别，class的作用就是让JavaScript引擎去实现原来需要我们自己编写的原型链代码。简而言之，用class的好处就是极大地简化了原型链代码。

你一定会问，class这么好用，能不能现在就用上？

现在用还早了点，因为不是所有的主流浏览器都支持ES6的class。如果一定要现在就用上，就需要一个工具把class代码转换为传统的prototype代码，可以试试Babel这个工具。

