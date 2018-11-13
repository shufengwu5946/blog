---
title: JavaScript学习笔记（一）
date: 
tags:
    - JavaScript
---

来自教程 https://wangdoc.com/javascript/basic/index.html

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

#### in 运算符————属性是否存在
```javascript
var obj = { p: 1 };
'p' in obj // true
'toString' in obj // true
```
in运算符不能区分哪些属性是对象自身的，还是继承的。

就像上面代码中，对象obj本身并没有toString属性，但是in运算符会返回true，因为这个属性是继承的。

这时，可以使用对象的hasOwnProperty方法判断是否为对象自身的属性。

#### for...in ————属性的遍历

```javascript
var obj = {a: 1, b: 2, c: 3};
for (var i in obj) {
  console.log('键名：', i);
  console.log('键值：', obj[i]);
}
// 键名： a
// 键值： 1
// 键名： b
// 键值： 2
// 键名： c
// 键值： 3
```
两个使用**注意点**:

* 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
* 它不仅遍历对象自身的属性，还遍历继承的属性。

只想遍历对象自身的属性，结合使用hasOwnProperty方法：
```javascript
for (var key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
```
### 2.7.3 with语句

操作同一个对象的多个属性时，提供一些书写的方便。

**不推荐使用！！！**

## 2.8 函数

### 2.8.1 概述

#### 函数声明

三种声明函数的方式：

##### （1）function 命令

```javascript
function print(s) {
  console.log(s);
}
```
##### （2）函数表达式
```javascript
var print = function(s) {
  console.log(s);
};
```
> function命令后面不带有函数名。如果加上函数名，该函数名只在函数体内部有效，在函数体外部无效。

##### （3）Function 构造函数
```java
var add = new Function(
  'x',
  'y',
  'return x + y'
);
```
#### 函数的重复声明

* 如果同一个函数被多次声明，后面的声明就会覆盖前面的声明。

* 由于函数名的提升，前一次声明在任何时候都是无效的，这一点要特别注意。

#### 函数名的提升
* 采用function命令声明函数时，整个函数会像变量声明一样，被提升到代码头部。

* 如果采用赋值语句定义函数，JavaScript 就会报错。
```javascript
f();
var f = function (){};
// TypeError: undefined is not a function
```
* 同时采用function命令和赋值语句声明同一个函数，最后总是采用赋值语句的定义。
```javascript
var f = function () {
  console.log('1');
}
function f() {
  console.log('2');
}
f() // 1
```

### 2.8.2 函数的属性和方法
#### name 属性
* 如果变量的值是一个具名函数，那么name属性返回function关键字之后的那个函数名。

#### length 属性
* 函数的length属性返回函数预期传入的参数个数，即函数定义之中的参数个数。

### 2.8.3 函数作用域

#### 函数本身的作用域

函数本身也是一个值，也有自己的作用域。它的作用域与变量一样，就是其声明时所在的作用域，与其运行时所在的作用域无关。
```javascript
var a = 1;
var x = function () {
  console.log(a);
};
function f() {
  var a = 2;
  x();
}
f() // 1
```
### 2.8.4 参数
#### 传递方式

1、 函数参数如果是原始类型的值（数值、字符串、布尔值），传递方式是传值传递（passes by value）。这意味着，在函数体内修改参数值，不会影响到函数外部。
```javascript
var p = 2;
function f(p) {
  p = 3;
}
f(p);
p // 2
```
上面代码中，变量p是一个原始类型的值，传入函数f的方式是传值传递。因此，在函数内部，p的值是原始值的拷贝，无论怎么修改，都不会影响到原始值。

2、 但是，如果函数参数是复合类型的值（数组、对象、其他函数），传递方式是传址传递（pass by reference）。也就是说，传入函数的原始值的地址，因此在函数内部修改参数，将会影响到原始值。
```javascript
var obj = { p: 1 };
function f(o) {
  o.p = 2;
}
f(obj);
obj.p // 2
```
上面代码中，传入函数f的是参数对象obj的地址。因此，在函数内部修改obj的属性p，会影响到原始值。

3、 注意，如果函数内部修改的，不是参数对象的某个属性，而是替换掉整个参数，这时不会影响到原始值。
```javascript
var obj = [1, 2, 3];
function f(o) {
  o = [2, 3, 4];
}
f(obj);
obj // [1, 2, 3]
```
上面代码中，在函数f内部，参数对象obj被整个替换成另一个值。这时不会影响到原始值。这是因为，形式参数（o）的值实际是参数obj的地址，重新对o赋值导致o指向另一个地址，保存在原地址上的值当然不受影响。

#### arguments 对象
* 正常模式下，arguments对象可以在运行时修改。
* 严格模式下，arguments对象是一个只读对象，修改它是无效的，但不会报错。
* 虽然arguments很像数组，但它是一个对象。数组专有的方法（比如slice和forEach），不能在arguments对象上直接使用。如果要让arguments对象使用数组方法，真正的解决方法是将arguments转为真正的数组:

  ```javascript
  var args = Array.prototype.slice.call(arguments);

  // 或者
  var args = [];
  for (var i = 0; i < arguments.length; i++) {
    args.push(arguments[i]);
  }
  ```
### 2.8.5 函数的其他知识点

#### 闭包

function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
上面代码中，函数f1的返回值就是函数f2，由于f2可以读取f1的内部变量，所以就可以在外部获得f1的内部变量了。

**闭包**就是函数f2，即能够读取其他函数内部变量的函数。由于在 JavaScript 语言中，只有函数内部的子函数才能读取内部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。

* 闭包最大的特点，就是它**可以“记住”诞生的环境**，比如f2记住了它诞生的环境f1，所以从f2可以得到f1的内部变量。在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

闭包的最大用处有两个，一个是**可以读取函数内部的变量**，另一个就是**让这些变量始终保持在内存**中，即闭包可以使得它诞生环境一直存在。请看下面的例子，闭包使得内部变量记住上一次调用时的运算结果。

```javascript
function createIncrementor(start) {
  return function () {
    return start++;
  };
}

var inc = createIncrementor(5);
inc() // 5
inc() // 6
inc() // 7
```
上面代码中，start是函数createIncrementor的内部变量。通过闭包，start的状态被保留了，每一次调用都是在上一次调用的基础上进行计算。从中可以看到，闭包inc使得函数createIncrementor的内部环境，一直存在。所以，闭包可以看作是函数内部作用域的一个接口。

为什么会这样呢？原因就在于inc始终在内存中，而inc的存在依赖于createIncrementor，因此也始终在内存中，不会在调用结束后，被垃圾回收机制回收。

闭包的另一个用处，**是封装对象的私有属性和私有方法**。
```javascript
function Person(name) {
  var _age;
  function setAge(n) {
    _age = n;
  }
  function getAge() {
    return _age;
  }

  return {
    name: name,
    getAge: getAge,
    setAge: setAge
  };
}

var p1 = Person('张三');
p1.setAge(25);
p1.getAge() // 25
```
上面代码中，函数Person的内部变量_age，通过闭包getAge和setAge，变成了返回对象p1的私有变量。

> 注意，外层函数每次运行，都会生成一个新的闭包，而这个闭包又会保留外层函数的内部变量，所以内存消耗很大。因此不能滥用闭包，否则会造成网页的性能问题。

#### 立即调用的函数表达式（IIFE）
```javascript
function(){ /* code */ }();
// SyntaxError: Unexpected token (
```
JavaScript 引擎看到行首是function关键字之后，认为这一段都是函数的定义，不应该以圆括号结尾，所以就报错了。

解决方法就是不要让function出现在行首，就是将其放在一个圆括号里面。
```javascript
(function(){ /* code */ }());
// 或者
(function(){ /* code */ })();
```

通常情况下，只对匿名函数使用这种“立即执行的函数表达式”。它的目的有两个：

* 一是不必为函数命名，避免了污染全局变量；

* 二是 IIFE 内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量。

```javascript
// 写法一
var tmp = newData;
processData(tmp);
storeData(tmp);

// 写法二
(function () {
  var tmp = newData;
  processData(tmp);
  storeData(tmp);
}());
```
上面代码中，写法二比写法一更好，因为完全避免了污染全局变量。

### 2.8.6 eval 命令

eval命令接受一个字符串作为参数，并将这个字符串当作语句执行。

> 所以一般不推荐使用！！！

## 2.9 数组
### 2.9.1 length属性

由于数组本质上是一种对象，所以可以为数组添加属性，但是这不影响length属性的值。
```javascript
var a = [];

a['p'] = 'abc';
a.length // 0

a[2.1] = 'abc';
a.length // 0
```
上面代码将数组的键分别设为字符串和小数，结果都不影响length属性。因为，length属性的值就是等于最大的数字键加1，而这个数组没有整数键，所以length属性保持为0。

如果数组的键名是添加超出范围的数值，该键名会自动转为字符串。

### 2.9.2 in运算符
### 2.9.3 for...in 循环和数组的遍历
* 在遍历数组时，也遍历到了数字键之外的其他键，所以，不推荐使用for...in遍历数组。
* 数组的遍历可以考虑使用for循环或while循环。
* 数组的forEach方法，也可以用来遍历数组。

### 2.9.4数组的空位
* 数组的空位不影响length属性。
* 如果最后一个元素后面有逗号，并不会产生空位。
* 使用delete命令删除一个数组成员，会形成空位，并且不会影响length属性。
* 使用数组的forEach方法、for...in结构、以及Object.keys方法进行遍历，空位都会被跳过。
* 如果某个位置是undefined，遍历的时候就不会被跳过。

### 2.9.5 类似数组的对象
* “类似数组的对象”并不是数组，因为它们不具备数组特有的方法。对象obj没有数组的push方法，使用该方法就会报错。

* “类似数组的对象”的根本特征，就是具有length属性，这种length属性不是动态值，不会随着成员的变化而变化。

* 数组的slice方法可以将“类似数组的对象”变成真正的数组。
  ```javascript
  var arr = Array.prototype.slice.call(arrayLike);
  ```
* 除了转为真正的数组，“类似数组的对象”还有一个办法可以使用数组的方法，就是通过call()把数组的方法放到对象上面。
  ```javascript
  function print(value, index) {
    console.log(index + ' : ' + value);
  }
  Array.prototype.forEach.call(arrayLike, print);
  ```
> 注意，这种方法比直接使用数组原生的forEach要慢，所以最好还是先将“类似数组的对象”转为真正的数组，然后再直接调用数组的forEach方法。
