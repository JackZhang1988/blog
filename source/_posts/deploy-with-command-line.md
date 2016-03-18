---
title:  "使用命令行一键发布到各个环境的方法"
date:   2015-12-11 10:39:34
description: 使用命令行一键发布到各个环境的方法
banner: http://swiftcafe.io/images/iterm/1.png
categories: 项目管理优化
---

由于2008发布平台由于多人使用时会造成冲突，而且一旦出问题，2008平台在调试修复上都十分困难。

重构后的项目我们都是全量发布，所以我们可以直接在本地build然后直接发布到各个测试环境中。

我写了两个脚本，帮助大家实现一键发布到各个环境的功能。

使用方法：

如果要发布测试环境，可以仅使用fabu.exp, 如果要发布61或线上换，需要将publish.exp放到你的堡垒机的根目录下。

将fabu.exp 文件放入指定目录下（比如我放在/usr/local/bin下），同时设置你的堡垒机用户名和密码。

![配置fabu.exp](/img/fabu1.png)


在想要发布的项目中执行grunt命令，然后在项目根目录下运行expect yourpath/fabu.exp + 测试环境号，就可以实现自动发布到指定测试环境。

比如，我要app项目发布app项目到test19环境，首先我在app项目根目录运行grunt test19 (localConfig.js 已经配置好相应命令，直接运行grunt + 目标服务器就可以，同时保证grunt build后的文件在dist目录下，并且p5、H5或static文件夹在dist目录下)，
运行： expect /usr/local/bin/fabu.exp 19  （19表示发布到test19环境）
运行后dist目录下的文件会自动同步到test19环境上。

发布61和线上环境：

由于发布61和上线环境需要在跳板机scp时输入密码，所以要在跳板机上安装另一条脚本，publish.exp, 把publish.exp放到跳板机根目录下。

![配置publish.exp](/img/fabu2.png)

同样运行发布测试环境的步骤，只是发布到61环境，发布参数为pre，上线环境为pro.

expect /usr/local/bin/fabu.exp pre   //61环境
expect /usr/local/bin/fabu.exp pro   //线上环境，（前提是保证项目目录内weidian文件夹下有打好的压缩包）

(如果遇到expect publish.exp commend not found等类似的错误，建议修改ssh配置文件： /etc/ssh/sshd_config，将PermitUserEnvironment设置为yes，然后重启ssh：/etc/init.d/ssh restart)

以上就是使用命令行一键发布到各个测试环境的步骤。
