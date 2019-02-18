# react 整体刷新，减少UI实现细节的关注
# 单向数据流 Flux架构
http://www.ruanyifeng.com/blog/2016/01/flux.html

首先，Flux将一个应用分成四个部分。

* View： 视图层
* Action（动作）：视图层发出的消息（比如mouseClick）
* Dispatcher（派发器）：用来接收Actions、执行回调函数
* Store（数据层）：用来存放应用的状态，一旦发生变动，就提醒Views要更新页面

Flux 的最大特点，就是数据的"单向流动"。

* 用户访问 View
* View 发出用户的 Action
* Dispatcher 收到 Action，要求 Store 进行相应的更新
* Store 更新后，发出一个"change"事件
* View 收到"change"事件后，更新页面

上面过程中，数据总是"单向流动"，任何相邻的部分都不会发生数据的"双向流动"。这保证了流程的清晰。
