---
title: JavaScript学习笔记（二）
date: 
tags:
    - JavaScript
---

来自教程 https://wangdoc.com/javascript/basic/index.html

# 3 运算符

JavaScript 共提供10个算术运算符，用来完成基本的算术运算。

* 加法运算符：x + y
* 减法运算符： x - y
* 乘法运算符： x * y
* 除法运算符：x / y
* 指数运算符：x ** y
* 余数运算符：x % y
* 自增运算符：++x 或者 x++
* 自减运算符：--x 或者 x--
* 数值运算符： +x
* 负数值运算符：-x

## 3.1 加法运算符

### 3.1.1 基本规则
```javascript
true + true // 2
1 + true // 2
'3' + 4 + 5 // "345"
3 + 4 + '5' // "75"
```

除了加法运算符，其他算术运算符（比如减法、除法和乘法）都不会发生重载。它们的规则是：所有运算子一律转为数值，再进行相应的数学运算。
```javascript
1 - '2' // -1
1 * '2' // 2
1 / '2' // 0.5
```
### 3.1.2 对象的相加
如果运算子是对象，必须先转成原始类型的值，然后再相加。
```javascript
var obj = { p: 1 };
obj + 2 // "[object Object]2"
```
上面代码中，对象obj转成原始类型的值是[object Object]，再加2就得到了上面的结果。

对象转成原始类型的值，规则如下。
```javascript
obj.valueOf().toString() 
```

知道了这个规则以后，就可以自己定义valueOf方法或toString方法，得到想要的结果。
```javascript
var obj = {
  valueOf: function () {
    return 1;
  }
};

obj + 2 // 3
```
```javascript
var obj = {
  toString: function () {
    return 'hello';
  }
};

obj + 2 // "hello2"
```
> 特例，如果运算子是一个Date对象的实例，那么会优先执行toString方法。
```javascript
var obj = new Date();
obj.valueOf = function () { return 1 };
obj.toString = function () { return 'hello' };

obj + 2 // "hello2"
```

### 3.1.3 数值运算符，负数值运算符

数值运算符的作用在于可以将任何值转为数值（与Number函数的作用相同）。
```javascript
+true // 1
+[] // 0
+{} // NaN
```

负数值运算符（-），也同样具有将一个值转为数值的功能，只不过得到的值正负相反。连用两个负数值运算符，等同于数值运算符。

```javascript
var x = 1;
-x // -1
-(-x) // 1
```

> 数值运算符号和负数值运算符，都会返回一个新的值，而不会改变原始变量的值。

# 标准库
# 面向对象编程
# 异步操作
# DOM
# 事件
# 浏览器模型
