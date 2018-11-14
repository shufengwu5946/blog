---
title: JavaScript学习笔记（二）——运算符
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
```javascript
7+null //7
7+undefined //NaN
7+NaN //NaN
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

### 3.1.4 指数运算符

指数运算符是右结合，而不是左结合。即多个指数运算符连用时，先进行最右边的计算。
```javascript
// 相当于 2 ** (3 ** 2)
2 ** 3 ** 2
// 512
```

## 3.2 比较运算符

JavaScript 一共提供了8个比较运算符。

* > 大于运算符
* < 小于运算符
* <= 小于或等于运算符
* >= 大于或等于运算符
* == 相等运算符
* === 严格相等运算符
* != 不相等运算符
* !== 严格不相等运算符

### 3.2.1 非相等运算符：非字符串的比较

#### （1）原始类型值

* 如果两个运算子都是原始类型的值，则是先转成数值再比较。
  ```javascript
  5 > '4' // true
  // 等同于 5 > Number('4')
  // 即 5 > 4

  true > false // true
  // 等同于 Number(true) > Number(false)
  // 即 1 > 0

  2 > true // true
  // 等同于 2 > Number(true)
  // 即 2 > 1
  ```
* 任何值（包括NaN本身）与NaN比较，返回的都是false。

#### （2）对象

如果运算子是对象，会转为原始类型的值，再进行比较。

对象转换成原始类型的值，算法是先调用valueOf方法；如果返回的还是对象，再接着调用toString方法
```javascript
var x = [2];
x > '11' // true
// 等同于 [2].valueOf().toString() > '11'
// 即 '2' > '11'

x.valueOf = function () { return '1' };
x > '11' // false
// 等同于 [2].valueOf() > '11'
// 即 '1' > '11'

[2] > [1] // true
// 等同于 [2].valueOf().toString() > [1].valueOf().toString()
// 即 '2' > '1'

[2] > [11] // true
// 等同于 [2].valueOf().toString() > [11].valueOf().toString()
// 即 '2' > '11'

{ x: 2 } >= { x: 1 } // true
// 等同于 { x: 2 }.valueOf().toString() >= { x: 1 }.valueOf().toString()
// 即 '[object Object]' >= '[object Object]'
```
### 3.2.2 严格相等运算符

JavaScript提供两种相等运算符：==和===。

**区别：**

* 相等运算符（==）比较两个值是否相等，严格相等运算符（===）比较它们是否为“同一个值”。

* 如果两个值不是同一类型，严格相等运算符（===）直接返回false，而相等运算符（==）会将它们转换成同一个类型，再用严格相等运算符进行比较。

**使用：**

* 不同类型的值：如果两个值的类型不同，直接返回false

* 同一类的原始类型值：同一类型的原始类型的值（数值、字符串、布尔值）比较时，值相同就返回true，值不同就返回false。

* 复合类型值：两个复合类型（对象、数组、函数）的数据比较时，是比较它们是否指向同一个地址。

> NaN与任何值都不相等（包括自身）。另外，正0等于负0

* undefined 和 null：
undefined和null与自身严格相等。
```javascript
undefined === undefined // true
null === null // true
```
### 3.2.3 相等运算符
* 相等运算符用来比较相同类型的数据时，与严格相等运算符完全一样。
* 比较不同类型的数据时，相等运算符会先将数据进行类型转换，然后再用严格相等运算符比较。

#### （1）原始类型值

原始类型的值会转换成数值再进行比较。
```javascript
1 == true // true
// 等同于 1 === Number(true)

0 == false // true
// 等同于 0 === Number(false)

2 == true // false
// 等同于 2 === Number(true)

2 == false // false
// 等同于 2 === Number(false)

'true' == true // false
// 等同于 Number('true') === Number(true)
// 等同于 NaN === 1

'' == 0 // true
// 等同于 Number('') === 0
// 等同于 0 === 0

'' == false  // true
// 等同于 Number('') === Number(false)
// 等同于 0 === 0

'1' == true  // true
// 等同于 Number('1') === Number(true)
// 等同于 1 === 1

'\n  123  \t' == 123 // true
// 因为字符串转为数字时，省略前置和后置的空格
```

#### （2）对象与原始类型值比较

对象（这里指广义的对象，包括数组和函数）与原始类型的值比较时，对象转换成原始类型的值，再进行比较。
```javascript
// 对象与数值比较时，对象转为数值
[1] == 1 // true
// 等同于 Number([1]) == 1

// 对象与字符串比较时，对象转为字符串
[1] == '1' // true
// 等同于 String([1]) == '1'
[1, 2] == '1,2' // true
// 等同于 String([1, 2]) == '1,2'

// 对象与布尔值比较时，两边都转为数值
[1] == true // true
// 等同于 Number([1]) == Number(true)
[2] == true // false
// 等同于 Number([2]) == Number(true)
```

#### （3）undefined 和 null

undefined和null与其他类型的值比较时，结果都为false，它们互相比较时结果为true。
```javascript
false == null // false
false == undefined // false

0 == null // false
0 == undefined // false

undefined == null // true
```
#### （4）相等运算符的缺点

相等运算符隐藏的类型转换，会带来一些违反直觉的结果。
```javascript
0 == ''             // true
0 == '0'            // true

2 == true           // false
2 == false          // false

false == 'false'    // false
false == '0'        // true

false == undefined  // false
false == null       // false
null == undefined   // true

' \t\r\n ' == 0     // true
```

## 3.3 布尔运算符
四个运算符。

* 取反运算符：!
* 且运算符：&&
* 或运算符：||
* 三元运算符：?:

### 3.3.1 取反运算符（!）
以下六个值取反后为true，其他值都为false。

* undefined
* null
* false
* 0
* NaN
* 空字符串（''）

### 3.3.2且运算符（&&）

* 它的运算规则是：如果第一个运算子的布尔值为true，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为false，则直接返回第一个运算子的值，且不再对第二个运算子求值。

* 且运算符可以多个连用，这时返回第一个布尔值为false的表达式的值。如果所有表达式的布尔值都为true，则返回最后一个表达式的值。

### 3.3.3 或运算符（||）

* 它的运算规则是：如果第一个运算子的布尔值为true，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为false，则返回第二个运算子的值。

* 或运算符可以多个连用，这时返回第一个布尔值为true的表达式的值。如果所有表达式都为false，则返回最后一个表达式的值。

## 3.4 二进制运算符

二进制位运算符用于直接对二进制位进行计算，一共有7个。

* 二进制或运算符（or）：符号为|，表示若两个二进制位都为0，则结果为0，否则为1。
* 二进制与运算符（and）：符号为&，表示若两个二进制位都为1，则结果为1，否则为0。
* 二进制否运算符（not）：符号为~，表示对一个二进制位取反。
* 异或运算符（xor）：符号为^，表示若两个二进制位不相同，则结果为1，否则为0。
* 左移运算符（left shift）：符号为<<，详见下文解释。
* 右移运算符（right shift）：符号为>>，详见下文解释。
* 带符号位的右移运算符（zero filled right shift）：符号为>>>，详见下文解释。

（未完待续）

## 3.5 其他运算符，运算顺序
### 3.5.1 void 运算符

void运算符的作用是执行一个表达式，然后不返回任何值，或者说返回undefined。

请看下面的代码。
```html
<script>
function f() {
  console.log('Hello World');
}
</script>
<a href="http://example.com" onclick="f(); return false;">点击</a>
```
上面代码中，点击链接后，会先执行onclick的代码，由于onclick返回false，所以浏览器不会跳转到 example.com。

void运算符可以取代上面的写法。
```html
<a href="javascript: void(f())">文字</a>
```
下面是一个更实际的例子，用户点击链接提交表单，但是不产生页面跳转。
```html
<a href="javascript: void(document.form.submit())">
  提交
</a>
```

### 3.5.2 逗号运算符

逗号运算符用于对两个表达式求值，并返回后一个表达式的值。

逗号运算符的一个用途是，在返回一个值之前，进行一些辅助操作。

### 3.5.3 运算顺序

#### 左结合与右结合

最主要的是赋值运算符（=）和三元条件运算符（?:）。

```javascript
w = x = y = z;
q = a ? b : c ? d : e ? f : g;
```
上面代码的运算结果，相当于下面的样子。

```javascript
w = (x = (y = z));
q = a ? b : (c ? d : (e ? f : g));
```
上面的两行代码，各有三个等号运算符和三个三元运算符，都是先计算最右边的那个运算符。

指数运算符（**）也是右结合的。
```javascript
// 相当于 2 ** (3 ** 2)
2 ** 3 ** 2
// 512
```

