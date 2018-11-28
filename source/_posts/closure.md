---
title: 闭包（Closure）
date: 
tags:
    - 闭包
---

# 闭包

## 变量的作用域

要理解闭包，首先必须理解Javascript特殊的变量作用域。

变量的作用域无非就是两种：全局作用域、函数作用域和块级作用域(ES6)。现在主要讨论全局作用域和函数作用域。

函数内部可以直接读取全局作用域变量。

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

函数f2就在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。但是反过来就不行，f2内部的局部变量，对f1就是不可见的。这就是**链式作用域**，函数的内部环境可以通过作用域链访问到所有的外部环境，但是外部环境却不可以访问外部环境，这就是作用域的关键。

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

一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在。此外，闭包可以避免全局变量的污染。

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

### 1、模拟对象的私有属性和私有方法。

```javascript
var makeCounter = function() {

    //私有属性，不能通过对象直接访问
    var privateCounter = 0;

    //私有方法，只能被同一个类中的其它方法所调用。
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

在计算机科学中，柯里化是把接受多个参数的函数变换成接受一个单一参数(最初函数的第一个参数)的函数，并且返回接受余下的参数且返回结果的新函数的技术。

柯里化就是预先将函数的某些参数传入，得到一个简单的函数，预先传入的参数被保存在闭包中。比如：

```javascript
var adder = function(num){
  return function(y){
     return num + y;
  }
}
var inc = adder(1);
var dec = adder(-1)
```
这里的inc/dec两个变量事实上是两个函数，可以通过括号来调用，比如下例中的用法：

```javascript
// inc, dec现在是两个新的函数，作用是将传入的参数值(+/-)1
print(inc(99));//100
print(dec(101));//100
print(adder(100)(2));//102
print(adder(2)(100));//102
```

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

### 5、防抖、节流与分时

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

![](closure/debounce.gif)

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

![](closure/throttle.gif)

#### 分时函数

一个例子是创建 WebQQ的 QQ好友列表。列表中通常会有成百上千个好友，如果一个好友用一个节点来表示，当我们在页面中渲染这个列表的时候，可能要一次性往页面中创建成百上千个节点。在短时间内往页面中大量添加 DOM节点显然也会让浏览器吃不消，我们看到的结果往往就是浏览器的卡顿甚至假死 。

这时候就需要分时函数，每隔一段时间运行一段，比如没200ms加载10个QQ好友。

```javascript
var timeChunk = function(argsAry, fn, count) {
    var interval;
    var exec = function() {
        var l = argsAry.length;
        for (var i = 0; i < Math.min(count || 1, l); i++) {
            var arg = argsAry.shift();
            fn(arg);
        }
    }

    return function() {
        interval = setInterval(function() {
            var flag = exec();
            if (argsAry.length === 0) {
                clearInterval(interval);
                interval = null;
            }
        }, 500)
    }
};

var a = [],func;

// 模拟QQ好友列表
for (var i = 0; i < 36; i++) {
    a.push(i);
}

//
func = timeChunk(a, function(i) {
    console.log(i);
}, 200)
func();
```

### 6、实现单例模式

```javascript
var getSingle = function(func){
    var ret = null;

    return function(){
        return ret || ret = func.apply(this, Array.prototype.slice.call(arguments));
    }
}

var getScript = getSingle(function(){ 
    return document.createElement( 'script' ); 
}); 
 
var script1 = getScript(); 
var script2 = getScript(); 
 
alert ( script1 === script2 );    // 输出：true  
```

### 7、AOP应用

AOP（面向切面编程）的主要作用是把一些跟核心业务逻辑模块无关的功能抽离出来，这些
跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这些功能抽离出来之后 。再通过“动态织入”的方式掺入业务逻辑模块中。这样做的好处首先是可以保持业务逻辑模块的纯净和高内聚性，其次是可以很方便地复用日志统计等功能模块。

#### 例子：数据统计上报

分离业务代码和数据统计代码,无论在什么语言中,都是AOP的经典应用之一.在项目开发的结尾阶段难免要加上很多统计数据的代码,这些过程很可能让我们被迫改动早已封装好的函数.

比如页面中有一个登录button,点击这个button会弹出登录浮层,与此同时要进行数据上报,来统计有多少用户点击了这个登录button

```html
<html>
    <button id="button" tag="login">点击打开登录浮层</button>
    <script>
        var showLogin = function() {
            console.log("打开登录浮层");
            log(this.getAttribute('tag'));
        }

        var log = function(tag) {
            console.log("上传标签为:" + tag);
            //(new Image).src="http://xxx.com/report?tag="+tag; //真正的上传代码略
        }

    document.getElementById('button').onclick = showLogin;
    </script>
</html>
```

我们看到在showLogin函数里,既要负责打开登录浮层,又要负责数据上传,这是两个层面的功能,在此处却被耦合进一个函数里.使用AOP分离之后,代码如下:
```html
<html>
    <button id="button" tag="login">点击打开登录浮层</button>
    <script>
        Function.prototype.after = function(afterfn) {
            var __self = this;
            return function() {
                var ret = __self.apply(this, arguments);
                afterfn.apply(this, arguments);
                return ret;
            }
        }

        var showLogin=function(){
            console.log("打开登录浮层");
        }

        var log=function(){
            console.log("上传标签为:"+this.getAttribute('tag'));
        }

        showLogin=showLogin.after(log);

        document.getElementById('button').onclick=showLogin;
    </script>
</html>
```

### 8、模拟块级作用域

ES5没有块级作用域的概念。这意味着在块语句中定义的变量，实际上是包含在函数中而非语句中创建的。因此从函数内的所有变量定义开始，就可以在函数内部随处访问它。

```javascript
function outputNumbers(count){
    for(var i = 0; i < count; i++){
        console.log(i); // 0, 1, ... count - 1
    }
    console.log(i); // count
}
```
函数包装器可以用来模仿块作用域并避免这个问题。 

函数包装器的作用：

立即执行函数中的代码，又不会再内存中留下对该函数的引用；
函数内部的所有变量都会被立即销毁(除非将这些变量赋值给了包含作用域中的变量)。

当在函数内部使用函数包装器的时候，此时函数包装器就是一个闭包，有权访问外部环境中的所有变量。

```javascript
function outputNumbers(count){
    (function(){
        //块级作用域
        for(var i = 0; i < count; i++){
            console.log(i); // 0, 1, ... count - 1
        }
    })();
    console.log(i); // error
}
```

### 9、通过对象实例方法关联函数

```javascript
function associateObjWithEvent(obj, methodName) {
    return (function (e) {
        e = e || window.event;
        return obj[methodName](e, this);
    });
}

function DhtmlObject(elementId) {
    var el = document.getElementById(elementId);
    if (el) {
        el.onclick = associateObjWithEvent(this, "doOnClick");
        el.onmouseover = associateObjWithEvent(this, "doMouseOver");
        el.onmouseout = associateObjWithEvent(this, "doMouseOut");
    }
}
DhtmlObject.prototype.doOnClick = function (event, element) {
    if (element.id === 'test') {
        console.log('test');
    } else {
        console.log('doOnClick: ' + event + ' ' + element);
    }
}
DhtmlObject.prototype.doMouseOver = function (event, element){
    console.log('doMouseOver: ' + event + ' ' + element);
}
DhtmlObject.prototype.doMouseOut = function (event, element) {
    console.log('doMouseOut: ' + event + ' ' + element);
}

document.addEventListener("DOMContentLoaded", function (event) {
    new DhtmlObject('test');
})
```
### 9、一些其他设计模式


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

this对象实在运行时基于函数的执行环境绑定的。

```javascript
var name = "The Window";

var object = {
    name : "My Object",
    getNameFunc : function(){
        return function(){
            return this.name;
        };
    }
};

alert(object.getNameFunc()());
```

**解决方法：**

将this保存在一个变量中，让闭包可以访问。

```javascript
var name = "The Window";
var object = {
    name : "My Object",

    getNameFunc : function(){
        var that = this;

        return function(){
            return that.name;
        };
    }
};

alert(object.getNameFunc()());
```
### 3、性能问题

外层函数每次运行，都会生成一个新的闭包，而这个闭包又会保留外层函数的内部变量，变量长期驻扎在内存，所以对内存产生消耗。因此不能滥用闭包，否则会造成网页的性能问题。





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

https://www.jianshu.com/p/e7f32464a8ab

http://demo.jb51.net/js/javascript_bibao/index.htm#clOtE


