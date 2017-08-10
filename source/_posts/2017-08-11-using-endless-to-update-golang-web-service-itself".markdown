---
layout: post
title: "using endless to update golang web service itself"
date: 2017-08-11 01:29:44 +0800
comments: true
categories:
---

[Endless](https://github.com/fvbock/endless)可以为go http server提供不停机重启的能力，很容易集成到自己的web service里面，只需要用它提供的ListenAndServe就好了。
```
err := endless.ListenAndServe("localhost:4242", handler)
```

不停机重启的原理大概是通过接收SIGHUP信号，用exec再启动一个自己并将fd传过去，继续监听然后accept连接。父进程不再接收新的连接，等到处理完所有旧的连接或者设置一段时间超时之后自己终止。

利用endless的特性我们就可以设计出一个自更新的解决方案，无论是通过定时还是提供一个api去触发更新操作。一旦触发更新，就去配置服务器上查询是否有比自身更新的版本，有的话就先将自身备份，然后下载最新版本替换掉自身，最后自己给自己发送SIGHUP信号实现自更新。

当然这其中还会涉及一些其他细节需要处理，比如数据库结构、配置有变化等。
