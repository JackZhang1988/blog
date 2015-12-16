---
title:  "NodeJs生产环境最佳实践-设计篇-流模式"
date:   2015-01-27 10:18:00
description: NodeJs生产环境最佳实践-设计篇-流模式
banner: https://www.eduonix.com/blog/wp-content/uploads/2015/11/Node-JS-Coding-Best-Practices-for-Beginners.png
categories: NodeJs生产环境最佳实践
---

[文章译自:https://www.joyent.com/developers/node/design](https://www.joyent.com/developers/node/design)

##流模式

流模式是另一个在node中广泛使用的设计模式。就像很多核心组件会实现事件分发器模式一样，许多组件也会实现[可读](http://nodejs.org/api/stream.html#stream_class_stream_readable)，[可写](http://nodejs.org/api/stream.html#stream_class_stream_writable)或者双工([Duplex](http://nodejs.org/api/stream.html#stream_class_stream_duplex))接口。

流是一种提供可读、可写、排放数据、数据接收、数据接收完毕、流关闭等常用事件的抽象接口。这些事件的功能会非常强大，但最强大的部分还是你可以把一些流封装成管道的形式。

管道对简化你代码的复杂性，更易理解，提高可读性很有帮助。使用**.pipe()**可以使Node通过管道的形式传输背压。这里的背压指的是你仅仅去读你可以写的部分或者写你可以读的部分。因此你仅需要在规定时间内保持你要完成的工作在内存中就可以了。


比如你想要发送一个来自于标准输入流的数据到一个本地文件和远程服务器中：

```javascript
var fs = require('fs');
var net = require('net');

var localFile = fs.createWriteStream('localFile.tmp');

net.connect('255.255.255.255', 12345, function(client) {
  process.stdin.pipe(client);
  process.stdin.pipe(localFile);
});
```

客户端和本地文件都会从标准输入中读取数据，但是数据同步速度只会和最慢的可读器读取速度一致。

pipe()同样返回目标流。如果目标对象是支持双工通信的，或者它就像[Transform](http://nodejs.org/api/stream.html#stream_class_stream_transform)一样是双工模式的特例，你可以以链式接口使用管道。

你可以从最新本本的Node[关于流的介绍](http://www.joyent.com/blog/streams-in-node)中了解更多知识。
