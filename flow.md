# 改动流程
添加新博文，样式等，如下操作：
 1. 依次执行 git add ., git commit -m "...", git push origin hexo 指令将改动推送到远端的 hexo 分支（此时本地的分支为 hexo）
 2. 执行 hexo g -d 发表网站到远端的 master 分支上

更加详细的说明看[使用 hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)中 CrazyMilk 的答案，本博客就是采用这种更新方式。