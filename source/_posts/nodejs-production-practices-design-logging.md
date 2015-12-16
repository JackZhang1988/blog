---
title:  "NodeJs生产环境最佳实践-设计篇-Logging"
date:   2015-02-02 19:18:00
description: NodeJs生产环境最佳实践-设计篇-Logging
banner: https://www.eduonix.com/blog/wp-content/uploads/2015/11/Node-JS-Coding-Best-Practices-for-Beginners.png
categories: NodeJs生产环境最佳实践
---

[文章译自:https://www.joyent.com/developers/node/design](https://www.joyent.com/developers/node/design)

##Logging

当你实际并构建你的应用时，你应该对未来有所计划尤其是考虑当你要调试时使用的工具。一个好的并且有显著效果的第一步就是给你的应用添加适量的log。确保你使用的记录log的库能提供你所需的功能。这里有一些需要考虑的方便：它是否能监控到你在意的目标，是否有你喜欢的格式化输出，以及它所调用API的方式相比Node应用和你的代码库编写应用的方式不会让你感到太陌生。

在调试的时候找到问题的关键以及在运行时分析的你应用是非常重要的。但是请注意，包含太多log信息可能会对你的应用的性能和存储有不好的影响。确保你在合适的位置记录真正有用的信息，并且并不会因为不重要的信息拖慢你的应用。`bunyan`一个很棒的功能是你可以在生产线上按需读取调试层的log，而不用重启进程。

###bunyan
`Bunyan`是一个简单明了的Nodejs应用log库。Bunyan的输出是按一定规则的json，这使得你可以很方便的使用unix命令如`grep`和`sed`，或它自己的命令行工具，或[json cli utility](https://www.npmjs.com/package/json)来操控它的输出。

Bunyan同样内置支持动态跟踪,它允许你针对现有的目标保持现有的日志等级（比如`INFO()`方法），但在运行时启动一个更详细的级别(比如`TRACE`方法)，然后通知用户端这些log入口，从而不会填满你的磁盘。DTrace在运行时使用非常安全，如果有更高级别的日志等级会导致对你的系统有不好的影响，DTrace会在影响发生前退出。

换言之，如你已经配置了在你的应用中记录的日志等级，但是你的进程在运行时出错了，而你期望找到更多信息却不需要重新启动服务或者增加log的存储。使用bunyan和DTrace吧，它会让你的进程在运行时向你发送你感兴趣的log日志等级。


```javascript
# bunyan -p *
[2013-09-27T18:16:19.679Z] DEBUG: test/1076 on sharptooth.local: something went wrong
```


[点此阅读更多关于运行时日志记录的信息](http://www.joyent.com/blog/node-js-in-production-runtime-log-snooping)