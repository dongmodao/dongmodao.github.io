---
title: Android Studio 中常用的一些布局(一)
date: 2017-03-31 10:58:26
tags: [Android Studio, 布局]
categories: [技术]
---

![](Android-Studio-中常用的一些布局/Beach.jpg)

## 写在前面
对于应用开发者来说，让应用拥有一个漂亮的外观往往能让用户有一个很好的印象。而上一星期刚好看了郭神的《第一行代码》，觉得其中介绍的布局方式使我受益良多，所以今天就介绍一些在 Android Studio 中的布局。这些是目前我所遇到了比较合适于简单开发的布局，以此记之。如果想要更详细的了解，建议阅读郭霖大神的 《第一行代码(第二版)》。
<!--more-->
## 正式开始

1\. Toolbar
众所周知，Toolbar 是 Android App 上端显示的内容，当我们新建一个 AS 项目时，AS 自动给我们添加了 ActionBar，但是这个 Toolbar 并不总能满足我们的需求，有时候就需要自定义的 Toolbar。因此我们就需要先修改为没有自带的 Toolbar 的主题。 下面是主要代码:
修改 res/values/styles.xml 中的相应属性如下:
``` xml
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
```
此时达到的效果就是隐藏了原本的 ActionBar， 接下来使用 Toolbar 代替它。
修改 activity_main.xml 如下：
``` xml
<!-- 引用 app -->
xmlns:app="http://schemas.android.com/apk/res-auto"
<android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="?attr/colorPrimary"
                android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
                app:layout_scrollFlags="scroll|enterAlways|snap" />

```
接着在 MainActivity 中的 onCreate 方法中 Toolbar 加入到 SupportActionBar 中
``` java
Toolbar toolbar = (Toolbar)findViewById(R.id.toolbar);
setSupportActionBar(toolbar);
```
此时就可以看到和 ActionBar 完全相同的效果。我们接下来对它继续完善。
右击 res → New → Directory， 创建 menu 文件夹。 在其中新建一个 toolbar.xml， 添加如下代码：
``` xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/backup"
        android:icon="@drawable/ic_backup"
        android:title="Backup"
        app:showAsAction="always"/>
    <item
        android:id="@+id/delete"
        android:icon="@drawable/ic_delete"
        android:title="Delete"
        app:showAsAction="ifRoom"/>
    <item
        android:id="@+id/settings"
        android:icon="@drawable/ic_settings"
        android:title="Settings"
        app:showAsAction="never"/>
</menu>
```
代码中的图片自行查找并添加，此处推荐 Google 使用的 [Material Design Icon](https://material.io/icons/)，这个网站上有非常丰富的标准图标资源，而且提供多种分辨率的图片，能满足很多开发的需要了。
此时已经可以显示了，接下来进行点击事件的模拟。在 MainActivity 中加入
``` java
   public boolean onCreateOptionsMenu(Menu menu){
        getMenuInflater().inflate(R.menu.toolbar, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item){
        switch (item.getItemId()){
            case R.id.backup:
                Toast.makeText(this, "You clicked Backup", Toast.LENGTH_SHORT).show();
                break;
            case R.id.delete:
                Toast.makeText(this, "You clicked Delete", Toast.LENGTH_SHORT).show();
                break;
            case R.id.settings:
                Toast.makeText(this, "You clicked Settings", Toast.LENGTH_SHORT).show();
                break;
            default:
        }
        return true;
    }
```

以上就完成了点击事件的模拟。

##### 由于篇幅原因以及肚子饿了(最大原因)，今天暂时到这里，后面会补上其他内容。下一次讲滑动菜单。

> ### 如果想要了解的更仔细，请阅读郭霖大神著作 《第一行代码（第二版）》。


<center> --- end --- </center>

---
