---
title: 'Android: 自定义 View'
date: 2018-06-07 23:21:31
tags: [Android]
categories: [技术]
---
<center>
Author: dongmodao
</center>

---

![](/Android-Making-new-view/index.jpg)

## 自定义控件

今天来学习以下自定义控件的绘制流程，使用的是一个在 GitHub 上开源的项目：雷达图扫描效果，进行了练手。本文说来算是一个简单版本的总结，内容主要来源见文末的参考文献。

### 基本流程

1. 继承自 View 或者 View 的子类
 至少使用一个构造函数，包含 Context 和 AttributeSet 对象，作用为允许 layout editor 创建并编辑 View 实例。<!-- more -->
2. 定义自定义属性
 + 在资源标签下定义自设的属性，在 res/values/attrs.xml 中进行定义
    ``` xml
    <resources>
        <declare-styleable name="ViewName">
            <attr name="attr_name" format="data_type" />
            <attr name="position" format="enum">
                <enum name="left" value="0" />
                <enum name="right" value="1" />
            </attr>

        </declare-styleable>
    </resources>
    ```
 + 在 layout xml 中指定属性值，在引用自定义 View 时，需要指定命名空间，如 xmlns:custom="http://schemas.android.com/apk/res/{View_package}"
 + 在运行是获取属性值
 + 应用属性值到 View 中
3. 使用自定义属性
 View 被创建时，使用构造器时，从 xml 中读取，保存到 AttributeSet 中，可从中读取，弊端在于拥有属性的资源没有经过解析，Styles 并没有运用上。不能确定值的类型。所以使用 obtainStyleAttributes() 来获取属性值，方法传递一个 TypedArray 对象，间接引用并经过 Styles 应用的。如：
 ``` java
 TypedArray a = context.getTheme().obtainStyleAttributes(attrs, R.styleable.ViewName, 0, 0);
 mAttr_1 = a.getBoolean(R.styleable.attr_name, default_value);
 a.recycle();   // 共享资源对象要进行回收
 ```
4. 添加属性和事件
 使用合适的 getter 和 setter 方法。当 View 的内容发生变化时，需要调用 invalidate() 和 requestLayout() 方法来进行内容和大小的处理，没有则容易报错。自定义 View 需要能够响应事件的监听器，可自己设定事件来通知监听器。暴露属性和事件时非常重要的。

5. 重写 onMeasure()
 这个方法用于测量和设置控件的大小。使用 setMeasuredDimension(width, height) 设置大小，其中 width 和height 则需要计算出来。

6. 重写 onDraw()
 参数为一个 Canvas 对象，该对象定义了绘制图像图形的方法，绘制之前需要创建一个 Paint 对象。其中 Canvas 用于定义绘制什么，Paint 定义如何绘制。如使用 canvas.drawCircle() 的方法进行画圆，其中参数中使用了 Paint 的实例。当需要重绘时需要调用 invalidate() 方法，更改大小时需要调用 requestLayout() 方法。


### 总结

 总的来说，自定义控件要经过以下几个步骤：
  1. 新建继承自 View 或其子类的 Class，并根据需要实现带参数的几种构造函数，构造的同时可能使用 ``TypedArray t = context.obtainStyleAttributes(attrs, R.styleabe.ViewName);`` 获取从布局 xml 中获取属性值。可以初始化画布 Canvas 和画笔 Paint。
  2. 在资源标签中定义本 View 自定义的属性，设置类型。
  3. 重写 onMeasure() 方法，设置控件的尺寸。
  4. 重写 onDraw() 方法，绘制控件，如需要数据，则提供获取数据的方式。
  5. 更改内容或者大小时，需要调用 invalidate() 或 requestLayout() 方法。
  6. 设置监听、事件


参考：
 1. [自定义 View][1]
 2. [Android自定义View 雷达扫描效果][2]


[1]:http://hukai.me/android-training-course-in-chinese/ui/custom-view/index.html
[2]:https://github.com/donkingliang/RadarView


<center> --- end --- </center>

---
