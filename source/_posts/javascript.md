---
title: JavaScript 学习笔记
date: 2018-11-3 14:52:11
tags:
    - JavaScript
---

# 1 基本语法

## 1.1 语句

(略)

## 1.2 变量

(略)

### 1.2.1 变量提升

(略)

## 1.3 标识符

标识符（identifier）指的是用来识别各种值的合法名称。最常见的标识符就是变量名，以及后面要提到的函数名。JavaScript 语言的标识符**对大小写敏感**。

标识符有一套命名规则，不符合规则的就是非法标识符。JavaScript 引擎遇到非法标识符，就会报错。

简单说，标识符命名规则如下。

* 第一个字符，可以是任意 Unicode 字母（包括英文字母和其他语言的字母），以及美元符号（$）和下划线（_）。

* 第二个字符及后面的字符，除了 Unicode 字母、美元符号和下划线，还可以用数字0-9。

## 1.4 注释 

(略)

## 1.5 区块

(略)

## 1.6 条件语句

* if...else
* switch
* 三元运算符

(略)

## 1.7 循环语句

(略)

### 1.7.1 for

(略)

### 1.7.2 while

(略)

### 1.7.3 do...while

(略)

### 1.7.4 break和continue

(略)

### 1.7.5 标签(label)

(略)

# 2 数据类型

## 2.1 类型

JavaScript 的数据类型，共有六种。（ES6 又新增了第七种 Symbol 类型的值，本教程不涉及。）

* 数值（number）：整数和小数（比如1和3.14）
* 字符串（string）：文本（比如Hello World）。
* 布尔值（boolean）：表示真伪的两个特殊值，即true（真）和false（假）
* undefined：表示“未定义”或不存在，即由于目前没有定义，所以此处暂时没有任何值
* null：表示空值，即此处的值为空。
* 对象（object）：各种值组成的集合。
    * 狭义的对象（object）
    * 数组（array）
    * 函数（function）

原始类型（primitive type）：数值、字符串、布尔值。

合成类型（complex type）：对象。

特殊值：null、undefined。

## 2.2 typeof 运算符

typeof运算符可以返回一个值的数据类型。

```javascript
typeof 123 // "number"   数值返回number
typeof '123' // "string"  字符串返回string
typeof false // "boolean"  布尔值返回boolean

function f() {}
typeof f // "function"  函数返回function

typeof undefined // "undefined"  undefined返回undefined

//对象返回object
typeof window // "object"
typeof {} // "object"
typeof [] // "object"

typeof null // "object"  null返回object
```

    利用这一点，typeof可以用来检查一个没有声明的变量，而不报错。
    ```javascript
    v
    // ReferenceError: v is not defined

    typeof v
    // "undefined"
    ```

    实际编程中，这个特点通常用在判断语句。
    ```javascript
    // 错误的写法
    if (v) {
    // ...
    }
    // ReferenceError: v is not defined

    // 正确的写法
    if (typeof v === "undefined") {
    // ...
    }
    ```

## 2.3 null 和 undefined

1. 在if语句中，它们都会被自动转为false，相等运算符（==）甚至直接报告两者相等。

```javascript
if (!undefined) {
  console.log('undefined is false');
}
// undefined is false

if (!null) {
  console.log('null is false');
}
// null is false

undefined == null
// true
```

2. null是一个表示“空”的对象，转为数值时为0；undefined是一个表示"此处无定义"的原始值，转为数值时为NaN。

```javascript
Number(null) // 0
5 + null // 5
Number(undefined) // NaN
5 + undefined // NaN
```

3. null表示空值，即该处的值现在为空。调用函数时，某个参数未设置任何值，这时就可以传入null，表示该参数为空。比如，某个函数接受引擎抛出的错误作为参数，如果运行过程中未出错，那么这个参数就会传入null，表示未发生错误。

    undefined表示“未定义”，下面是返回undefined的典型场景。

```javascript
// 变量声明了，但没有赋值
var i;
i // undefined

// 调用函数时，应该提供的参数没有提供，该参数等于 undefined
function f(x) {
  return x;
}
f() // undefined

// 对象没有赋值的属性
var  o = new Object();
o.p // undefined

// 函数没有返回值时，默认返回 undefined
function f() {}
f() // undefined
```

## 2.4 布尔值

下列运算符会返回布尔值：

* 前置逻辑运算符： ! (Not)
* 相等运算符：===，!==，==，!=
* 比较运算符：>，>=，<，<=

如果 JavaScript 预期某个位置应该是布尔值，会将该位置上现有的值自动转为布尔值。转换规则是除了下面六个值被转为false，其他值都视为true。

```
undefined
null
false
0
NaN
""或''（空字符串）
```
> 注意，空数组（[]）和空对象（{}）对应的布尔值，都是true。

## 2.5 数值

（后续补充）

## 2.6 字符串

### 2.6.1 用法

单引号字符串的内部，可以使用双引号。双引号字符串的内部，可以使用单引号。

```javascript
'key = "value"'
"It's a long journey"
```

HTML语言的属性值使用双引号，JavaScript语言的字符串只使用单引号。

如果长字符串必须分成多行，可以在每一行的尾部使用反斜杠。

```javascript
var longString = 'Long \
long \
long \
string';

longString
// "Long long long string"
```

如果想输出多行字符串，有一种利用多行注释的变通方法。
```javascript
(function () { /*
line 1
line 2
line 3
*/}).toString().split('\n').slice(1, -1).join('\n')
// "line 1
// line 2
// line 3"
```

### 2.6.2 转义

(后续补充)

### 2.6.3 字符串与数组

* 字符串可以被视为字符数组。
```javascript
var s = 'hello';
s[0] // "h"
s[1] // "e"
s[4] // "o"

// 直接对字符串使用方括号运算符
'hello'[1] // "e"
```

* 如果方括号中的数字超过字符串的长度，或者方括号中根本不是数字，则返回undefined。

```javascript
'abc'[3] // undefined
'abc'[-1] // undefined
'abc'['x'] // undefined
```
* 无法改变字符串之中的单个字符。
```javascript
var s = 'hello';

delete s[0];
s // "hello"

s[1] = 'a';
s // "hello"

s[5] = '!';
s // "hello"
```
### 2.6.4 字符集
（后续补充）
### 2.6.5 Base 64转码
（后续补充）

## 2.7 对象

### 2.7.1 含义 

#### 键名

* 对象的所有键名都是字符串（ES6 又引入了 Symbol 值也可以作为键名），所以加不加引号都可以。
* 如果键名是数值，会被自动转为字符串。
* 如果键名不符合标识名的条件（比如第一个字符为数字，或者含有空格或运算符），且也不是数字，则必须加上引号，否则会报错。

#### 对象的引用
o1和o2指向同一个对象，如果取消某一个变量对于原对象的引用，不会影响到另一个变量。
```javascript
var o1 = {};
var o2 = o1;

o1 = 1;
o2 // {}
```
上面代码中，o1和o2指向同一个对象，然后o1的值变为1，这时不会对o2产生影响，o2还是指向原来的那个对象。

#### 表达式还是语句？
```javascript
{ foo: 123 }
```

JavaScript 引擎读到上面这行代码，会发现可能有两种含义。

* 第一种可能是，这是一个表达式，表示一个包含foo属性的对象；

* 第二种可能是，这是一个语句，表示一个代码区块，里面有一个标签foo，指向表达式123。

为了避免这种歧义，V8 引擎规定：

* 如果行首是大括号，一律解释为对象。不过，为了避免歧义，最好在大括号前加上圆括号:

    ```javascript
    ({ foo: 123})
    ```
这种差异在**eval语句**（作用是对字符串求值）中反映得最明显。
```javascript
eval('{foo: 123}') // 123
eval('({foo: 123})') // {foo: 123}
```
上面代码中，如果没有圆括号，eval将其理解为一个代码块；加上圆括号以后，就理解成一个对象。

### 2.7.2 属性的操作
#### 属性的读取

```javascript
var foo = 'bar';

var obj = {
  p: 'Hello World',
  bar: 2,
  'hello world':3,
  6:'num_key',
  0.7: 'Hello World',
  123: 'hello 123'
};

obj.p // "Hello World"  使用点运算符
obj['p'] // "Hello World"  方括号运算符,键名必须放在引号里面，否则会被当作变量处理。
obj[foo] // 2  键名为变量
// 方括号运算符内部还可以使用表达式
obj['hello' + ' world'] //3
obj[3 + 3] //num_key
// 数字键可以不加引号，因为会自动转成字符串。
obj['0.7'] // "Hello World"
obj[0.7] // "Hello World"
obj.123 // 报错  数值键名不能使用点运算符
obj[123] // "hello 123"  
```
#### 属性的赋值
（略）
#### 属性的查看
查看一个对象本身的所有属性，可以使用Object.keys方法。
```javascript
Object.keys(obj);
// ["6", "123", "p", "bar", "hello world", "0.7"]
```
#### 属性的删除
```javascript
delete obj.p // true
```
* 删除一个不存在的属性，delete不报错，而且返回true

* 只有一种情况，delete命令会返回false，那就是该属性存在，且不得删除。

    ```javascript
    var obj = Object.defineProperty({}, 'p', {
        value: 123,
        configurable: false
    });
    delete obj.p // false
    ```
* delete命令只能删除对象本身的属性，无法删除继承的属性,虽然delete命令返回true，但该属性并没有被删除，依然存在。

#### in 运算符
```javascript
var obj = { p: 1 };
'p' in obj // true
'toString' in obj // true
```
in运算符不能识别哪些属性是对象自身的，哪些属性是继承的。就像上面代码中，对象obj本身并没有toString属性，但是in运算符会返回true，因为这个属性是继承的。

这时，可以使用对象的hasOwnProperty方法判断一下，是否为对象自身的属性。


## 函数

## 运算符
## 标准库
## 面向对象编程
## 异步操作
## DOM
## 事件
## 浏览器模型
