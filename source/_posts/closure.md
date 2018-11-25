---
title: 闭包（Closure）
date: 
tags:
    - 闭包
---

# 闭包

## 变量的作用域

要理解闭包，首先必须理解Javascript特殊的变量作用域。

变量的作用域无非就是两种：全局变量和局部变量。

函数内部可以直接读取全局变量。

```javascript
var n = 999;

function f1() {
  console.log(n);
}
f1() // 999
```

函数外部无法读取函数内部声明的变量。

```javascript
function f1() {
  var n = 999;
}

console.log(n)
// Uncaught ReferenceError: n is not defined(
```

如何读取函数内部声明的变量？

可以在函数内部在定义一个函数：

```java
function f1() {
  var n = 999;
  function f2() {
　　console.log(n); // 999
  }
}
```

函数f2就在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。但是反过来就不行，f2内部的局部变量，对f1就是不可见的。这就是**链式作用域**，子对象会一级一级地向上寻找所有父对象的变量。所以，父对象的所有变量，对子对象都是可见的，反之则不成立。

f2能访问f1的局部变量，把f2作为f1的返回值返回，这样就能在f1外部访问它的局部变量：
```java
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```
## 闭包定义

上面的f2函数，就是闭包。

**闭包就是能够读取其他函数内部变量的函数。**

由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成"定义在一个函数内部的函数"。

所以，在本质上，闭包就是**将函数内部和函数外部连接起来的一座桥梁**。

## 闭包特点

一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在。

```javascript
function createIncrementor(start) {
  var clo = function () {
    return start++;
  }
  return clo;
}

var inc = createIncrementor(5);

inc() // 5
inc() // 6
inc() // 7
```
start是函数createIncrementor的内部变量。通过闭包clo，start的状态被保留了，每一次调用都是在上一次调用的基础上进行计算。从中可以看到，闭包inc使得函数createIncrementor的内部环境，一直存在。所以，闭包可以看作是函数内部作用域的一个接口。

**为什么会这样呢？**

闭包clo通过createIncrementor返回，并赋给全局变量inc，这使得inc始终存在于内存中，即闭包clo一直存在于内存中，闭包的存在依赖于函数createIncrementor，表明createIncrementor一直存在于内存中，createIncrementor的的变量就一直存在于内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

## 闭包使用易错点

### 1、在循环中创建闭包

```javascript
function outer(){
    var funcs = [];
    for(var i = 0;i < 5;i++){
        funcs[i] = function (){
            console.log(i);
        }
    }
    return funcs;
}
var o = outer();
o[0](); // 5
o[1](); // 5
o[2](); // 5
o[3](); // 5
o[4](); // 5
```

#### 解决方案
1、使用更多闭包

```javascript
function makeFunction(value){
    return function (){
        console.log(value);
    }
}

function outer(){
    var funcs = [];
    for(var i = 0;i < 5;i++){
        funcs[i] = makeFunction(i);
    }
    return funcs;
}
```
或
```javascript
function outer() {

    var funcs = [];

    for (var i = 0; i < 5; i++) {
        (function (value) {
            funcs[i] = function () {
                console.log(value);
            }
        })(i);
    }

    return funcs;
}
```

2、循环中变量用let声明代替var声明：
```javascript
function outer(){
    var funcs = [];
    for(let i = 0;i < 5;i++){
        funcs[i] = function (){
            console.log(i);
        }
    }
    return funcs;
}
```

## 使用场景

1、封装对象的私有属性和私有方法。
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



## 参考文献

《闭包的特征和应用场景》

https://zhuanlan.zhihu.com/p/36239651

《JS闭包可被利用的常见场景》

https://blog.csdn.net/yanghua_kobe/article/details/6780181

MDN《闭包》

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures

阮一峰《学习Javascript闭包（Closure）》

http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html

https://wangdoc.com/javascript/types/function.html#%E9%97%AD%E5%8C%85

Javascript 闭包与高阶函数 ( 一 )

https://www.cnblogs.com/likeFlyingFish/p/6421615.html

Javascript 闭包与高阶函数 ( 二 )

https://www.cnblogs.com/likeFlyingFish/p/6426892.html

https://blog.csdn.net/qq_38070608/article/details/78903596

https://www.cnblogs.com/renlong0602/p/4398883.html


