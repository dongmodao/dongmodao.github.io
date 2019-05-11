---
title: 'C# 中的委托与事件'
date: 2017-05-28 01:06:16
tags: [C#, 委托, 事件]
categories: [技术]
---

![](C-中的委托与事件\water.jpg)
## 委托与事件

委托和事件是 C# 中一个比较重要的部分。最近小组作业中使用到了它们，今天自己写的时候遇到了小问题，于是也就稍微探究了一下。
<!-- more -->

**实现的基本方法**
``` csharp
// (1) 定义委托
internal delegate void myHandler(int a, int b);
// (2) 定义事件
internal event myHandler myevent;
// (3) 注册事件
myevent += myFunction;
// (4) 实现方法
void myFunction(int a, int b)
{...}
```

**个人理解**

委托相当于获得了一个函数的指针。可以在本类中调用其他类的函数，只要这个委托可以在其他类中注册；同时，在实现上，这个所谓的其他类的参数也可以被使用，这也是委托和事件的方便之处。
也就是说该类的持有者可以实现该类的事件，并在事件中使用持有者类的对象，在该类中调用该事件则会执行持有者类中的事件。

![](C-中的委托与事件\Csharp_delegate_event.png)

如上图，在 Fields 类中定义了委托 FieldDeleteHandle(object sender, Field field) 以及事件 FieldDeleteHandle FieldDelete，在持有者 Dataset 类中注册并实现了该事件，其中调用的 DataSet 类的其他成员，这些成员并不是 Fields 类中的。

所以，如果某个类改变后要对另外一个类有某种影响，则可以通过事件已经两个对象的公共持有者进行处理，子类通知持有者类，由持有者类对另外的子类进行处理。

本文最初是收录在自己的学习笔记关于 C# 部分内容的，只是今天突然所感而写。好久没更新了，虽然这篇文章很短，而且还有些许不成熟之处，但是还是发一下，至少感觉到学到了一点东西的。

懒癌病发！！！

不想写作业！！！

期末更懒。。。。

就这样，没了。

<center> --- end --- </center>
