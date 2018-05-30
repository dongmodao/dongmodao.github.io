---
title: Android Day 1
date: 2018-05-30 23:56:19
tags:[Android]
categories: [技术]
---
<center>
Author: dongmodao
</center>

---


## Android - Day 1

### Activity
一种包含用户界面的组件，主要用于和用户进行交互。

#### Basic

 + extends AppCompatActivity
 + onCreate() // 重写方法
 + setContentView(R.layout.layout_name) //加载布局
 + AndroidManifest, <activity android:name=".Activity_Name">  //注册 
 + finsh()      // 销毁活动
#### Menu
 1. res->New->Directory->menu, New->Menu resource file->file_name;建立menu的xml文件。
 2. Activity 中重写 onCreateOptionsMenu(), getMenuInflater().inflate(menu_xml);
 3. 重写 onOptionsItemSelected()

#### Intent
 1. 显示 Intent
  ``` java
  Intent intent = new Intent(Context, Activity.class);
  startActivity(intent);
  ```
 2. 隐式 Intent
  + 筛选器
  ``` xml
  <intent-filter>
        <action android:name="activity_name.ACTION_START" />
        <category android:name="android.intent.category.DEFAULT" />
  </intent-filter>
  ```
  + startActivity
  ``` java
  Intent intent = new Intent("activity_name.ACTION_START"); // 选择intent
  //intent.addCategory("category_value");
  startActivity(intent);
  ```
  + 用于启动其他应用，网页、电话、其他应用，使用intent.setData()方法，还可以配置data标签。

 3. 传递数据，intent.putExtra("KEY", value);
 4. 返回数据， startActivityForResult(intent, 1);//1 为请求码；在另一个Activity中使用 setResult(RESULT_OK, intent) //RESULT_CANCELED；重写本Activity 的 onActivityResult();


#### 生命周期
返回栈（Back Stack）管理，新开Activity入栈，退出出栈，总是显示栈顶Activity。

 1. 状态：活动（活动）、暂停、停止、销毁共四种状态，对应处于栈顶、非栈顶但可见、非栈顶完全不可见、从栈中移除这四种实质。
 2. 活动周期：完全生存期、可见生存期、前台生存期，对应onCreate()->onDestroy()、onStart()->onStop()、onResume()->onPause()三个过程。
  ```
  + onCreate():活动第一次创建时调用，初始化，加载布局、绑定事件
  + onStart():不可见转为可见状态时调用
  + onResume():活动准备好与用户交互式使用，处于栈顶，运行状态
  + onPause():准备启动或恢复另一活动时使用，用于释放资源、保存数据。要求执行速度快，否则影响新栈顶活动使用
  + onStop():完全不可见时调用，onPause()在部分可见就执行，而onStop()需要完全不可见，如对话框时不执行，onPause()执行
  + onDestroy():活动销毁之前执行，随后销毁。
  + onRestart():停止状态到运行状态时之前调用
  ```
 3. 回收活动的恢复：使用onSaveInstanceState()方法，用于保存数据，键值对方式，传入Buldle对象，使用putString()等Bulder实例的putDataType()方式保存。在onCreate()中检查传入的Budle saveInstanceState()参数是否为null。

#### 启动模式
 1. standard、singleTop、singleTask、singleInstance，在AndroidManifest中``<activity>``标签下使用android:launchMode属性选择。
    + standard：默认启动方式，启动则会在返回栈顶生成新的活动
    + singleTop:栈顶时该活动时不能重新生成，不处于栈顶时可重新生成
    + singleTask：每次启动检查返回栈中是否有，如果有则不启动，而是将其上的活动移除，使用已有实例，没有时新建
    + singleInstance：启用一个新的返回栈管理活动。如与其他程序共享同一个活动或者调用，不管哪个应用访问活动，共用一个返回栈（新的）。只有标记了singleInstance的活动才会入新栈，随后生成的活动还是返回到原始栈。

### 基础UI
 1. 基础中的基础：TextView、Button、EditText(hint,maxLines)、ImageView(src)、ProgressBar(style[更改显示样式，圆形，水平进度条]、setProgress(int))
 2. AlertDialog、ProgressDialog：通过代码实现
  ```java
  AlertDialog.Builder dialog = new AlertDialog.Builder(Context);
  //设置标题、信息、设置点击两个按钮的监听setPositiveButton("OK", new DialogInterface.OnClickListener(){})与setNegative("Cancel", new Dialog)
  dialog.show();

  ProgressDialog progressdialog = new ProgressDialog(Context);abstract// 设置标题、返回键关闭等
  progressdialog.show();
  // 绑定事件监听、使用dismiss()关闭
  ```

#### 布局
 1. 四大基本布局：线型布局(LinearLayout)、相对布局(RelativeLayout)、帧布局(FrameLayout)、百分比布局(PercentFrameLayout)。
  + 线型布局：orientation、layout_weight
  + 相对布局：layout_alignParent[Left/Top/...]、layout_to[Left/Top/...]Of
  + 帧布局：内部元素默认在左上角
  + 百分比布局：'com.android.support:percent:version'、app:layout_[width/height]Percent
  + gravity与layout_gravity:前者是View内部内容的布局位置、后者是布局在父布局的位置

待续---2018年5月30日23点52分




