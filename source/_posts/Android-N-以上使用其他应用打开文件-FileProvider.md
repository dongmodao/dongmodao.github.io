---
title: Android N 以上使用其他应用打开文件 FileProvider
date: 2018-11-24 14:59:31
tags: [Android]
categories: [技术]
---
<center>
Author: dongmodao
</center>

---

![](Android-N-以上使用其他应用打开文件-FileProvider/index.jpg)

  又是好长一段时间没有写东西了，说忙到没有时间写东西，这个说法在任何时候都是借口。总的来说还是没有写，真的要写的话还是有时间来写的，不说成为一个高产的人吧，但是稍微写一些的时间还是有的，再不济也可以分开到很多天再来写的吧。所以说这到头来还是个人的问题，没有主动有意识的去写这些东西，没有有意识的去学习去分享。这也在一定程度上说自己这段时间主动学习的时间少了。无论如何，还是要好好学习的，虽然已经不再学校，但从另一个角度来说自己应该更能明白知识的重要性。所以以后要多加注意主动学习。<!-- more -->

### 前言
  Android 在很多时候需要使用本地的文件，除了需要使用到读写外部存储文件权限之外，有时候还需要使用到内容提供器：ContentProvider。说到这里我真的感叹 Android 开发的混乱，需要使用到各种适配。比如 Android 随着版本的变化带来的 API 的变化，不同生产商自定义的 Android 的功能，不同手机不同屏幕尺寸的适配。总之这就带来了很多问题，原本在这个手机上运行好好的程序到另一个手机上却是 cash 了，十分的蛋疼。这次要说的是对于文件的处理差异，主要涉及的是文件 Uri 的处理。此处以 Android N 为分界，SDK_INT < N 归为一种情况，另外是一种情况。

### API < 24
 ``Uri uri = Uri.fromFile(file)``

 在 Android N 也就是 Android 7.0 之前使用上面这个方法是完全没有问题的，比如
``` java
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    File file = new File(path);     // path 是外部存储中的一个图片的路径
    Uri uri = Uri.fromFile(file);
    intent.setDataAndType(uri, "image/*");
    startActivity(Intent.createChooser(intent, null));

```
这个方法可以调用系统中支持打开图片的应用让用户选择并打开对应的图片，而没有任何问题。当时事情到了 7.0 之后就不一样了，这个方法会抛出一个 FileUriExposedException 的异常。

### API >= 24
  ``Uri uri = FileProvider.getUriForFile(context, athorty_key, file)``

  从 7.0 之后提高了文件的安全性，就需要用上述的方法来获取文件的 Uri。总共分为这几步。
  1. 在 AndroidManifest.xml 中定义 FileProvider 如下
    ``` xml
    <provider
        android:name="android.support.v4.content.FileProvider"
        android:authorities="${applicationId}.fileprovider"
        android:exported="false"
        android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/provider_paths" />
    </provider>
    ```
    其中 name, exported, grantUriPermissions 的值不能改变，authorities 则是在使用 getUriForFile() 方法的第二个参数，用于标识与验证。
  2. 添加 @xml/provider_paths 资源如下
    ``` xml
    <?xml version="1.0" encoding="utf-8"?>
    <paths>
        <external-path name="external_files" path="."/>
    </paths>
    ```
    其中 name 的值会在构成的 uri 中出现，用于隐藏实际的路径，增加安全性；path 则表示文件的读取路径，此处是表示外部存储中的所有文件路径，当然你可以指定为某一个文件夹，比如"image/",则只有这个文件夹下的文件能正常访问，其他的文件夹不行；至于 ``external-path`` 标签则是表示这个提供的是外部存储的读取，如果你需要读取缓存路径或者其他的路径的文件的 uri 则必须要添加对应的标签值。有哪些标签可以去官方看。

  3. 使用  Uri uri = FileProvider.getUriForFile(context, athorty_key, file); 把文件转换成 Uri。如
    ``` java
    File imagePath = new File(Context.getFilesDir(), "images");
    File newFile = new File(imagePath, "default_image.jpg");
    Uri contentUri = getUriForFile(getContext(), "com.mydomain.fileprovider", newFile);   // 官方示例
    ```
  4. 如果分享到其他应用，还需要进行授权。需要给目标应用添加读取 uri 的权限。
    + 使用  Context.grantUriPermission(package, Uri, mode_flags) 给特定的应用包添加读写权限，mode_flags 包括 FLAG_GRANT_READ_URI_PERMISSION 和 FLAG_GRANT_WRITE_URI_PERMISSION，可以都添加，分别是读和写 Uri 的权限。授权期限到重启设备或者使用 revokeUriPermission() 取消权限为止。
    + 除上面的方法之外，也可以使用在自己的要使用的 Intent 中加入这个权限，比如有一个 Intent 为 intent，调用 intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); 则使用该 intent 的应该可以读取 uri，但下次还需要添加才行。

所以上面的那个分享的方式可以写成如下的形式来调用：
``` java
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    File file = new File(path);     // path 是外部存储中的一个图片的路径
    Uri uri;
    if (Build.VERSION.SDK_INT < Build.VERSION_CODES.N) {
        uri = Uri.fromFile(file);
    } else {
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
        uri = FileProvider.getUriForFile(content, authorities, file);
    }
    intent.setDataAndType(uri, "image/*");
    startActivity(Intent.createChooser(intent, null));

```

### 后记
时隔很久之后才重新写的分享，发现写起来真的废力啊，耗时比预计时间长，自己的打字速度不行啊。总之，以后也要加油啊。本次分享到这里，下一次，写的应该是关于不同 Android 版本的热点和 Wifi 的管理，这里面套路比这个更深啊，汗颜。。。

**链接**

1. [官方对于 FileProvider 的文档](https://developer.android.com/reference/android/support/v4/content/FileProvider)(需要翻墙)


 <center> --- end --- </center>

 ---
