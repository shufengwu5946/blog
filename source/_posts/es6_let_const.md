---
title: ES6——let和const
date: 
tags:
    - ES6
    - let
    - const
---

# 1、let 命令

## 作用域

for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
```javascript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```
上面代码正确运行，输出了 3 次abc。这表明函数内部的变量i与循环变量i不在同一个作用域，有各自单独的作用域。

## 不存在变量提升

## 暂时性死区

在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为暂时性死区。
```javascript
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```
### typeof

“暂时性死区”也意味着typeof不再是一个百分之百安全的操作。

```javascript
typeof x; // ReferenceError
let x;
```

上面代码中，**变量x使用let命令声明，所以在声明之前，都属于x的“死区”，只要用到该变量就会报错**。因此，typeof运行时就会抛出一个ReferenceError。

作为比较，如果一个变量根本没有被声明，使用typeof反而不会报错。

```javascript
typeof undeclared_variable // "undefined"
```
上面代码中，undeclared_variable是一个不存在的变量名，结果返回“undefined”。

### 易错点

(1)
```javascript
function bar(x = y, y = 2) {
  return [x, y];
}

bar(); // 报错
```
(2)
```javascript
// 不报错
var x = x;
// 报错
let x = x;
// ReferenceError: x is not defined
```
## 不允许重复声明
let不允许在相同作用域内，重复声明同一个变量。

# 2、块级作用域

## 为什么需要块级作用域？
* 第一种场景，内层变量可能会覆盖外层变量。
* 第二种场景，用来计数的循环变量泄露为全局变量。

## ES6 的块级作用域

ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。

但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在块级作用域之中声明函数，因此上面两种情况实际都能运行，不会报错。

ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。

## 块级作用域与函数声明

```javascript
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();
}());
```
上面代码在 ES5 中运行，会得到“I am inside!”。

ES6里有自己的行为方式：

* 允许在块级作用域内声明函数。
* 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
* 同时，函数声明还会提升到所在的块级作用域的头部。

> 注意，上面三条规则只对 ES6 的浏览器实现有效，其他环境的实现不用遵守，还是将块级作用域的函数声明当作let处理。

根据这三条规则，在浏览器的 ES6 环境中，块级作用域内声明的函数，行为类似于var声明的变量。

上面的代码在符合 ES6 的浏览器中，都会报错，因为实际运行的是下面的代码。

```javascript
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

考虑到环境导致的行为差异太大，应该**避免在块级作用域内声明函数**。如果确实需要，也应该写成**函数表达式**，而不是函数声明语句。

# 3、const 命令
## 基本用法

const一旦声明变量，就必须立即初始化，不能留到以后赋值。

const的作用域与let命令相同：只在声明所在的块级作用域内有效。

const命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。

const声明的常量，也与let一样不可重复声明。

## 本质

const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。

对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。

但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

如果真的想将**对象冻结**，应该使用**Object.freeze方法**。
```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```
上面代码中，常量foo指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。

递归实现将对象彻底冻结的函数。
```javascript
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```
## ES6 声明变量的六种方法

ES5：var命令、function命令。

ES6：let命令、const命令、import命令、class命令。

# 4、顶层对象的属性

顶层对象，在浏览器环境指的是window对象，在 Node 指的是global对象。

ES5 之中，顶层对象的属性与全局变量是等价的。

ES6 为了改变这一点，一方面规定，为了保持兼容性，var命令和function命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。
```javascript
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```
上面代码中，全局变量a由var命令声明，所以它是顶层对象的属性；全局变量b由let命令声明，所以它不是顶层对象的属性，返回undefined。

