---
layout: post
title: "C++任意指针转换为object指针"
date: 2013-10-31 22:13
comments: true
categories: C++ 指针
---

最近碰到一个C++指针转换的问题，发现颇有意思，值得记录一下。
有一个foo class如下。
``` cpp
class foo
{
public:
    int get_foo(int val);

    int m_foo;
};

int foo::get_foo(int val)
{
    int a = 345;
    int rc = val + a;
    cout << rc << endl;
    return rc;
}
```
测试如下
``` cpp
int main()
{
    int* val = new int[100];

    foo* foo_ptr;
    foo_ptr = (foo*)val;
    foo_ptr->get_foo(100);
    foo_ptr->m_foo = 101;
    cout << foo_ptr->get_m_foo() << endl;

    return 0;
}
```
以上代码编译后运行竟然完全正常，太奇怪了！强制将任意一个指针转换成对象指针，然后调用它的方法和成员变量竟然可以正常执行。为此我去[stackoverflow](http://stackoverflow.com/questions/19688638/convert-an-int-pointer-to-any-object-pointer-then-call-its-methods-it-works)求助了一下，再经过和同事讨论，得出了如下结果：
    
* 尽管这个指针实际不是指向的一个foo对象，但是当编译器把它当作一个foo对象来处理时，调用他的方法其实是调用foo class的方法，属于所有object都公用，如果该方法不是个虚函数也没有用到成员变量，这样做应该是没有问题的。

* 如果Foo class里有虚函数，那么foo对象里会包含一个指向虚函数表的指针[^1]，调用这个虚函数时会先用这个指针找到vtable，这里foo对象的虚函数表指针应该是val指针指向内存的后面某个位置，而这个位置的数据是不确定的，有很大可能指向的是一个非法访问地址，导致程序崩溃。

* 访问成员变量在上面行得通是因为我为val分配了一段内存，foo对象访问成员m_foo其实是访问val这段内存的某个位置，修改这段内存不会有什么问题。如果val这样定义：`int *val;`, 那foo访问成员时行为就很不确定，运气不好，访问到允许的地址，修改了那里的值，问题过很久以后才显现出来。运气好，访问到非法地址，程序立即崩溃，这时出问题的地方离产生问题的地方很近。

最后引用下stackoverflow里很有意思的一个回复 :)

> C++ lets you get away with doing things that aren't allowed, under the name undefined behaviour. Basically, it means "if you do something stupid, we won't stop you, but absolutely anything could happen"! 

[^1]: http://www.cppblog.com/ElliottZC/archive/2007/07/20/28416.html "C++多态性：虚函数的调用原理"

