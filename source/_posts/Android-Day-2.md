---
title: Android Day 2
date: 2018-06-06 09:45:23
tags: [Android]
categories: [技术]
---
<center>
Author: dongmodao
</center>

---

![](Android-Day-2/index.jpg)

### 基础 UI
#### 自定义控件
 1. 组合/继承控件
  + 写布局 xml 文件
  + 使用 LayoutInflater.from(Context).inflate(layout_id, parent_layout[this])
  + 重写事件等
 2. 完全自定义 View<!-- more -->


#### 重要控件
 1. ListView
  + 生成数据数组
  + 创建内容类型，设置 get/set
  + 创建每个子项的布局 layout
  + 创建自定义 Adapter (可继承于自带 Adapter，如 ArrayAdapter<Bean>)
    + 实现构造函数
    + 重写 getView 方法，加载布局，设置控件内容，返回 View
    + 重用缓存 convertView 参数。getView 中判断是否为 null，是则加载布局，否则操作返回
    + 使用 ViewHolder，将布局中的 View 交予其管理。可结合 View 的 set/getTag 存储/管理/重用 ViewHolder。
  + setOnItemClickListener(new AdapterView.OnItemClickListener(){}),子项点击监听

 2. RecyclerView
  + 加入引用 "com.android.support:recyclerview-v7:{version}",引入布局
  + [基本步骤与 ListView 相似，不提] 创建自定义适配器 BeanAdapter：
    + 继承与 RecyclerView.Adapter<BeanAdapter.ViewHolder>，内部类 ViewHolder
    + ViewHolder extends RecyclerView.ViewHolder。管理/绑定子项 View，构造时绑定 view_id
    + 构造函数引入数据
    + 重写 onCreateViewHolder()，加载布局，设定子 View 监听等，返回 ViewHolder
    + 重写 onBindViewHolder()，子项绑定 ViewHolder，所以获取 Bean 实例，通过 ViewHolder 管理 View
    + 重写 getItemCount()，返回数据总数量

  + 使用 [Linear/StaggeredGrid/...]LayoutManager 管理 RecyclerView 的子项布局方式，使用setLayoutManager()，可对 layoutManager 进行各项定义
  + setAdapter() 设置适配器

9-Patch 图设定：[创建可调整大小的位图（9-Patch 文件）][1]


[1]:https://developer.android.com/studio/write/draw9patch?hl=zh-cn
