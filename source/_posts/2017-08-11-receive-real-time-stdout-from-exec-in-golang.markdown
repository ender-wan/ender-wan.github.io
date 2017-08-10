---
layout: post
title: "receive real time stdout from exec in golang"
date: 2017-08-11 00:55:44 +0800
comments: true
categories:
---

最近在go里面用exec启动另外一个程序并获取它的stdout时出现了一个比较奇怪的问题。

stdpipe里的数据不是按行传过来，而是过了很久才会刷一大批数据出来，应该是其中存在某种缓冲机制。

跟踪了go标准库的代码没有发现会导致这种现象，查看了管道的缓冲机制好像也没问题，最后查到应该是跟该程序的缓冲io相关.

附上代码, 例子里运行的ping不会出现上面提到的问题
```
cmd := exec.Command("ping")
out, err := cmd.StdoutPipe()
if err != nil {
    ewlog.Error(err)
    return err
}

err = cmd.Start()
if err != nil {
    ewlog.Error(err)
    return err
}

scanner := bufio.NewScanner(out)

for scanner.Scan() {
    line := scanner.Text()
    ewlog.Debug(line)
}
```
