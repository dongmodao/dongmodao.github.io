---
title: 使用 LitePal 操作数据库
date: 2017-09-17 02:06:23
tags: [Android]
categories: [技术]
---
<center>
Author: dongmodao
</center>

![](使用-LitePal-操作数据库/night.jpg)

## 使用 LitePal 操作数据库

简单说说，只要自己明白，会用就好！<!--more-->

### 前记

今天和一个同学聊天，发现我好久更新关于 Android 的文章了，于是在聊天之后，把上一次没看问完的数据持久化看了一遍，虽然没有码代码，但是把基本理论给清理了一下。算是温习以及新学吧。但是。。。啊，我的报告啊，报告没写完就来这里浪？明天爬不起来怎么能写完，说好的不熬夜呢。。。有时候来了心情自己也控制不了。。。于是。。。「2017-09-17 02:32:12」

### 关于 LitePal

LitePal 是一款开源的 Android 数据库框架，采用对象关系映射（ORM：Object Relational Mapping）的模式，对常用数据库功能封装，可以不必编写 SQL 语句就可完成建表和 CRUD。[LitePal 的 GitHub 地址](https://github.com/LitePalFramework/LitePal).

### 配置 LitePal

1. 在依赖 dependencies 闭包中添加依赖。
> compile 'org.litepal.android:core:1.6.0'

2. 配置 app/src/main/assets/litepal.xml 文件。
> &lt;?xml version="1.0" encoding="utf-8"?&gt;
>
>  &lt;litepal&gt;
>
>  &lt;dbname value="BookStore" &gt;&lt;/dbname&gt;
>
>  &lt;version value="1" &gt;&lt;/version&gt;
>
>  &lt;list&gt;
>  &lt;/list&gt;
>
> &lt;/litepal&gt;

 dbname 标签用于指定数据库名，version 标签用于指定版本号，list 标签用于指定所用的映射模型。

3. 配置 AndroidManifest.xml
> 在 application 标签下添加 ``android:name="org.litepal.LitePalApplication"

**对象关系映射（ORM：Object Relational Mapping）**

编程语言是面向对象语言，数据库为关系型数据库，将面向对象的语言与面向关系的数据库之间建立一种映射关系，即为对象关系映射。

使用面向对象的思维来操做数据库，不需要使用 SQL 语句。

### 操作数据库

1. 定义一个类，这个类将和数据库中要建立的表拥有相同的字段，也即类的字段和数据表的列是行对应的。如 Book 类与 Book 表有相同的字段。

2. 将新建类添加到映射模型列表中。在 litepal.xml 文件中的 list 标签添加映射关系入下：&lt;mapping class="com.包名.Book"&gt;&lt;/mapping&gt;，使用完整类名。

3. 使用 ``LitePal.getDatabase();`` 完成创建表操作。

4. 升级数据库版本，修改 litepal.xml 的 version 标签加 1 即可。会自动保留原有数据。

5. **进行 CRUD 操作时，需要使新建类继承 DataSupport 类。**

6. C 操作。添加新数据时，只需 new 一个对象，设置字段值，调用对象的 ``.save()`` 方法即可。

7. U 操作。区分对象是否已经存储，``model.isSaved()`` 可用与判断，对于已经存储的对象，更改其值之后使用对象的 ``.save()`` 更新数据库；无论对象是否存储，使用对象的 ``.updateAll()`` 方法按条件进行更新（是已存储，则修改，否则添加吧）。将字段设置为默认值时需注意，Java 对象初始化时已有默认值，使用 ``.setToDefault()`` 操作。**每新建一个类的对象时就相当于将新建或修改一条数据。**

8. D 操作。调用 ``DataSupport.delete()`` 方法即可。

9. R 操作。使用 ``DataSupport.findAll()`` 方法获得满足条件的新建类的对象的 List。

上述为个人认识，如有差错，可与我联系。学习资料为郭霖大神《第一行代码》的**数据持久化**章节的内容。

<center> --- end --- </center>

---
