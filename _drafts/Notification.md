# Notification

#### Notification

> 显示在手机状态栏的通知，代表具有全局效果的通知，一般通过NotificationManager服务来发送Notification

1. 调用getSystemService(NOTIFICATION_SERVICE)方法获取系统的NotificationManager服务
2. 通过构造器创建一个Notification对象(NotificationCompat.Builder)
3. 为Notification设置属性
4. 通过NotificationManager发送Notification

```
//for example
Notification notification = new Notification.Builder(this)
.setXXX()
.build();

NotificationManager nm = (NotificationManager)getStstemService(NOTIFICATION_SERVICE);
nm.notify(NOTIFICATION_ID,notification);
nm.cancel(NOTIFICATION_ID);
```



```
//Notification.Builder中的方法
//设置通知LED灯、音乐、振动等
setDefaults();

//点击通知后，状态栏自动删除通知
setAutoCancel();

//设置通知标题
setContentTitle()

//设置通知内容
setContentText();

//为通知设置图标
setSmallIcon();
setLargeIcon();

//设置通知在状态栏的提示文本
setTicker();

//设置点击通知后将要启动的程序组件对应的PendingIntent
setContentIntent()

setWhen(System.currentTimeMillis());

setProgress(max,progress,indeterminate);
```

