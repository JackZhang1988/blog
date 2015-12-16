---
title:  "NodeJs生产环境最佳实践-设计篇-代码风格"
date:   2015-01-29 19:18:00
description: NodeJs生产环境最佳实践-设计篇-代码风格
banner: https://www.eduonix.com/blog/wp-content/uploads/2015/11/Node-JS-Coding-Best-Practices-for-Beginners.png
categories: NodeJs生产环境最佳实践
---

[文章译自:https://www.joyent.com/developers/node/design](https://www.joyent.com/developers/node/design)

##代码风格
代码应该遵守什么样的风格很难界定，因为大部分的规定都是片面的。遵守一个能让你自己和你的团队感到舒服的代码风格很重要。而按很多人总结的，有一些规范能帮你在开发node应用时更轻松。


###为你的函数起名
尝试为所有函数命名，即使是小的你不认为值得命名的闭包函数。然而V8引擎会尽最大努力使用脚本名和函数来制定错误的位置。如果这样的话，未命名的函数将很难第一眼区分不同的函数。而最后你所做的只是不断浪费时间在调试错误的函数。

###避免闭包
同样的，考虑把函数的定义从其他函数内部移出来。这会改变你的思考方式从闭包式的到堆栈式的。这种逻辑上小的改变可以帮助你排除很多由闭包引起的无意识的内存泄露问题。

###使用更小的函数
V8运行时是一个很强大的引擎，它可以优化很多的模式，但更小更简洁的函数能让V8运行时更好的内联函数。好的一点是，如果你的函数很小（代码或者实例代码行数不到100），它更能提高代码的可读性和可理解性，从而减少很多维护应用的工作。

###检查编程风格
使用一致的编程风格，并且使用代码审查工具来进一步保持这种风格。我们使用**jsstyle**，一种针对Javascript不同寻常的风格，但是至少这个工具能保证维持这种风格。来自代码风格检查工具的错误提示就像语法错误或单元测试错误一样：构建是被认为是失败的并且需要马上修复。