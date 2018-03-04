---
title: Echarts 的基本使用和 Android 连接 Mysql 数据库
date: 2018-01-29 19:24:46
tags: [Echarts, Mysql]
categories: [技术]
---
<center>
Author: dongmodao
</center>

---
![](Echarts-的基本使用和-Android-连接-Mysql-数据库/tower.jpg)

好久好久没有更新博客了。。。

大学生活中的最后一个假期如期而至，这次回家好像比往常有更多学习的心思，可能时因为下学期准备毕业，准备找工作的原因？好像也不是很强烈。不过，回家一个人也是好无聊的样子，于是今天写了一些东西，虽然都是比较简单的东西吧。<!--more-->

进入了少网络的环境下，更容易专注于做一些事情。今天下午做了一些在 Android 中使用 Echarts 和连接 Mysql 数据库的尝试。其实也是之前答应了朋友要和他一起做一个 App 的原因。趁着无聊就来学习一下。

## 使用 Echarts
### Echarts 是什么
Echarts 是由百度公司开发的一个轻量级的可以轻松实现直观、可交互、高度自定义图表和可视化的 canvas 库，基于 zrender 和轻量级的 JavaScript 语言。其官方网址为 [Echarts](https://echarts.baidu.com)，在这里就不再多说，官网比我说的更加详细。

### 使用 Echarts (附加 JS 和 Android 应用的交互)
先从官网中下载最新的版本，[戳这里](https://echarts.baidu.com/tutorial.html)，同时还有如何操作的介绍。随后引入 Echarts，使用 echarts.init 方法初始化一个实例，使用 setOption 方法生成图表。如下是一个简单的示例：
``` html
<!DOCTYPE html>
<html style="height: 100%">
<head>
    <meta charset="utf-8">
</head>
<body style="height: 100%; margin: 0">

<div id="container" style="height: 100%"></div>

<script type="text/javascript" src="http://echarts.baidu.com/gallery/vendors/echarts/echarts.min.js"></script>
<script type="text/javascript">
       var dom = document.getElementById("container");
       var myChart = echarts.init(dom);
       var option = {
              title:{
                     text:'入门示例'
              },
              tooltip:{},
              legend:{
                     data:['销量']
              },
              xAxis:{
                     data:["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
              },
              yAxis:{},
              series:[{
                     name:'销量',
                     type:'bar',
                     data:[5, 20, 36, 10, 15, 20]
              }]
       };
       myChart.setOption(option);
</script>
</body>
</html>
```

概括来说就是先获取一个实例，生成一个对应的 option，使用 setOption 方法，成图，over！

今天还涉及一个部分就是动态加载数据的问题，需要在手机上点击之后更改图表，所以使用一个 JavaScript 函数来实现，在上述代码中自定义的 JavaScript 脚本部分加入如下代码：
``` javascript
       	function calljs(data){		// 函数记得加 function 关键字
       	    var dom = document.getElementById("container");
       		var myChart = echarts.init(dom);
       		// 获取横纵数据
       		var xList = data.map(function (item) {
       		    return item[0];
       		});
       		var valueList = data.map(function (item) {
       		    return item[1];
       		});
       	    var option = {
       		title:{
       			text:'入门示例'
       		},
       		tooltip:{},
       		legend:{
       			data:['销量']
       		},
       		xAxis:{
       			data:xList
       		},
       		yAxis:{},
       		series:[{
       			name:'销量',
       			type:'bar',
       			data:valueList
       		}]
       	};
       	myChart.setOption(option);
       	}

```
然后在 Android App 中加入如下代码：
``` java
    String data = "[[\"衬衫\",5],[\"羊毛衫\",20],[\"雪纺衫\",36],[\"裤子\",10],[\"高跟鞋\",15],[\"袜子\",20]]";
    webView.loadUrl(MessageFormat.format("javascript:calljs({0})", data));	// 以参数 data 执行脚本方法 calljs。不必新开线程，webView 自动在子线程中使用
```
以上是我今天需要使用的部分，关于 Android 和 JS 的交互，可以移步[Android：你要的 WebView 与 JS 交互方式都在这里了](http://blog.csdn.net/carson_ho/article/details/64904691)查看。

## 连接 Mysql 数据库
下午的另一个事情就是连接 MySQL 数据库，并读取信息。实现步骤如下：
 1. 下载 MySQL 和 Java 连接器的包，如我使用的是 mysql-connector-java-5.1.28.jar。
 2. 主要代码如下，如果提示使用 try/catch 的，请自行添加，此处把流程过一遍：
``` java
String DRIVER = "com.mysql.jdbc.Driver";
String USER = "username";
String PASS = "password";
String URL = "jdbc:mysql://123.207.110.110:3306/yourDBName";
String sql = "select * from ......"
Class.forName(DRIVER)
Connection connection = DriverManager.getConnection(URL, USER, PASS);
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(sql);
while(resultSet.next())
{
	String param1 = resultSet.getString(1);		// 读取记录，记录初始字段从 1 开始，不是从 0 开始
	// Todo: anything...
}
resultSet.close();
statement.close();
connection.close();
```
上面的操作，有比较多的是要在子线程中实现的，如 getConnection 和 executeQuery。另外，要在读取结束之后再关闭连接，注意顺序，否之会报错。反正，报错了看是什么错误，解决一下应该都没问题。

### 后记
这其实是在假期写的文章，结果没有后续，留到现在才发布...[2018年3月4日 09点32分]

<center> --- end --- </center>

---

