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
#### （1）value
（略）
#### （2）writable

如果原型对象的某个属性的writable为false，那么子对象将无法自定义这个属性。
```javascript
var proto = Object.defineProperty({}, 'foo', {
  value: 'a',
  writable: false
});

var obj = Object.create(proto);

obj.foo = 'b';
obj.foo // 'a'
```

但是，有一个规避方法，就是通过覆盖属性描述对象，绕过这个限制。原因是这种情况下，原型链会被完全忽视。

```javascript
var proto = Object.defineProperty({}, 'foo', {
  value: 'a',
  writable: false
});

var obj = Object.create(proto);
Object.defineProperty(obj, 'foo', {
  value: 'b'
});

obj.foo // "b"
```

#### （3） enumerable
如果一个属性的enumerable为false，下面三个操作不会取到该属性。

* for..in循环
* Object.keys方法
* JSON.stringify方法

只有可遍历的属性，才会被for...in循环遍历，同时还规定toString这一类实例对象继承的原生属性，都是不可遍历的，这样就保证了for...in循环的可用性。

* for...in循环包括继承的属性
* Object.keys方法不包括继承的属性
* 如果需要获取对象自身的所有属性，不管是否可遍历，可以使用Object.getOwnPropertyNames方法。

#### （4）configurable

configurable(可配置性）返回一个布尔值，决定了是否可以修改属性描述对象。也就是说，configurable为false时，value、writable、enumerable和configurable都不能被修改了。

> writable只有在false改为true会报错，true改为false是允许的。

> 至于value，只要writable和configurable有一个为true，就允许改动。

另外，configurable为false时，直接目标属性赋值，不报错，但不会成功。如果是严格模式，还会报错。

可配置性决定了目标属性是否可以被删除（delete）。


### 5.2.7 存取器
### 5.2.8 对象的拷贝

有时，我们需要将一个对象的所有属性，拷贝到另一个对象，可以用下面的方法实现。
```javascript
var extend = function (to, from) {
  for (var property in from) {
    to[property] = from[property];
  }

  return to;
}

extend({}, {
  a: 1
})
// {a: 1}
```
上面这个方法的问题在于，如果遇到存取器定义的属性，会只拷贝值。
```javascript
extend({}, {
  get a() { return 1 }
})
// {a: 1}
```
为了解决这个问题，我们可以通过Object.defineProperty方法来拷贝属性。
```javascript
var extend = function (to, from) {
  for (var property in from) {
    if (!from.hasOwnProperty(property)) continue;
    Object.defineProperty(
      to,
      property,
      Object.getOwnPropertyDescriptor(from, property)
    );
  }

  return to;
}

extend({}, { get a(){ return 1 } })
// { get a(){ return 1 } })
```
上面代码中，hasOwnProperty那一行用来过滤掉继承的属性，否则会报错，因为Object.getOwnPropertyDescriptor读不到继承属性的属性描述对象。

### 5.2.8 控制对象状态
#### （1）Object.preventExtensions()

Object.preventExtensions方法可以使得一个对象无法再添加新的属性。

#### （2）Object.isExtensible()

Object.isExtensible方法用于检查一个对象是否使用了Object.preventExtensions方法。

#### （3）Object.seal()
Object.seal方法使得一个对象既无法添加新属性，也无法删除旧属性。

Object.seal实质是把属性描述对象的configurable属性设为false，因此属性描述对象不再能改变了。

Object.seal只是禁止新增或删除属性，并不影响修改某个属性的值，此时p属性的可写性由writable决定。

#### （4）Object.isSealed()

Object.isSealed方法用于检查一个对象是否使用了Object.seal方法。

这时，Object.isExtensible方法也返回false。

#### （5）Object.freeze()

Object.freeze方法可以使得一个对象无法添加新属性、无法删除旧属性、也无法改变属性的值，使得这个对象实际上变成了常量。如果在严格模式下，则会报错。

#### （6）Object.isFrozen()
Object.isFrozen方法用于检查一个对象是否使用了Object.freeze方法。

使用Object.freeze方法以后，Object.isSealed将会返回true，Object.isExtensible返回false。

Object.isFrozen的一个用途是，确认某个对象没有被冻结后，再对它的属性赋值。

### 5.2.9 局限性

上面的三个方法锁定对象的可写性有一个漏洞：可以通过改变原型对象，来为对象增加属性。

* 一种解决方案是，把obj的原型也冻结住。

另外一个局限是，如果属性值是对象，上面这些方法只能冻结属性指向的对象，而不能冻结对象本身的内容。

## 5.3 Array对象

### 5.3.1 构造函数

Array构造函数有一个很大的缺陷，就是不同的参数，会导致它的行为不一致。
```javascript
// 无参数时，返回一个空数组
new Array() // []

// 单个正整数参数，表示返回的新数组的长度
new Array(1) // [ empty ]
new Array(2) // [ empty x 2 ]

// 非正整数的数值作为参数，会报错
new Array(3.2) // RangeError: Invalid array length
new Array(-3) // RangeError: Invalid array length

// 单个非数值（比如字符串、布尔值、对象等）作为参数，
// 则该参数是返回的新数组的成员
new Array('abc') // ['abc']
new Array([1]) // [Array[1]]

// 多参数时，所有参数都是返回的新数组的成员
new Array(1, 2) // [1, 2]
new Array('a', 'b', 'c') // ['a', 'b', 'c']
```
可以看到，Array作为构造函数，行为很不一致。因此，不建议使用它生成新数组，直接使用数组字面量是更好的做法。
```javascript
// bad
var arr = new Array(1, 2);

// good
var arr = [1, 2];
```
> 注意，如果参数是一个正整数，返回数组的成员都是空位。虽然读取的时候返回undefined，但实际上该位置没有任何值。虽然可以取到length属性，但是取不到键名。

### 5.3.2 静态方法

#### Array.isArray()
Array.isArray方法返回一个布尔值，表示参数是否为数组。它可以弥补typeof运算符的不足。

### 5.3.3 实例方法
#### （1） valueOf()，toString()

数组的valueOf方法返回数组本身。

数组的toString方法返回数组的字符串形式。

#### （2）push()，pop()
push方法用于在数组的末端添加一个或多个元素，并返回添加新元素后的数组长度。

> 注意，该方法会改变原数组。

pop方法用于删除数组的最后一个元素，并返回该元素。

> 注意，该方法会改变原数组。

对空数组使用pop方法，不会报错，而是返回undefined。

push和pop结合使用，就构成了“后进先出”的栈结构（stack）。

#### （3）shift()，unshift()

shift()方法用于删除数组的第一个元素，并返回该元素。

> 注意，该方法会改变原数组。

shift()方法可以遍历并清空一个数组。

push()和shift()结合使用，就构成了“先进先出”的队列结构（queue）。

unshift()方法用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。

> 注意，该方法会改变原数组。

unshift()方法可以接受多个参数，这些参数都会添加到目标数组头部。

#### （4）join()

join()方法以指定参数作为分隔符，将所有数组成员连接为一个字符串返回。如果不提供参数，默认用逗号分隔。

如果数组成员是undefined或null或空位，会被转成空字符串。

通过call方法，这个方法也可以用于字符串或类似数组的对象。
```javascript
Array.prototype.join.call('hello', '-')
// "h-e-l-l-o"

var obj = { 0: 'a', 1: 'b', length: 2 };
Array.prototype.join.call(obj, '-')
// 'a-b'
```
#### （5）concat()

concat方法用于多个数组的合并。它将新数组的成员，添加到原数组成员的后部，然后返回一个新数组，原数组不变。

除了数组作为参数，concat也接受其他类型的值作为参数，添加到目标数组尾部。

如果数组成员包括对象，concat方法返回当前数组的一个浅拷贝。所谓“浅拷贝”，指的是新数组拷贝的是对象的引用。

#### （6）reverse()
reverse方法用于颠倒排列数组元素，返回改变后的数组。
> 注意，该方法将改变原数组。

#### （7）slice()
slice方法用于提取目标数组的一部分，返回一个新数组，原数组不变。

```javascript
arr.slice(start, end);
```

如果slice方法的参数是负数，则表示倒数计算的位置。

```javascript
var a = ['a', 'b', 'c'];
a.slice(-2) // ["b", "c"]
a.slice(-2, -1) // ["b"]
```

如果第一个参数大于等于数组长度，或者第二个参数小于第一个参数，则返回空数组。

slice方法的一个重要应用，是将类似数组的对象转为真正的数组。
```javascript
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// ['a', 'b']

Array.prototype.slice.call(document.querySelectorAll("div"));
Array.prototype.slice.call(arguments);
```

#### （8）splice()
splice方法用于删除原数组的一部分成员，并可以在删除的位置添加新的数组成员，返回值是被删除的元素。

> 注意，该方法会改变原数组。
```javascript
arr.splice(start, count, addElement1, addElement2, ...);
```
splice的第一个参数是删除的起始位置（从0开始），第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。

起始位置如果是负数，就表示从倒数位置开始删除。

如果只是单纯地插入元素，splice方法的第二个参数可以设为0。

如果只提供第一个参数，等同于将原数组在指定位置拆分成两个数组。

#### （9）sort()
sort方法对数组成员进行排序，默认是按照字典顺序排序。排序后，原数组将被改变。

sort方法不是按照大小排序，而是按照字典顺序。也就是说，数值会被先转成字符串，再按照字典顺序进行比较。

如果想让sort方法按照自定义方式排序，可以传入一个函数作为参数。
```javascript
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]
```
上面代码中，sort的参数函数本身接受两个参数，表示进行比较的两个数组成员。如果该函数的返回值大于0，表示第一个成员排在第二个成员后面；其他情况下，都是第一个元素排在第二个元素前面。

#### （10）map()

map方法将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回，原数组没有变化。

map方法接受一个函数作为参数。该函数调用时，map方法向它传入三个参数：当前成员、当前位置和数组本身。
```javascript
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
});
// [0, 2, 6]
```
上面代码中，map方法的回调函数有三个参数，elem为当前成员的值，index为当前成员的位置，arr为原数组（[1, 2, 3]）。

map方法还可以接受第二个参数，用来绑定回调函数内部的this变量（详见《this 变量》一章）。

```javascript
var arr = ['a', 'b', 'c'];
[1, 2].map(function (e) {
  return this[e];
}, arr)
// ['b', 'c']
```

如果数组有空位，map方法的回调函数在这个位置不会执行，会跳过数组的空位。

map方法不会跳过undefined和null，但是会跳过空位。

#### （11）forEach()
forEach方法与map方法很相似，也是对数组的所有成员依次执行参数函数。但是，forEach方法不返回值，只用来操作数据。这就是说，如果数组遍历的目的是为了得到返回值，那么使用map方法，否则使用forEach方法。

forEach的用法与map方法一致，参数是一个函数，该函数同样接受三个参数：当前值、当前位置、整个数组。

forEach方法也可以接受第二个参数，绑定参数函数的this变量。

> 注意，forEach方法无法中断执行，总是会将所有成员遍历完。如果希望符合某种条件时，就中断遍历，要使用for循环。

forEach方法也会跳过数组的空位。

forEach方法不会跳过undefined和null，但会跳过空位。

#### （12）filter()

filter方法用于过滤数组成员，满足条件的成员组成一个新数组返回。

它的参数是一个函数，所有数组成员依次执行该函数，返回结果为true的成员组成一个新数组返回。

该方法不会改变原数组。

filter方法的参数函数可以接受三个参数：当前成员，当前位置和整个数组。

filter方法还可以接受第二个参数，用来绑定参数函数内部的this变量。

#### （13）some()，every()

这两个方法类似“断言”（assert），返回一个布尔值，表示判断数组成员是否符合某种条件。

它们接受一个函数作为参数，所有数组成员依次执行该函数。该函数接受三个参数：当前成员、当前位置和整个数组，然后返回一个布尔值。

some方法是只要一个成员的返回值是true，则整个some方法的返回值就是true，否则返回false。

every方法是所有成员的返回值都是true，整个every方法才返回true，否则返回false。

> 注意，对于空数组，some方法返回false，every方法返回true，回调函数都不会执行。

some和every方法还可以接受第二个参数，用来绑定参数函数内部的this变量。

#### （14）reduce()，reduceRight()

reduce方法和reduceRight方法依次处理数组的每个成员，最终累计为一个值。它们的差别是，reduce是从左到右处理（从第一个成员到最后一个成员），reduceRight则是从右到左（从最后一个成员到第一个成员），其他完全一样。

[1, 2, 3, 4, 5].reduce(function (a, b) {
  console.log(a, b);
  return a + b;
})
// 1 2
// 3 3
// 6 4
// 10 5
//最后结果：15
上面代码中，reduce方法求出数组所有成员的和。第一次执行，a是数组的第一个成员1，b是数组的第二个成员2。第二次执行，a为上一轮的返回值3，b为第三个成员3。第三次执行，a为上一轮的返回值6，b为第四个成员4。第四次执行，a为上一轮返回值10，b为第五个成员5。至此所有成员遍历完成，整个方法的返回值就是最后一轮的返回值15。

reduce方法和reduceRight方法的第一个参数都是一个函数。该函数接受以下四个参数。

累积变量，默认为数组的第一个成员
当前变量，默认为数组的第二个成员
当前位置（从0开始）
原数组
这四个参数之中，只有前两个是必须的，后两个则是可选的。

如果要对累积变量指定初值，可以把它放在reduce方法和reduceRight方法的第二个参数。

[1, 2, 3, 4, 5].reduce(function (a, b) {
  return a + b;
}, 10);
// 25
上面代码指定参数a的初值为10，所以数组从10开始累加，最终结果为25。注意，这时b是从数组的第一个成员开始遍历。

上面的第二个参数相当于设定了默认值，处理空数组时尤其有用。

function add(prev, cur) {
  return prev + cur;
}

[].reduce(add)
// TypeError: Reduce of empty array with no initial value
[].reduce(add, 1)
// 1
上面代码中，由于空数组取不到初始值，reduce方法会报错。这时，加上第二个参数，就能保证总是会返回一个值。

下面是一个reduceRight方法的例子。

function substract(prev, cur) {
  return prev - cur;
}

[3, 2, 1].reduce(substract) // 0
[3, 2, 1].reduceRight(substract) // -4
上面代码中，reduce方法相当于3减去2再减去1，reduceRight方法相当于1减去2再减去3。

由于这两个方法会遍历数组，所以实际上还可以用来做一些遍历相关的操作。比如，找出字符长度最长的数组成员。

function findLongest(entries) {
  return entries.reduce(function (longest, entry) {
    return entry.length > longest.length ? entry : longest;
  }, '');
}

findLongest(['aaa', 'bb', 'c']) // "aaa"
上面代码中，reduce的参数函数会将字符长度较长的那个数组成员，作为累积值。这导致遍历所有成员之后，累积值就是字符长度最长的那个成员。

indexOf()，lastIndexOf()
indexOf方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1。

var a = ['a', 'b', 'c'];

a.indexOf('b') // 1
a.indexOf('y') // -1
indexOf方法还可以接受第二个参数，表示搜索的开始位置。

['a', 'b', 'c'].indexOf('a', 1) // -1
上面代码从1号位置开始搜索字符a，结果为-1，表示没有搜索到。

lastIndexOf方法返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1。

var a = [2, 5, 9, 2];
a.lastIndexOf(2) // 3
a.lastIndexOf(7) // -1
注意，这两个方法不能用来搜索NaN的位置，即它们无法确定数组成员是否包含NaN。

[NaN].indexOf(NaN) // -1
[NaN].lastIndexOf(NaN) // -1
这是因为这两个方法内部，使用严格相等运算符（===）进行比较，而NaN是唯一一个不等于自身的值。

链式使用
上面这些数组方法之中，有不少返回的还是数组，所以可以链式使用。

var users = [
  {name: 'tom', email: 'tom@example.com'},
  {name: 'peter', email: 'peter@example.com'}
];

users
.map(function (user) {
  return user.email;
})
.filter(function (email) {
  return /^t/.test(email);
})
.forEach(function (email) {
  console.log(email);
});
// "tom@example.com"
上面代码中，先产生一个所有 Email 地址组成的数组，然后再过滤出以t开头的 Email 地址，最后将它打印出来。

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
