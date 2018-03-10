---
title: Android 广播机制与服务
date: 2018-03-07 17:54:23
tags: [Android]
categories: [技术]
---
<center>
Author: dongmodao
</center>

---

![](Android-广播机制与服务/main-img.jpg "image from Momentum")

## 前言
今天有兴趣来看看 Android 相关的一些东西，看了一些关于桌面弹窗的内容，然后谷歌看到实现方式大多是使用服务(Service)来实现的，然后想着想着又想到了广播机制(Broadcast)。于是今天稍微写一下关于两者的一点内容，内容不多，偏向于入门，算是用于备忘吧。<!-- more -->

## 广播机制(Broadcast)
 广播机制，广而播之，通俗来说就是将一个通告向所有地方（频道）发送，在 Android 中，就是向监听了某个广播的接收器发送通告。比如电池电量、时间更变等情况下，会发送对应的广播，如果应用注册了这两个广播，就可以接收到该条广播，从而实现对应的操作。
 广播分为两种类型：标准广播和有序广播。
 - **标准广播**：完全异步的广播，所有接收器几乎同时收到消息，无顺序，不可截获。是 1:N 的状态。
 - **有序广播**:同步执行的广播，一个时刻只会被一个接收器接收，完成逻辑的执行后方可继续传播，有顺序，可被截获，随后的接收器就不能收到消息了。


### 接收广播
 广播接收器需要注册广播才能接收到广播，并处理逻辑。注册广播有两种方法，动态注册，也即在代码中注册，静态注册则是在 AndroidManifest.xml 中注册。

 接收流程主要为：
 1. 新建继承自 BroadcastReceiver 的接收器，并重写 onReceive() 方法。
 2. 使用 IntentFilter 的实例的 addAction() 方法添加对应广播的 action。
 3. 创建接收器实例，然受使用 registerReceiver 传入参数接收器和 IntentFilter 的实例。动态注册时需要 unregisterReceiver() 取消注册。

 郭神《第一行代码》示范如下(如需进行其他的复杂逻辑操作，可能需要添加权限)：
 ``` java
    private IntentFilter intentFilter;
    private NetworkChangeReceiver networkChangeReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        intentFilter = new IntentFilter();
        intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
        networkChangeReceiver = new NetworkChangeReceiver();
        registerReceiver(networkChangeReceiver, intentFilter);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(networkChangeReceiver);
    }

    class NetworkChangeReceiver extends BroadcastReceiver{
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context, "network change.", Toast.LENGTH_SHORT).show();
        }
    }
 ```

 动态注册需要在程序启动之后才能接收广播，静态注册则不必启动。静态注册也需要自定义接收器才能实现自己的逻辑。同时要在 AndroidManifest.xml 中注册，在 application 标签内新建子标签如下：
 ``` xml
        <receiver android:name="广播接收器名"
            android:enabled="true"
            android:exported="true" >
            <intent-filter>
                <action android:name="广播活动" />
            </intent-filter>
        </receiver>
 ```

### 发送广播
 发送自定义广播流程：
 1. 定义接收器
 2. 在 AndroidManifest.xml 中注册接收器，名称为接收器类名，使用自定义广播值作为筛选，
 3. 使用 sendBroadcast(new Intent("广播活动值")) 发送广播。

 发送有序广播，使用 sendOrderBroadcast(intetn, 权限字符串/null)，可以在筛选器中使用 priority 更改接收广播的优先级，接收器收到后可以截断 abortBroadcast()。

 **使用本地广播**：为了避免其他程序接收广播，使用本地广播，广播只能在应用内接收，不能在系统全局接收，作用为保密性强，排外性高（不接受外部应用同一个广播活动值），高效率。


 所以要定义一个 LocalBroadcastManager 实例如 localBroadcastManager 用于管理本地广播的接发。发送时使用该 localBroadcastManager.sendBroadcast(new Intent("广播活动值"))，注册/注销时也需要该实例的相应方法，和全局类似，只是有实例发起。不再赘述。

## 服务(Service)
服务可以认为是在后台运行的任务，使用于执行不需交互并长期运行的任务，切换应用至后台时仍然运行。依赖于创建服务的应用程序进程，杀死该进程则服务停止。服务不会自动开启线程，代码默认运行在主线程中，需在内部手动开启子线程并处理任务，否则可能阻塞主线程。

### 使用流程
服务的使用流程：
1. 自定义服务继承于 Service，实现 onBind() 方法。
2. 可根据需求重写诸如 onCreate()、onStartCommand()、 onDestroy() 等方法。
3. 在 AndroidManifest.xml 中注册服务，类似于广播，标签为 service，主要属性 enabled 和 exported.
4. 使用 Intent 启动和停止，如 startService(new Intent(Context, ServiceName.class)), 同理使用 stopService(Intent)。

### 和活动通信
 使用 onBind() 方法实现，在其中返回一个继承 Binder 类型的实例，如 UploadBinder extends Binder,其中定义可以在外部使用的方法，以进行消息传递。创建 ServiceConnection 匿名类，重写相应方法。绑定服务和解绑服务。

 主要的代码入下：
 ``` java
         class MyService extends Service{
            
            private MyBinder myBinder = new MyBinder();

            class MyBinder extends Binder{
                public int getXXX(){
                    // todo:.........
                    return 0;
                }
            }
            
            @Nullable
            @Override
            public IBinder onBind(Intent intent) {
                return myBinder;
            }
        }
        
        // 创建 ServiceConnection 匿名类，并重写两个方法，分别用于连接服务时和断开服务时
        private ServiceConnection connection = new ServiceConnection() {
            @Override
            public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
                myBinder = (MyBinder)iBinder;
                // 执行 MyBinder 的公开方法，获取信息
                myBinder.getXXX();
            }

            @Override
            public void onServiceDisconnected(ComponentName componentName) {
            }
        };
        // 绑定服务
        Intent bindIntent = new Intent(this, MyService.class);
        bindService(bindIntent, connection, BIND_AUTO_CREATE); // 绑定，绑定后自动创建服务
        unbindService(connection);  // 解除绑定
 ```

## 后记
好饿，写不下去了，今天看了这两个东西，写这个要时间好久，饿的不行了，11 点早午餐到现在，不行了，我要回去吃东西了，后面写的仓促，不过大致能看懂吧。如果不懂可以找找郭神的《第一行代码》看看对应章节。这些东西书籍为主，谷歌为辅，差不多的。饿，饿，饿，曲项向晚餐。—— 2018-03-07 20:51

<center> --- end --- </center>