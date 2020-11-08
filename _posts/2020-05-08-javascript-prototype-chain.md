---
layout: post
title: Javascript原型链
categories: [Javascript, OOP]
description: Javascript面向对象编程相关的一些基础知识点笔记，关于对象和原型之间的关系等概念的速记。
keywords: Javascript, JS, OOP
---

Javascript对每个创建的对象都会设置一个原型，指向它的原型对象。而原型对象自己也是一个对象，所以也有自己的原型。通过这种方式，一个对象就拥有了一条原型链。原型链是有尽头的，`Object.prototype`就是原型链的尽头，它所指向的就是`null`。

## 原型与原型链

当我们用`obj.xxx`访问一个对象的属性时，Javascript引擎先在当前对象上查找该属性，如果没有找到，就到其原型对象上找，如果还没有找到，就一直上溯到`Object.prototype`对象，最后，如果还没有找到，就只能返回`undefined`。例如，创建一个Array对象：

```javascript
var arr = [1, 2, 3];
```

其原型链是：

```
arr.__proto__ ----> Array.prototype ----> Object.prototype ----> null
```

`Array.prototype`定义了`indexOf()`、`shift()`等方法，因此你可以在所有的`Array`对象上直接调用这些方法。而当我们创建一个函数的时候

```javascript
function test() {
    return 0;
}
```

函数也是一个对象，它的原型链是：

```
test.__proto__ ----> Function.prototype ----> Object.prototype ----> null
```

由于`Function.prototype`定义了`apply()`等方法，因此，所有函数都可以调用`apply()`方法。

## 构造函数

除了直接用创建键值对的集合的方法：`{k : v}`来创建一个对象以外，Javascript还可以用一种构造函数的方法来创建对象。它的用法是，先定义一个构造函数：

```javascript
function Student(name) {
    this.name = name;
    this.hello = function () {
        alert('Hello, ' + this.name + '!');
    }
}
```

在JavaScript中，可以用关键字`new`来调用这个函数，并返回一个对象：

```javascript
var world = new Student('World');
world.hello(); // Hello, World!
```

如果不写new，这就是一个普通函数，它返回`undefined`，因为`Student()`这个函数没有返回值。但是，如果写了`new`，它就变成了一个构造函数，它绑定的`this`指向新创建的对象，并默认返回`this`。而新创建的`world`的原型链是：

```
world.__proto__ ----> Student.prototype ----> Object.prototype ----> null
```

也就是说，`world`的原型指向函数`Student`的原型。用`new`创建的对象还从原型上获得了一个`constructor`属性，它指向函数本身：

```javascript
world.constructor === Student.prototype.constructor; // true
Student.prototype.constructor === Student; // true
Object.getPrototypeOf(world) === Student.prototype; // true
world instanceof Student; // true
```

我们可以通过一张图来表示这种原型链结构：

```
Student                                             Object
- prototype --->|                                   - prototype
        |       |-----> Prototype Object1                |
        |<------|<----- - constructor          |-------->|---> Prototype Object2        |----> null
world           |       - __proto__ ---------->|               - __proto__ ------------>|
- __proto__ --->|
- name
- hello

```

箭头代表了原型链方向，需要注意的是：`Student.prototype`指向的对象就是`world`的原型对象，这个原型对象自己还有个属性`constructor`，指向`Student`函数本身。
现在我们可以认为`world`这些对象“继承”自`Student`。

不过还有一个小问题，注意观察：

```javascript
var bill = new Student("Bill");

world.name; // 'world'
bill.name; // 'Bill'

world.hello; // function: Student.hello()
bill.hello; // function: Student.hello()
world.hello === bill.hello; // false
```

`world`和`bill`各自的`name`不同。`world`和`bill`各自的`hello`是一个函数，但它们是两个不同的函数，虽然函数名称和代码都是相同的。

如果我们通过`new Student()`创建了很多对象，这些对象的`hello`函数每一个都要单独开辟堆空间。但是这些对象的`hello`函数实际上只需要共享同一个函数就可以，这样可以节省很多内存。要让创建的对象共享一个hello函数，根据对象的属性查找原则，我们只要把`hello`函数移动到`Student.prototype`就可以：

```javascript
function Student(name) {
    this.name = name;
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
};
```

## Hint

我们可以编写一个`createStudent()`函数，在内部封装所有的`new`操作。一个常用的编程模式像这样：

```javascript
function Student(props) {
    this.name = props.name || 'anonymous'; // 默认值为'anonymous'
    this.grade = props.grade || 1; // 默认值为1
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
};

function createStudent(props) {
    return new Student(props || {})
}
```

这个createStudent()函数有几个巨大的优点：一是不需要new来调用，二是参数非常灵活，可以不传，也可以这么传：

```javascript
var xiaoming = createStudent({
    name: '小明'
});

xiaoming.grade; // 1
```

如果创建的对象有很多属性，我们只需要传递需要的某些属性，剩下的属性可以用默认值。由于参数是一个`Object`，我们无需记忆参数的顺序。如果恰好从JSON拿到了一个对象，可以直接将JSON对象转换为所需要的对象。