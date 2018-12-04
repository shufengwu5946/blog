---
title: JavaScript从垃圾回收角度理解闭包原理
date: 
tags:
    - 垃圾回收
    - 闭包
---

# JavaScript 垃圾回收机制

JavaScript中对内存的回收完全实现了自动管理，比较复杂，不展开讲解。

**原理**可以简单理解为：垃圾收集器按照固定的时间间隔周期性地进行垃圾回收操作，找出那些不再继续使用的变量，然后释放其所占用的内存。

# 前置知识

## 函数执行过程

### 函数对象的创建

执行到某个函数，会对应的创建一个函数对象，给这个对象增加一个scope属性，这个scope属性维护着一条作用域链（scope chain），作用域链上的每个节点，都是一个活动对象（activation object, 下面会介绍）。函数对象的scope属性的初始值，赋值自当前函数对象的父级作用域的执行上下文对象（execution context, 下面会介绍）的scope属性值。

![图1]()

### 函数活动对象的创建

当一个函数对象被执行时，会为该函数对象创建一个活动对象（activation object）。这个活动对象主要包含当前函数运行所需要的环境，具体内容有：

* 形式参量
* 函数声明
* 传入参数
* 局部变量



https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651228474&idx=1&sn=031ea46ca182f2dacf8f65cc30c6566b&chksm=bd4950be8a3ed9a87e24c664dec77bd63bb69e735887ea33dc574358070affb0fbf6eafd9f0b&mpshare=1&scene=1&srcid=1204q5xZR2W0Kw6u5utapapz#rd

https://segmentfault.com/a/1190000003021472

https://www.cnblogs.com/lsgxeva/p/7976034.html




