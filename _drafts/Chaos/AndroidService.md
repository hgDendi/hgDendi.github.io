# Service #

	// 首次启动时会调用，若已在运行，则不会被调用
    onCreate()
	
	//每次启动后都会被调用
	onStartCommand(){
		//Service运行的进程被Android系统强制杀掉后不会重新创建Service
		return START_NOT_STICKY;

		//被强制杀掉后，会将Service依然设置为运行状态，但是不再保存onStartCommand方法传入的Intent对象，会传入null
		return START_STICKY;

		//再次创建时会把最后一次传入onStartCommand方法中的Intent再次保留下来
		return START_REDELIVER_INTENT;

	}
	
	// bindService()后启动
	onBind()

	onDestroy()