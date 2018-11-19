---
title: JavaScript学习笔记（五）——面向对象编程
date: 
tags:
    - JavaScript
---

来自教程 https://wangdoc.com/javascript/basic/index.html

# 6 面向对象编程

## 实例对象与 new 命令

### new 命令

#### 直接调用构造函数存在问题

如果忘了使用new命令，直接调用构造函数，构造函数就变成了普通函数，并不会生成实例对象，这时，构造函数中的this这时代表全局对象

```javascript
var Vehicle = function (){
  this.price = 1000;
};

var v = Vehicle();
v // undefined
price // 1000
```

##### 解决方案：

**方案一**：为了保证构造函数必须与new命令一起使用，构造函数内部使用严格模式。这样的话，一旦忘了使用new命令，直接调用构造函数就会报错。

```javascript
function Fubar(foo, bar){
  'use strict';
  this._foo = foo;
  this._bar = bar;
}

Fubar()
// TypeError: Cannot set property '_foo' of undefined
```
> 由于严格模式中，函数内部的this不能指向全局对象，默认等于undefined，导致不加new调用会报错（JavaScript 不允许对undefined添加属性）。

**方案二**：构造函数内部判断是否使用new命令，如果发现没有使用，则直接返回一个实例对象。

```javascript
function Fubar(foo, bar) {
  if (!(this instanceof Fubar)) {
    return new Fubar(foo, bar);
  }

  this._foo = foo;
  this._bar = bar;
}

Fubar(1, 2)._foo // 1
(new Fubar(1, 2))._foo // 1
```

### new 命令的原理

使用new命令时，它后面的函数依次执行下面的步骤。

* 创建一个空对象，作为将要返回的对象实例。
* 将这个空对象的原型，指向构造函数的prototype属性。
* 将这个空对象赋值给函数内部的this关键字。
* 开始执行构造函数内部的代码。

如果构造函数内部有return语句，而且return后面跟着一个对象，new命令会返回return语句指定的对象；否则，就会不管return语句，返回this对象。

如果对普通函数（内部没有this关键字的函数）使用new命令，则会返回一个空对象。


* 手写new命令

```javascript
function _new( constructor,  params) {
  // 将 arguments 对象转为数组
  var args = [].slice.call(arguments);
  // 取出构造函数
  var constructor = args.shift();
  // 创建一个空对象，继承构造函数的 prototype 属性
  var context = Object.create(constructor.prototype);
  // 执行构造函数
  var result = constructor.apply(context, args);
  // 如果返回结果是对象，就直接返回，否则返回 context 对象
  return (typeof result === 'object' && result != null) ? result : context;
}

// 实例
var actor = _new(Person, '张三', 28);
```