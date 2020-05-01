---
title: Android Service
date: 2018-06-09 20:23:49
tags: [Android]
categories: [技术]
---

![](Android-Service/index.jpg)


### 服务 Service

 在程序后台运行的，不需要和用户交互且要求长期运行的任务，不依赖于用户界面，程序被切换到后台之后仍能正常运行。依赖于应用程序的进程，应用程序进程被杀死时，服务也停止。代码运行于主线程中，需要则考虑自己开线程。<!-- more -->

#### 异步处理机制
 1. 组成：Message、Handler、MessageQueue、Looper
  + Message 用于消息的存取，what、arg1、arg2、obj 等可以存取，
  + Handler 则是处理消息，使用 sendMessage() 方法发送消息到 MessageQueue 中，最终传到 Hanlder 的 hanldeMessage() 方法中，以供处理。
  + MessageQueue 是消息队列，用于存放 Hander 使用 sendMessage() 发送的 Message，等待 Looper 的提取，处理。每个线程只会有一个 MessageQueue 对象。
  + Looper 循环监控 MessageQueue，调用 Looper 的 loop() 发送的消息之后，无限循环，发现 MessageQueue 中有消息时取出给 Hander 调用 handleMessage() 方法。每个线程只有一个 Looper 对象。
  ![异步消息处理机制流程《第一行代码》第二版·郭霖](images/handler.png "异步消息处理机制流程《第一行代码》第二版·郭霖")

 2. AsyncTask 的使用
  实现原理基于异步处理机制，抽象类，需要子类继承使用，继承时可以使用 3 个泛型参数：
  ```
  Params: 执行 AsyncTask 时需要传入的参数，可用于后台任务
  Progress: 后台执行任务时，用于显示进度
  Result: 执行完毕之后，如需对结果进行返回，则用于返回

  定义如下：

  class NewTask extends AsyncTask<Void, Integer, Boolean> {
    ...
  }
  ```
  重写 AsyncTask 方法完成任务的定制，主要方法有以下几个：
  1. onPreExecute(): 会在后台任务执行之前使用， 可以进行 UI 操作，如一些界面上的初始化操作，如进度条对话框。
  2. doInBackground(Params...): 这个方法的所有代码都会在子线程中执行，处理耗时任务，任务完成时可以通过 return 返回结果，如果 Result 的类型是 Void 则没有返回。不可以进行 UI 操作，但可以调用诸如 publishProgress(Progress...) 方法完成。
  3. onProgressUpdate(Progress...): 当在后台调用 publishProgress(Progress...) 方法后，这个方法很快被调用，携带的参数就是后台任务中传递过来的。可以进行 UI 的操作。
  4. onPostExecute(Result): 当在后台执行完毕并通过 return 返回时，调用方法。返回的数据作为参数传递到该方法，可以进行 UI 操作，如提醒结果，关闭对话框等。
  5. 使用 new NewTask().execute(); 来启动这个任务

  **注意:** publishProgress() 方法要在 doInBackground() 中调用，才能把执行的进度传到 onProgressUpdate() 中， onProgressUpdate() 才会执行。

#### 服务的基本用法
 1. 定义服务
  从 AS 中右键 New 中定义新的的服务。其中除了命名之外还需要选中 Exported 和 Enabled 两个属性，和广播的属性定义是一样的。AS 会在 AndroidManifest.xml 中自动生成相关代码。

  自定义 NewService 继承自称自 Service 类，要重写一个 onBind() 方法，在 Service 类中时抽象的，需要在子类中实现。

  重写其他和 Service 生命周期有关的方法，比如：onCreate()、onStartCommand()、onDestroy()，分别在服务创建时、每次启动服务时、服务销毁时进行调用。

 2. 启动与停止
  借助 Intent 来实现启动和停止。
  + 在活动中调用 ``startService(new Intent(this, NewService.class));``  进行启动
  + 同理，使用 ``stopService(new Intent(this, NewService.class));`` 来停止
  + 在 NewService 中，可以使用 stopSelf() 方法停止服务

  **注意：** 对于广播和服务等一些比较特殊的类型来说，当子类时内部类时，需要使用 static 修饰。否则将报错： has no zero argument constructor 的错误。

#### 活动和服务的通信
 使用自定义服务 onBind() 方法进行管理。
 1. 创建自定义绑定类，继承于 Binder，在该类中可以定义了解服务内容需要的方法。
 2. 在 onBind() 中返回这一个绑定类，实现了 IBinder 接口
 3. 使用 ServiceConnection 实例来获取服务 onBind() 中返回的自定义 Binder（需要转型），在该类中可以定义了解服务内容需要的方法。实现了两个接口，分别在活动与服务成功绑定以及接触绑定的时候调用。
 4. 使用 ``bindService(Intent, ServiceConnection, BIND_AUTO_CREATE);`` 绑定服务，使用 ``unbindService(ServiceConnection);`` 解绑服务。其中 BIND_AUTO_CREATE 表示在霍东阁和服务绑定后自动创建服务而不经过 onStartCommand() 方法。
 5. 绑定之后，可以通过调用自定义的 Binder 类的方法来实现服务执行功能。
 6. 可以和不同的活动进行绑定，返回的是同一个 Binder 实例。
 7. 可以进行借助自定义的 Binder 和 Service 进行通信了。

 **注意**

  服务都会只有一个，当服务都使用了 startService() 和 bindService() 两个方法，需要同时调用 stopService() 和 unbindService() 才能销毁服务，两个条件要都满足。而且如果使用 bindService() 生成、不使用 startService()，则必定是以 unbindService() 销毁；同理，使用 startService() 创建而不适用 bindService()，则必以 stopService() 销毁。两者都使用，则两者都不使用时才销毁，否则服务将一直运行。

  当使用服务做一些耗时的工作时，主要执行在 onStartCommand() 方法中，所以需要开启子线程进行处理。如果不适用子线程，则代码运行在主线程中，很容易出现 ANR(Application Not Responding) 的情况。

  可以使用继承自 IntentService 的类来避免复杂操作，该类可简单创建异步、会自动停止的服务。重写 onHandleIntent() 中执行代码则不需要担心 ANR 问题，因为该方法在子线程中运行。

#### 生命周期
 1. startService -> (onCreate) -> onStartCommand -> stopService/stopSelf -> onDestroy
 2. bindService -> (onCreate) -> onBind -> unbindService -> onDestroy
 3. 每调用一次 startService 就会执行一次 onStartCommand

#### 进阶
 前台服务与普通服务，有一个图标显示在状态栏，下拉可看到更详细信息，类似于同值效果，防止 GC。使用 PendingIntent，Notification 等。使用 IntentService 等。

 **致谢：**
  1. 《第一行代码（第二版）》·郭霖


 <center> --- end --- </center>

 ---
