---
layout: post
comments: true
title: "Android的IPC机制"
date: 2017-05-07
categories: android
tags: [android]
---

[TOC]

# IPC机制

## 进程间通信

> **IPC**		 InterProcess Communication
>
> **RPC** 	Remote Procedure Call

Android默认每个app是一个进程，但也可以通过android:process属性使每个app有多个进程，或者多个app共享某个进程。

有时候通过多进程的方式获取多份内存空间。一般是指定android:process属性，还有一种非常规的方法，通过JNI在native层去fork一个新的进程。

* 进程名以”:“开头的进程属于当前应用的**私有进程**，其他应用的组件不可以和它跑在一个进程中
* 进程名不以 ":" 开头的程序属于**全局进程**，其他应用通过ShareUID方式可以和它跑在同一个进程中。
  * 如果两个应用需要通过ShareUID跑在同一个进程，需要这两个应用有相同的ShareUID并且签名相同才可以
  * 在这种情况下，可以互相访问对方的私有数据，比如data目录、组件信息等，当然还可以共享内存数据

```Xml
android:process = ":remote"
android:process = "com.dendi.helloworld.remote"
```

### 多进程的特性

> Android为每个进程都分配一个独立的虚拟机，在内存分配上有不同的地址空间，这就导致了在不同的虚拟机中访问一个类的对象会产生多份副本
>
> 故而多进程的APP会拥有独立的虚拟机、Application以及内存空间。
>
> 相当于两个不同的应用采用了SharedUID的模式（Android系统为每个应用分配一个唯一的UID，具有相同UID的应用才能共享数据）

- 不同的内存空间，数据无法共享
- 需要谨慎处理代码中的线程同步
- 需要提防多进程并发导致的文件锁和数据库锁时效的问题

具体问题：

```
1. 静态成员和单例模式完全失效
2. 线程同步机制失效
3. SharedPreferences可靠性下降(底层通过操作XML文件实现，并发写显然容易出问题)
4. Application会多次重建
```

## 三个基础知识

### Serializable

Serializable使用简单但是开销很大，序列化和反序列化过程需要大量的IO操作，一般用于将对象序列化到**存储设备**中或者将对象序列化后通过**网络传输**。

可以通过ObjectOutputStream和ObjectInputStream轻松实现。

其中serialVersionUID的作用的辅助序列化和反序列化，原则上序列化后的数据中的serialVersionUID只有和当前类的相同才能够被正常的反序列化。

静态变量和用transient关键字标记的成员变量不参与序列化的过程。

### Parcelable

Parcelable使用麻烦，但效率很高。

* 序列化	writeToParcel
* 反序列化 CREATOR
* 内容描述符 describeContents
  * 都应该返回0
  * 对象中存在文件描述符时，返回1

### Binder

binder是Android中的一种跨进程通信方式.

可以理解为一种**虚拟的物理设备**，它的设备驱动是/dev/binder.

从Android Framework角度来说，Binder是ServiceManager连接各种Manager和相应ManagerService的桥梁。

从Android应用层来说，Binder是客户端和服务端进行通信的媒介。当bindService的时候，服务端会返回一个包含了服务端业务调用的Binder对象，通过这个Binder对象，客户端就可以获取服务端提供的服务或者数据，这里的服务包括**普通服务**和**基于AIDL**的服务。

> Binder更安全，比如socket的ip地址可以进行伪造，而Binder机制从协议本身就支持对通信双方做身份校验，因而大大提升了安全性，这个也是Android权限模型的基础。

![Binder通信模型](http://upload-images.jianshu.io/upload_images/454052-af085ffaf33054ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



如下图，伪装。即**代理模式**。对代理对象的操作会通过驱动最终转发到Binder本地对象上去完成，当然使用者无需关心这些细节。

![Binder伪装](http://upload-images.jianshu.io/upload_images/454052-f45493b4121cd3b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Binder对象是一个可以跨进程引用的对象，它的实体位于一个进程中，它的引用却遍布于系统的各个进程中。最诱人的是，这个引用和java里引用一样既可以是强类型，也可以是弱类型，而且可以从一个进程传给另一个进程，让大家都能访问同一Server，就像一个对象或引用赋值给另一个引用一样。Binder**模糊了进程边界**，淡化了进程间通信过程，**整个系统仿佛运行于同一个面向对象的程序之中**。

##### AILD的本质是系统为我们提供了一种快速实现Binder的工具，仅此而已。

#### Service中的Binder

Android中Binder主要用在Service中，包括AIDL和messager（Messager底层实际就是AIDL）

## IPC方式

> 摘自任玉刚的《把玩Android多进程》ppt

![Screen Shot 2017-01-07 at 16.37.37](https://ww4.sinaimg.cn/large/006tNc79gw1fbi70eu93yj31kc0wqgxp.jpg)

### Bundle

可以在一个进程中启动另一个进程的Activity、Service、Receiver

### 文件共享

适合在对数据同步要求不高的进程之间进行通信，并且要妥善处理好并发读、写的问题。

SharedPreferences因为系统对它的读写有有一定的缓存策略，即在内存中会有一份SharedPreferences文件的缓存，因此在多进程模式下，系统对它的读、写就变得不可靠。

### Messenger

可以在不同进程中传递Message对象，在Message中放入我们需要传递的数据。

是一种轻量级的IPC方案，底层实现是AIDL。

一次只处理一个请求，因此在服务端我们**不用考虑线程同步**的问题。

通过Messgenger来传递Message，能使用的载体只有what、arg1、arg2、Bundle和replayTo。

```java
//Messenger的构造方法

// 初始化
Messenger(Handler);

// 从服务端获得IBinder后初始化
Messenger(IBinder);

//服务端
public class MessengerService extends Service{
  	private Messenger mMessenger = new Messenger(new Handler());
  
  	@Override
  	public IBinder onBind(Intent intent){
      	return mMessenger.getBinder();
  	}
}

//客户端
private mMessenger;

public ServiceConnection mConnection = new ServiceConnection(){
  	public void onServiceConnected(ComponentName className,IBinder service){
      	mMessenger = new Messenger(service);
  	}
}

onCreate(){
  	bindService(intent,mConnection,Context.BIND_AUTO_CREATE);
}
```

### AIDL

使用步骤：

* 服务端创建Service，实现AIDL接口,并在onBind中返回它

  ```java
  // Server实现AIDL接口
  private Binder mBinder = new IBookManager.Stub(){
    
  }

  @Override
  public IBinder onBind(Intent intent){
    	return mBinder;
  }
  ```

* 客户端绑定Service，将服务端返回的Binder对象转换成AIDL接口所属的类型

  ```Java
  public ServiceConnection mConnection = new ServiceConnection(){
    	public void onServiceConnected(ComponentName className,IBinder service){
        	IBookManager bookManager = IBookManager.Stub.asInterface(service);
    	}
  }
  ```

需要注意：

1. 对象的跨进程传输本质上是反序列化的过程，通过Binder传递后的对象会是全新的对象，但是这些对象会有一个共同点，他们底层的Binder是同一个。

   所以若要跨进程使用观察者模式，需要使用**RemoteCallbackList**。

   这个类还有一个很有用的功能，就是当客户端进程终止后，能够自动移除客户端所注册的listener。

2. Binder是会意外死亡的

   * 给Binder设置DeathRecipient监听
   * 在onServiceDisconnected中重连远程服务

3. 远程方法调用发生在binder线程池中，需要小心因为耗时操作导致本端阻塞

4. 服务端验证客户端权限

   * onBind中通过checkCallingOrsELFpermission("permission")检查客户端是否声明该权限
   * 验证包名，getPackageManager().getPackagesForUid(getCallingUid())

### ContentProvider

底层实现也是Binder,用于不同应用间进行数据共享的方式。

#### 创建一个ContentProvider

创建一个自定义的ContentProvider需要继承ContentProvider并实现六个抽象方法：

另外，是存在多进程并发访问的，故而四大方法需要做好线程**同步**准备。

* onCreate
  * 代表ContentProvider的创建，做一些初始化的操作
* query
* update
* insert
* delete
* getType
  * 返回一个Uri请求所对应的MIME类型，比如图片或者视频等
  * 不关注这个选项可以返回null或者“*/*”

需要注册：

```xml
<provider
	android:name = ".provider.BookProvider"
	//唯一标识
	android:authorities="com.dendi.chapter.book.provider"
	//权限声明
	android:permission="com.dendi.Provider"/>
```

查询时

```java
Uri userUri = Uri.parse("content://com.dendi.chapter.book.provider/user");
Cursor userCursor = getContentResolver().query(
  	userUri,new String[]{"_id","name","sex"},null,null,null);
while(userCursor.moveToNext()){
 	//print
}
```

#### 特性

以**表格**的形式来组织数据

### Socket

* 流式套接字
  * TCP
  * 面向连接，提供稳定的双向通信功能（本身提供了超时重传），有很高的稳定性
* 用户数据报套接字
  * UDP
  * 无连接，提供不稳定的单向通信功能
  * 效率更高，但是不能保证数据一定能够正确传输，尤其在网络拥塞的情况下

注意不要在UI线程中访问网络，会抛出异常。

另外Socket的ip地址是可以伪造的，故而不一定具有安全性。