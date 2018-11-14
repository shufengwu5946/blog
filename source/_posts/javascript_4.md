---
title: JavaScript学习笔记（四）——标准库
date: 
tags:
    - JavaScript
---

来自教程 https://wangdoc.com/javascript/basic/index.html

# 5 标准库

## 5.1 Object对象

### 5.1.1 Object()

如果参数为空（或者为undefined和null），Object()返回一个空对象。
```javascript
var obj = Object();
// 等同于
var obj = Object(undefined);
var obj = Object(null);

obj instanceof Object // true
```
如果参数是原始类型的值，Object方法将其转为对应的包装对象的实例。
```javascript
var obj = Object(1);
obj instanceof Object // true
obj instanceof Number // true

var obj = Object('foo');
obj instanceof Object // true
obj instanceof String // true

var obj = Object(true);
obj instanceof Object // true
obj instanceof Boolean // true
```
如果Object方法的参数是一个对象，它总是返回该对象，即不用转换。
```javascript
var arr = [];
var obj = Object(arr); // 返回原数组
obj === arr // true

var value = {};
var obj = Object(value) // 返回原对象
obj === value // true

var fn = function () {};
var obj = Object(fn); // 返回原函数
obj === fn // true
```
### 5.1.2 Object构造函数

* 通过var obj = new Object()的写法生成新对象，与字面量的写法var obj = {}是等价的。
* 但是Object(value)与new Object(value)两者的语义是不同的，Object(value)表示将value转成一个对象，new Object(value)则表示新生成一个对象，它的值是value。

### 5.1.3 Object静态方法
#### Object.keys()，Object.getOwnPropertyNames()

Object.keys方法和Object.getOwnPropertyNames方法都用来遍历对象的属性。方法的参数都是一个对象，返回一个数组。该数组的成员都是该对象自身的（而不是继承的）所有属性名。

Object.keys方法只返回可枚举的属性，Object.getOwnPropertyNames方法还返回不可枚举的属性名。

#### 其他方法

**（1）对象属性模型的相关方法**

* Object.getOwnPropertyDescriptor()：获取某个属性的描述对象。
* Object.defineProperty()：通过描述对象，定义某个属性。
* Object.defineProperties()：通过描述对象，定义多个属性。

**（2）控制对象状态的方法**

* Object.preventExtensions()：防止对象扩展。
* Object.isExtensible()：判断对象是否可扩展。
* Object.seal()：禁止对象配置。
* Object.isSealed()：判断一个对象是否可配置。
* Object.freeze()：冻结一个对象。
* Object.isFrozen()：判断一个对象是否被冻结。

**（3）原型链相关方法**

* Object.create()：该方法可以指定原型对象和属性，返回一个新的对象。
* Object.getPrototypeOf()：获取对象的Prototype对象。

### 5.1.4 Object实例方法

Object实例对象的方法，主要有以下六个。

* Object.prototype.valueOf()：返回当前对象对应的值。
* Object.prototype.toString()：返回当前对象对应的字符串形式。
* Object.prototype.toLocaleString()：返回当前对象对应的本地字符串形式。
* Object.prototype.hasOwnProperty()：判断某个属性是否为当前对象自身的属性，还是继承自原型对象的属性。
* Object.prototype.isPrototypeOf()：判断当前对象是否为另一个对象的原型。
* Object.prototype.propertyIsEnumerable()：判断某个属性是否可枚举。

#### toString() 的应用：判断数据类型

由于实例对象可能会自定义toString方法，覆盖掉Object.prototype.toString方法，所以为了得到类型字符串，最好直接使用Object.prototype.toString方法。通过函数的call方法，可以在任意值上调用这个方法，帮助我们判断这个值的类型。
```javascript
Object.prototype.toString.call(value)
```
上面代码表示对value这个值调用Object.prototype.toString方法。

不同数据类型的Object.prototype.toString方法返回值如下。

* 数值：返回[object Number]。
* 字符串：返回[object String]。
* 布尔值：返回[object Boolean]。
* undefined：返回[object Undefined]。
* null：返回[object Null]。
* 数组：返回[object Array]。
* arguments 对象：返回[object Arguments]。
* 函数：返回[object Function]。
* Error 对象：返回[object Error]。
* Date 对象：返回[object Date]。
* RegExp 对象：返回[object RegExp]。
* 其他对象：返回[object Object]。
这就是说，Object.prototype.toString可以看出一个值到底是什么类型。

## 5.2 属性描述对象

### 5.2.1 概述
下面是属性描述对象的一个例子。
```javascript
{
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false,
  get: undefined,
  set: undefined
}
```
属性描述对象提供6个元属性。

**（1）value**

value是该属性的属性值，默认为undefined。

**（2）writable**

writable是一个布尔值，表示属性值（value）是否可改变（即是否可写），默认为true。

**（3）enumerable**

enumerable是一个布尔值，表示该属性是否可遍历，默认为true。如果设为false，会使得某些操作（比如for...in循环、Object.keys()）跳过该属性。

**（4）configurable**

configurable是一个布尔值，表示可配置性，默认为true。如果设为false，将阻止某些操作改写该属性，比如无法删除该属性，也不得改变该属性的属性描述对象（value属性除外）。也就是说，configurable属性控制了属性描述对象的可写性。

**（5）get**

get是一个函数，表示该属性的取值函数（getter），默认为undefined。

**（6）set**

set是一个函数，表示该属性的存值函数（setter），默认为undefined。

### 5.2.2 Object.getOwnPropertyDescriptor()

只能用于对象自身的属性，不能用于继承的属性。

### 5.2.3 Object.getOwnPropertyNames()

方法返回一个数组，成员是参数对象自身的全部属性的属性名，不管该属性是否可遍历，不返回继承的属性。

这跟Object.keys的行为不同，Object.keys只返回对象自身的可遍历属性的全部属性名。

### 5.2.4 Object.defineProperty()，Object.defineProperties()

Object.defineProperty()方法允许通过属性描述对象，定义或修改一个属性，然后返回修改后的对象，它的用法如下。

```javascript
Object.defineProperty(object, propertyName, attributesObject)
```
Object.defineProperty方法接受三个参数，依次如下。

* object：属性所在的对象
* propertyName：字符串，表示属性名
* attributesObject：属性描述对象

如果一次性定义或修改多个属性，可以使用Object.defineProperties()方法。

> 注意，一旦定义了取值函数get（或存值函数set），就不能将writable属性设为true，或者同时定义value属性，否则会报错。

### 5.2.5 Object.prototype.propertyIsEnumerable()

实例对象的propertyIsEnumerable()方法返回一个布尔值，用来判断某个属性是否可遍历。

> 注意，这个方法只能用于判断对象自身的属性，对于继承的属性一律返回false。

### 5.2.6 元属性




## 5.3 Array对象
## 5.4 包装对象
## 5.5 Boolean对象
## 5.6 Number 对象
## 5.7 String 对象
## 5.8 Math 对象
## 5.9 Date 对象
## 5.10 RegExp 对象
## 5.11 JSON 对象

# 6 面向对象编程
# 7 异步操作
# 8 DOM
# 事件
# 浏览器模型
