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





## 使用场景

### 1、封装对象的私有属性和私有方法。
```javascript
var makeCounter = function() {
  var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  }  
};

var Counter1 = makeCounter();
var Counter2 = makeCounter();
console.log(Counter1.value()); /* logs 0 */
Counter1.increment();
Counter1.increment();
console.log(Counter1.value()); /* logs 2 */
Counter1.decrement();
console.log(Counter1.value()); /* logs 1 */
console.log(Counter2.value()); /* logs 0 */
```
### 2、函数柯里化

### 3、setTimeout参数传递

一段代码想通过setTimeout来调用，那么它需要传递一个函数对象的引用来作为第一个参数。延迟的毫秒数作为第二个参数，但这个函数对象的引用无法为将要被延迟执行的对象提供参数。

```javascript
function callLater(paramA, paramB, paramC) {
    return (function () {
        paramA[paramB] = paramC;
    });
}

var funcRef = callLater(elStyle, "display", "none");
hideMenu = setTimeout(funcRef, 500);
```

### 4、封装相关功能集

```javascript

var getImgInPositionedDivHtml = (function () {
    
    var buffAr = [
            '<div id="',
        '',   //index 1, DIV ID attribute
        '" style="position:absolute;top:',
        '',   //index 3, DIV top position
        'px;left:',
        '',   //index 5, DIV left position
        'px;width:',
        '',   //index 7, DIV width
        'px;height:',
        '',   //index 9, DIV height
        'px;overflow:hidden;\"><img src=\"',
        '',   //index 11, IMG URL
        '\" width=\"',
        '',   //index 13, IMG width
        '\" height=\"',
        '',   //index 15, IMG height
        '\" alt=\"',
        '',   //index 17, IMG alt text
        '\"><\/div>'
    ];

    
    return (function (url, id, width, height, top, left, altText) {
        
        buffAr[1] = id;
        buffAr[3] = top;
        buffAr[5] = left;
        buffAr[13] = (buffAr[7] = width);
        buffAr[15] = (buffAr[9] = height);
        buffAr[11] = url;
        buffAr[17] = altText;

        return buffAr.join('');
    });
})();
```
### 5、防抖与节流

#### 防抖（Debounce）

防抖，指的是无论某个动作被连续触发多少次，直到这个连续动作停止后，才会被当作一次来执行

比如一个输入框接受用户不断输入，输入结束后才开始搜索

例：以页面滚动作为例子，可以定义一个防抖函数，接受一个自定义的 delay值，作为判断停止的时间标识

```javascript
// 函数防抖，频繁操作中不处理，直到操作完成之后（再过 delay 的时间）才一次性处理
function debounce(fn, delay) {
    delay = delay || 200;
    
    var timer = null;

    return function() {
        var arg = arguments;
          
        // 每次操作时，清除上次的定时器
        clearTimeout(timer);
        timer = null;
        
        // 定义新的定时器，一段时间后进行操作
        timer = setTimeout(function() {
            fn.apply(this, arg);
        }, delay);
    }
};

var count = 0;

window.onscroll = debounce(function(e) {
    console.log(e.type, ++count); // scroll
}, 500);
```


滚动页面，可以看到只有在滚动结束后才执行

#### 节流（Throttle）

节流，指的是无论某个动作被连续触发多少次，在定义的一段时间之内，它仅能够触发一次

比如resize和scroll时间频繁触发的操作，如果都接受了处理，可能会影响性能，需要进行节流控制

以页面滚动作为例子，可以定义一个节流函数，接受一个自定义的 delay值，作为判断停止的时间标识

需要注意的两点

要设置一个初始的标识，防止一开始处理就被执行了，同时在最后一次处理之后，也需要重新置位

也要设置定时器处理，防止两次动作未到delay值，最后一组动作触发不了

```javascript
// 函数节流，频繁操作中间隔 delay 的时间才处理一次
function throttle(fn, delay) {
    delay = delay || 200;
    
    var timer = null;
    // 每次滚动初始的标识
    var timestamp = 0;

    return function() {
        var arg = arguments;
        var now = Date.now();
        
        // 设置开始时间
        if (timestamp === 0) {
            timestamp = now;
        }
        
        clearTimeout(timer);
        timer = null;
        
        // 已经到了delay的一段时间，进行处理
        if (now - timestamp >= delay) {
            fn.apply(this, arg);
            timestamp = now;
        }
        // 添加定时器，确保最后一次的操作也能处理
        else {
            timer = setTimeout(function() {
                fn.apply(this, arg);
                // 恢复标识
                timestamp = 0;
            }, delay);
        }
    }
};

var count = 0;

window.onscroll = throttle(function(e) {
    console.log(e.type, ++count); // scroll
}, 500);
```




## 闭包需要注意的问题
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

（1）使用更多闭包

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

（2）循环中变量用let声明代替var声明：

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
### 2、关于this对象
### 3、内存泄露



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

https://blog.csdn.net/qq_42564846/article/details/81448352


