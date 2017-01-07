# Pending Intent #

用于处理即将发生的事情，因为其中保存有当前APP的Context，使它赋予外部APP一种能力，使得外部APP可以如同当前APP一样的执行intent（即时在执行时当前APP已经不存在了）

- getActivity(Context context,int requestCode,Intent intent,int flags)
- getBroadcast
- getService
>flags对应取值有FLAG_ONE_SHOT FLAG_NO_CREATE FLAG_CANCEL_CURRENT FLAG_UPDATE_CURRENT


分别对应跳转到一个activity组件、打开一个广播组件和打开一个服务组件

- PendingIntent可以cancel
- Intent在原Task中运行，PendingIntent在新的Task中运行

## 原理 ##
可以理解为特殊的异步处理机制

AMS：Activity Manager Service

AMS从发起端获取PendingIntent，在受到处理端激发事件后进行激活，将intent递送出去




## Notification ##
    NotificatoinManager mNotificationManager = (NotificationManager)getSystemService(Context.NOTIFICATION_SERVICE);
	int icon = R.drawable....
	long time = System.currentTimeMillis();

	//第一个参数为图标，第二个参数为短暂提示标题，第三个为通知时间
	Notification notification = new Notification(icon,null,when);
	//发出默认声音
	notification.defaults = Notification.DEFAULT_SOUND;
	//点击通知后自动清除通知
	notification.flags |= Notification.FLAG_AUTO_CANCEL;

	Intent intent = new Intent(this,AnotherActivity.class);
	PendingIntent contentIntent = PendingIntent.getActivity(this,0,intent,0);
	notification.setLastestEventInfo(this,"title","content",contentIntent);

	//第一个参数为自定义的通知唯一标记
	mNotificationManager.notify(0,notification);

## SmsManager ##
	String msg = "This is for test";
	String number = "139~~~";
	SmsManager sms = SmsManager.getDefault();

	PendingIntent pi = PendingIntent.getBroadcast(SmsActivity.this,0,new Intent(XX),0);
	sms.sendTextMessage(number,null,msg,pi,null);

	//sentIntent是短信发出时发出的
	//deliveryIntent为消息已经传递给收信人后所进行的
	//SmsManager.sendTextMessage(String destinationAddress, String scAddress, String text, PendingIntent sentIntent, PendingIntent deliveryIntent)

## AlarmManager ##

    //实现长连接

	//Application的onCreate()
	public void onCreate(){
		startLongConn();
	}

	public void startLongConn(){
		quitLongConn();
		AlarmManager manager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
		Intent intent = new Intent(this,LongConnService.class);
		intent.setAction(LongConnService.ACTION);
		PendingIntent pendingIntent = PendingIntent.getService(this,0,intent,PendingIntent.FLAG_UPDATE_CURRENT);
		long triggerAtTime = SystemClock.elapsedRealtime();
		manager.setRepeating(AlarmManager.RTC_WAKEIP,triggerAtTime, 60*1000,pendingIntent);
	}