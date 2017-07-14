---
layout: post
title: "kafka configuration memo"
date: 2017-07-15 00:31:44 +0800
comments: true
categories:
---

有时候还真是不能过度透支自己，最近重新找回了编程的乐趣, 想把丢了好久的blog捡起来, 不知道写些什么, 就从一些简单的memo开始写起吧。

最近配置kafka遇到最困惑的问题就是关于两个listener, 不知道为什么导致客户端连接不上, 记录下.
```
# kafka真正监听的地址, 只这样配置的话本机连接是不会有问题的
listeners=PLAINTEXT://:9092

# 这个是设置在zookeeper里面让客户端真正连接的地址，如果像以下一样留空，或者沿用listeners的配置
# 将会导致客户端访问hostname:9092，所以连接kafka出错
advertised.listeners=PLAINTEXT://:9092

# 将ip段配置成如下kafka机器的域名或者外网ip就可以正常访问了
advertised.listeners=PLAINTEXT://endwan.com:9092
```
