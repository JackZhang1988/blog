---
title:  "NodeJs生产环境最佳实践-设计篇-流程控制"
date:   2015-01-28 19:18:00
description: NodeJs生产环境最佳实践-设计篇-流程控制
banner: https://www.eduonix.com/blog/wp-content/uploads/2015/11/Node-JS-Coding-Best-Practices-for-Beginners.png
categories: NodeJs生产环境最佳实践
---

[文章译自:https://www.joyent.com/developers/node/design](https://www.joyent.com/developers/node/design)

##流程控制

因为JavaScript有函数作为类对象已经闭包的概念，所以它很容易在需要的地方定义callback。这在你定义解决方案的原型十分有利，它允许你在正确的地方整合你的逻辑。
而且它可以像圣诞树一样将复杂的一系列方法整合起来。

比如你想要连续地读取一系列文件，然后在这些任务中执行以下公用任务：

```javascript
fs.readFile('firstFile', 'utf8', function firstCb(err, firstFile) {
  doSomething(firstFile);
  fs.readFile('secondFile', 'utf8', function secondCb(err, secondFile) {
    doSomething(secondFile);
    fs.readFile('thirdFile', 'utf8', function thirdCb(err, thirdFile) {
      doSomething(thirdFile);
    });
  });
});
```

这看起来不是很糟糕，但在设计模式上还是有一些不好的地方：

* 如果这部分逻辑太过复杂，对于理解这段流程和操作的顺序都变得异常困难。
* 这里面没有任何的错误处理，当我们执行第三层调用时，我们已经将第一层的错误跟踪两次了。
* 在垃圾收集器只有在第三个操作完成时才会收集第一个文件读取的结果。闭包引起的内存泄露是JavaScript应用中常见的问题，而且通常难以诊断和检测。

>###vasync
>[vasync](https://npmjs.org/package/vasync)是一个流程控制库，它的设计灵感来自于[async](https://npmjs.org/package/async)模块。然而vasync被设计使得事件监控者对他们程序的运行的进度有足够的可见性和可观察性。这些信息对判断一个任务在什么时候出错十分重要。