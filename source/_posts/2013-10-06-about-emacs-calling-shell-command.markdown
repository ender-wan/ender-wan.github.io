---
layout: post
title: "Emacs执行shell命令的问题"
date: 2013-10-06 23:21
comments: true
categories: Emacs
---


今天在配置emacs的[markdown-mode](http://jblevins.org/projects/markdown-mode/)时调用`C-c C-c p`(转换成html然后在浏览器里打开预览)一直提示**/bin/bash: markdown: command not found**，它需要一个shell命令能将markdown转换成html，这里我用到了一个python的[markdown](https://pypi.python.org/pypi/Markdown)库。

安装完在我的机器上是这个路径：
``/Library/Frameworks/Python.framework/Versions/Current/bin/markdown_py``

接下来在/usr/bin创建一个markdown的软链接到markdown_py
``` bash
sudo ln -s /Library/Frameworks/Python.framework/Versions/Current/bin/markdown_py /usr/bin/markdown
```

做完上面这些后在emacs里调用`C-c C-c p`还是提示同样的错误，但是markdown在我的本地终端可以执行。Google了下之后发现这可能是由于emacs在启动一个shell的时候不会去读取.bash_profile，这个文件只是在登陆的时候被读取一次。而我在.bashrc里没有做任何关于环境变量的设置，所以/usr/bin没有被加到PATH里去，导致bash找不到markdown这条命令。

等我把下面这句加到.bashrc
``` bash
if [ -f ~/.bash_profile ]; then
. ~/.bash_profile
fi
```

问题还是没有解决，提示同样的错误。继续Google, 最后终于在[这里](http://stackoverflow.com/questions/4393628/emacs-shell-command-not-found)找到答案。emacs直接执行shell命令默认是非交互式的，所以不会去读取.bashrc文件的。解决的办法就是在.emacs初始化文件里加上这句。
``` cl
(setq shell-command-switch "-ic")
```

`-i`指定bash启动是交互式的, 然后才会去读取.bashrc。或者你也可以配置**BASH_ENV**, 单独为非交互式的shell指定启动脚本。`-c`表示命令读取来自字符串。

以上统统搞定终于写出了我的第一篇博客！哈哈
