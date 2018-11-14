---
title: JavaScript学习笔记（三）——语法专题
date: 
tags:
    - JavaScript
---

来自教程 https://wangdoc.com/javascript/basic/index.html

# 4 语法专题
## 4.1 数据类型的转换
### 4.1.1 强制转换
#### Number()

**（1）原始类型值**

原始类型值的转换规则如下。

```javascript
// 数值：转换后还是原来的值
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0
```
Number函数只要有一个字符无法转成数值，整个字符串就会被转为NaN。

```javascript
parseInt('42 cats') // 42
Number('42 cats') // NaN
```
上面代码中，parseInt逐个解析字符，而Number函数整体转换字符串的类型。

另外，parseInt和Number函数都会自动过滤一个字符串前导和后缀的空格。

```javascript
parseInt('\t\v\r12.34\n') // 12
Number('\t\v\r12.34\n') // 12.34
```
**（2）对象**

简单的规则是，Number方法的参数是对象时，将返回NaN，除非是包含单个数值的数组。

Number背后的转换规则:

* 第一步，调用对象自身的valueOf方法。如果返回原始类型的值，则直接对该值使用Number函数，不再进行后续步骤。

* 第二步，如果valueOf方法返回的还是对象，则改为调用对象自身的toString方法。如果toString方法返回原始类型的值，则对该值使用Number函数，不再进行后续步骤。

* 第三步，如果toString方法返回的是对象，就报错。

#### String()

**（1）原始类型值**

* 数值：转为相应的字符串。
* 字符串：转换后还是原来的值。
* 布尔值：true转为字符串"true"，false转为字符串"false"。
* undefined：转为字符串"undefined"。
* null：转为字符串"null"。

**（2）对象**

String方法的参数如果是对象，返回一个类型字符串；如果是数组，返回该数组的字符串形式。

String方法背后的转换规则，与Number方法基本相同，只是互换了valueOf方法和toString方法的执行顺序。

先调用对象自身的toString方法。如果返回原始类型的值，则对该值使用String函数，不再进行以下步骤。

如果toString方法返回的是对象，再调用原对象的valueOf方法。如果valueOf方法返回原始类型的值，则对该值使用String函数，不再进行以下步骤。

#### Boolean()

转换规则相对简单：除了以下五个值的转换结果为false，其他的值全部为true。

* undefined
* null
* -0或+0
* NaN
''（空字符串）

### 4.1.1 自动转换

#### 自动转换为字符串
```javascript
'5' + 1 // '51'
'5' + true // "5true"
'5' + false // "5false"
'5' + {} // "5[object Object]"
'5' + [] // "5"
'5' + function (){} // "5function (){}"
'5' + undefined // "5undefined"
'5' + null // "5null"

var obj = {
  width: '100'
};
obj.width + 20 // "10020"
```

#### 自动转换为数值
```javascript
'5' - '2' // 3
'5' * '2' // 10
true - 1  // 0
false - 1 // -1
'1' - 1   // 0
'5' * []    // 0
false / '5' // 0
'abc' - 1   // NaN
null + 1 // 1
undefined + 1 // NaN

+'abc' // NaN
-'abc' // NaN
+true // 1
-false // 0
```

## 4.2 错误处理机制

(未完待续)

## 4.3 编程风格

（未完待续）
## 4.4 console对象与控制台

（未完待续）

