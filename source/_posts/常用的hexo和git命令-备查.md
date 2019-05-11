---
title: 常用的hexo和git命令
date: 2017-03-10 10:03:04
tags: [hexo,git]
categories: [技术]
---

![](常用的hexo和git命令-备查/beautyhouse.jpg)

### 写在前面

对于使用hexo和git搭建的博客的人来说，熟悉一些常用的hexo和git命令是非常必要的，以下是我
现阶段经常使用到的一些命令，忘记的时候可以打开博客查看一下。我会把自己在学习过程中所遇
到的命令更新到这里，长时间维持更新。
<!--more-->
### 常用的hexo命令
1\. 生成新页面
首先，安装好hexo和git之后要新建一个新的页面，这时候我们需要运行

> hexo n "new_post_title"  //输入新的页面的名称

于是hexo就会在source/\_post/文件夹下生成名为new_post_title的.md文件，我们可以使用支持
Markdown语法的文本编辑器如sublime Text系列、Atom等打开.md文件编写我们的文件内容了。

2\. 在本地进行预览
当我们编写好了我们的.md文件，我们可能想在本地进行一下预览，看看效果，于是我们可以使用：
> hexo s  //等待完成之后可以在本机的4000端口查看我们的博客效果
> heo s --debug  //使用debug模式，我们可以通过控制台看到浏览器向我们的服务器获取了那些
内容，可以知道我们项目存在那些问题

3\. 生成正式文件
经过本地预览没有问题之后，我们基本上就可以生成用于发布的文件了，使用：
> hexo g  //生产项目的正式文件，用于发布，默认保存在public文件夹之下，可以通过修改配置
使得生成在github.io的文件目录下。
> hexo g --debug  //和上面的相似，可以看到生成的过程

4\. 发布到github服务器
经过上述的步骤之后，我们基本已经完成了所有的内容了，剩下的就是将本地文件上传到github上面，就可以在任意一台电脑对博客进行访问了。
> hexo deploy //发布到服务器
> hexo deploy --debug -m [message]  //如果使用这个命令就进入debug模式并且commit的信息为message

### 常用git命令
> git init //在当前文件夹生成一个代码库
> git add [filename]  //把filename文件加入缓冲区中，等待提交
> git add .  //把当前目录下所有文件都放入缓冲区中，等待提交
> git commit -m [message]  //生成提交，提交信息为commit，并清空缓冲区内容

### 特别：
> 用hexo搭建的博客使用图片时，需要设置配置文件.config.yml文件中的
> post_asset_folder: true
此时在source/\_post/中生成和页面同名的文件夹，用于存储图片等资源，在markdown中调用时写成(文件夹名/图片名)即可，如果是对于早前没有生成文件夹的压面需要手动生成了。

----

### 最后

更多关于 Git 的基础知识介绍，可以查看这篇博客[一个小时学会 Git](1)，介绍的十分详细，非常适合初级学者。

这应该是本文的最后一次更新，借用了他人的劳动成果，自己整理起来可能没有上述链接文章这么好。[2017年9月10日09:53:37]

[1]: http://www.cnblogs.com/best/p/7474442.html "Learn Git in one hour"
<center> --- end --- </center>

---
