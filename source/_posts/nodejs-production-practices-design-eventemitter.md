---
title:  "NodeJs生产环境最佳实践-设计篇-事件分发器"
date:   2015-01-26 10:18:00
description: Thriller Comedy Horror
banner: https://www.eduonix.com/blog/wp-content/uploads/2015/11/Node-JS-Coding-Best-Practices-for-Beginners.png
categories: NodeJs生产环境最佳实践
---

[文章译自:https://www.joyent.com/developers/node/design](https://www.joyent.com/developers/node/design)

###Node.js使用Javascript语言，所以一切你知道的有关Javascript的都同样应用于Nodejs。你再编写前端代码使用的模式同样试用与编写server端应用逻辑。Node并不适用Javascript语言的扩展或修改版来完成其server端的功能。

因此，了解在Joyent的node应用中使用的设计模式会对你设计应用时很有帮助并且很有用。

#事件分发器


第一个要知道的设计模式是[事件分发](https://nodejs.org/api/events.html#events_class_events_eventemitter)模式，它允许功能实现者分发一个事件，而功能消费者可以订阅他们感兴趣的事件。你可以认为这是函数执行完成后就将回调传递给异步函数的设计模式的一个扩展。对当一个回调不能够满足需求时，事件分发模式很有用，因为通常我们的执行函数要订阅多个事件。

举例来说，一个执行函数可能要发出一个展示远程server端文件的请求。你可能期望以流的形式传回结果，当每个文件传回时执行一次回调。事件分发模式使你在每次得到一个文件时分发“file”事件，然后再你完成所有操作后分发“end”事件。


当我们实现一个事件分发器，你仅仅只需要**分发**这个事件，然后把相应参数传递过去就行。

```javascript
var EventEmitter = require('events').EventEmitter;
var util = require('util');

function MyClass() {
  if (!(this instanceof MyClass)) return new MyClass();

  EventEmitter.call(this);

  var self = this;
  setTimeout(function timeoutCb() {
    self.emit('myEvent', 'hello world', 42);
  }, 1000);
}
util.inherits(MyClass, EventEmitter);
```

MyClass的构造函数创建了一个每秒触发一次的计时器，每次计时器触发时，计时器都会分发**myEvent**事件，并传递参数**'hello world'**和数字**42**。监听这个事件你需要使用**on()**方法，这个方法已经在它继承自**EventEmitter**时添加到它的原型里了。

```javascript
var myObj = new MyClass();
var start = Date.now();
myObj.on('myEvent', function myEventCb(str, num) {
  console.log('myEvent triggered', str, num, Date.now() - start);
});
```

很重要的一点是，你订阅的EventEmitter事件会异步地通知到你，但监听它们的执行函数是在事件触发时同步执行的。所以如果myEvent事件有10个监听者，所有10个监听者都会顺序地调用而不会推迟事件轮询。要意识到同步性对于事件分发器推迟事件的发放以便让功能订阅者在同一次事件轮询中订阅多个事件，同时保证回调函数会在未来准确无误地触发非常重要。
 
>##verror
[verror](https://npmjs.org/package/verror)扩展了Error基类，它允许你以**printf**的形式输出消息。你的应用通常是一些异步方法的组合，并且处理错误时，你可能想将这个错误信息以冒泡的形式传播到你的系统里。**verror**拥有**VError**和**WError**，它们允许你通过链式结构汇集多层错误，并且同样能看到在信息中错误的组合（以**VError**的形式），或者在**WError**中有一个最终的消息，但是能用程序访问在链式结构中之前的错误。


