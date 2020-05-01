---
title: C++ 多态性与虚函数
date: 2017-06-08 01:32:51
tags: [C++, 多态性, 虚函数]
categories: [技术]
---

![夜空与宁静](C-多态性与虚函数\stars in night sky.jpg)

**写在前面：**最近快期末考了，事情真的好多，作业总也写不完的感觉，好久没更新了，只能把自己的学习笔记中一些自己亲自测试过的东西搬到这边来了。这次写的是 C++ 中的多态性和虚函数，也是最近作业中需要使用的东西。知识这种东西吧，只有自己亲身体会以及实践后才能比较清楚，写笔记和不写笔记真的不一样。话不多说，回归主题！
<!--more-->

## 多态性

多态性是指一个事物具有多个状态。重载函数拥有同一个函数名，不同的函数体，调用同一个函数，分别实现不同的函数体的功能，这也是多态的一个体现。不同的对象，对于同一个消息（函数），有不同的实现行为（功能），这也是多态的一种表现。

多态性从实现上分为静态多态性以及动态多态性。

静态多态性也称为编译时的多态性，在编译时，已经根据变量的类型和个数或者对象所属的类型确定执行什么操作，类似的就是函数的重载，在不同类型或者相同类型中同名函数的调用，除此之外，还有运算符的重载，也是静态多态性的一种。

动态多态性也称为运行时的多态性，在编译时无法知道要处理的是什么操作，只能在程序的执行过程中根据具体的情况来动态的判断要执行的操作。这样多态性一般通过继承和虚函数来实现。

单单使用继承，子类中如果需要使用和父类中同名的函数，则需要重新定义，并且新定义的函数会覆盖父类的函数，父类函数被屏蔽。也即，派生类的对象是不能访问从基类中继承来的同名函数，这也是虚函数的由来，通过虚函数就能动态解决这问题。

先看一下使用继承而不使用虚函数带来的问题。

**testcode.h**
``` cpp
#ifndef TESTCODE_H_	//用于判断是否已经调用了该头文件，避免重复调用错误 #define 保护
#define TESTCODE_H_

class Point
{
protected:
double x;
double y;
public:
Point(double a, double b);	//声明构造函数
double GetX();	//声明各类函数
double GetY();
void Print();
};

class PointD : public Point
{
protected:
double z;
public:
PointD(double a, double b, double c);
double GetZ();
void Print();
};
#endif	//结束 ifndef
```

**testcode.cpp**
``` cpp
#include "testcode.h"
#include<iostream>
using namespace std;

Point::Point(double a, double b)
{
this->x = a;
this->y = b;
}

double Point::GetX()
{
return this->x;
}

double Point::GetY()
{
return this->y;
}

void Point::Print()
{
cout << "x = " << this->x << "\ty = " << this->y << endl;
}

PointD::PointD(double a, double b, double c) :Point(a, b)
{
this->z = c;
}

double PointD::GetZ()
{
return this->z;
}

void PointD::Print()
{
cout << "x = " << this->x << "\ty = " << this->y << "\tz = " << this->z << endl;
}
```

**程序入口：test.cpp**
``` cpp
#include<iostream>
#include "testcode.h"
using namespace std;

void main(void)
{
Point a(10, 20), *p;
p = &a;
p->Print();
PointD b(10, 20, 30);
p = &b;
p->Print();
system("pause");
}
/*输出结果
x = 10 y = 20
x = 10 y = 20
*/
```

从上面的程序可以看到，尽管使用的是基类的指针类型，但是指针指向派生类的时候也没有执行派生类定义的 Print 函数。这并不是我想要的，我想要的是，当指针指向的是派生类的时候，执行的是派生类定义的函数。要解决这个问题就需要使用虚函数。

## 虚函数

虚函数的出现就是为了实现派生类对象能够调用不同派生层次的同名函数。定义如下：
在基类中：
``` cpp
public:
virtual 函数类型 函数名(参数表){...}
```

运用虚函数解决上述问题。在 ``testcode.h`` 中在 ``void Print()`` 前加上关键字 ``virtual``，表明将把这个函数定义为虚函数，在其后的继承关系中不需要重复添加。只要要求函数名、函数类型、参数列表与基类相同即可，否则渐变成重载。

其他部分不变，而结果则变为了：
``` cpp
/*
x = 10 y = 20
x = 10 y = 20 z = 30
*/
```
符合预期，动态根据指针指向的类型选择执行怎样的操作。成功。

**这个的笔记是根据书籍《程序设计与算法语言——C++ 程序设计基础》中对于虚函数的介绍而修改过来的。有条件可以读一读。**



<center> --- end --- </center>
