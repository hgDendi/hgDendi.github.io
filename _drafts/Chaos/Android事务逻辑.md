# Android事务逻辑 #

### ANR ###
Application not responding

- 主线程在5秒内没有响应输入时间
- BroadcastReceiver没有在10秒内完成返回

在主线程和BroadcastReceiver中进行网络操作、缓慢的磁盘操作、高耗时的计算


### AsyncTask ###

重写方法:

- doInBackground(Params...)
	- 在后台线程执行，返回Result,执行过程中调用publicProgress(Progress)来进行任务进度的更新
- onPostExecute(Result)
	- 在UI线程中执行
- onProgressUpdate(Progress...)
	- 在主线程执行,在publicProgress方法调用后执行



必须遵守的规则:

1. Task实例必须在UI线程中创建
2. execute方法必须在UI线程中调用
3. 该Task只能被执行一次,多次调用时会出现异常
4. 不要手动调用onPreExecute....等方法

### 事件的分发处理 ###



- 事件传递:由上到下,由大到小。
	- dispatchTouchEvent()
	- onInterceptTouchEvent(),返回true则拦截



- 事件处理:由下到上,由小到大
	- onTouchEvent(),返回true则不用审核

### AndroidMainifest启动模式 ###


- standard

- singleTop
>判断当前栈顶是否是要启动的Activity,若是则调用onNewIntent()方法




- singleTask
>整个任务栈中Activity唯一,会将Activity上的任务都销毁。如果Activity已经在后台一个任务栈中了,那么启动后,后台的这个任务栈将一起被切换到前台。

>可以用来退出整个应用,将主Activity设为singleTask,在onNewIntent()中加上finish()




- singleInstance
>Activity会出现在一个新的任务栈中,并且该任务栈只存在这一个Activity,常用于需要与程序分离的界面。


不同Task之间不能传递数据的,如果一定要传递,只能通过Intent来绑定数据。
如果在singleTop和singleInstance中通过startActivityForResult()方法来启动另一个Activity,那么系统将直接返回Activity.RESULT_CANCELED而不会再去等待返回。


### IntentFlag ###

-  Intent.FLAG_ ACTIVITY_ NEW_ TASK
>使用一个新的Task来启动一个Activity,通常使用在从Service中启动Activity的场景,由于在Service中并不存在Activity栈,所以使用该Flag来创建一个新的Activity栈



-  FLAG_ ACTIVITY_ SINGLE_ TOP
>同singleTop



- FLAG_ ACTIVITY_ CLEAR_ TOP
>singleTask



-  FLAG_ ACTIVITY_ NO_ HISTORY
>使用这种模式启动Activity,当该Activity启动其他Activity后,该Activity就消失了,不会保留在Activity栈中。

### 清空任务栈 ###
activity中可以添加如下标签

- clearTaskOnLaunch
>每次返回该Activity时,都将该Activity之上的所有Activity都清除


- finishOnTaskLaunch
>当离开这个Activity所处的Task,那么当用户再返回时,该Activity会被finish掉


- alwaysRetainTaskState
>该Activity所在的Task将不接受任何清理命令，一直保持当前Task状态

### Timer ###

    Timer timer = new Timer();
	TimerTask task = new TimerTask(){
		public void run(){}
	}
	//从1秒钟开始，每5秒执行一次Task
	timer.schedule(task,1000,5000);
	//取消timer
	timer.cancel();

### PendingIntent ###
>Intent是及时启动，随所在的Activity消失而消失
>
>PendingIntent可以看做是对intent的包装，通常通过PendingIntent.getActivity()、getBroadcast()、getService()来得到pendingIntent的实例
>
>并不马上启动，而是在外部执行pendingintent时才进行调用。里面包含当前App的Context,使它赋予外部App可以如同当前App一样的执行Intent。

>可以理解为封装给别人执行或者延迟执行的Intent


### 资源文件的使用 ###

	//id的获取
	//in XML
	@[<package_name>:]<resource_type>/<resource_name>
	//in Code
	@<package_name>.R.<resource_type>.<resource_name>

    Resource res = getResources();
	res.getString(int id);
	res.getDeminsion(int id);
	res.getInteger(int id);

### 动态获取id ###

   	    int ridInt = mContext.getResources().getIdentifier(sb.toString(),
                "string", mContext.getPackageName());
        if (ridInt == 0) {
            resultString = "未定义";
        } else {
            resultString = mContext.getString(ridInt);
        }


### 获取版本号 ###

    PackageManager manager = this.getPackageManager();
	PackageInfo info = manager.getPackageInfo(this.getPackageName(),0);
	String version = info.versionName;


### 国际化和本地化 ###
- Internationalization = I18N
- Localization = L10N

国际化思路：
>将程序中的标签、提示等信息放在资源文件中,程序需要支持哪些国家、语言环境，就提供响应的资源文件，资源文件是key-value对，每个资源文件中的key是不变的,但value则随不用国家、语言改变。

资源文件的命名:
>BaseName是资源文件的基本名,language和country必须是Java所支持的语言和国家
>
- baseName_language_country.properties
- baseName_language.properties
- baseName.properties

相关类：

- ResourceBundle:用于加载一个国家、语言资源包
- Locale：用于封装一个特定的国家/区域、语言环境
- MessageFormat:用于格式化带占位符的字符串
 >

    public static void main(String args[]){
		//取得系统默认的国家、语言环境
		Locale myLocale = Locale.getDefault(Locale.Category.FORMAT);
		//根据指定国家、语言环境加载资源文件
		ResourceBundle bundle = ResourceBundle.getBundle("mess",myLocale);
		//输出从资源文件中取得的消息
		System.out.println(bundle.getString("hello"));
	}
	
	// mess_zh_CN.properties
	hello = 您好！
	// mess_en_US.properties
    hello = Welcome!

	//非西欧字符的资源文件
	native2ascii 源资源文件 目的资源文件


提供国际化资源：

- 文件夹命名方式
	- values-语言代码-r国家代码
	- drawable-语言代码-r国家代码


### Handler ###
使用普通的Thread创建Handler:

    Handler mHandler;
	public void createHandler(){
		new Thread(){
			@override
			public void run(){
				super.run();
				//使用Looper创建于当前线程绑定的Looper实例
				Looper.prepare();
				//使用Looper生成Handler实例创建Handler
				mHandler = new Handler(Looper.myLooper());
				//实现消息循环
				Looper.loop();
			}
		}.start();
	}

HandlerThread的实现

    @override
	public void run(){
		mTid = Process.myTid();
		Looper.prepare();
		synchronized(this){
			mLooper = Looper.myLooper();
			notifyAll();
		}
		Process.setThreadPriority(mPriority);
		onLooperPrepared();
		Looper.loop();
		mTid = -1;
	}

相关概念：

- Message
	- 消息，发送到Handler进行处理的对象
- MessageQueue
	- 消息队列，Message的集合
- Looper
	- 用来从MessageQueue中抽取Message
- Handler
	- 处理Looper抽取出来的Message

如何使用HandlerThread

    private Handler mHandler;
	HandlerThread workerThread = new HandlerThread("LightTaskThread");
	workerThread.start();
	mHandler = new Handler(workerThread.getLooper());

>可以使用HandlerThread处理本地IO读写操作，因为其大多属于毫秒级别，对于单线程+异步队列不会产生较大的阻塞

>对于本地读取操作可以使用postAtFrontOfQueue方法，快速将读取操作加入队列前端执行，必要时返回给主线程更新UI

>对于本地IO写操作，根据具体情况，选择post或者postDelayd方法执行，比如SharedPreference commit，或者文件写入操作。


### 网络应用 ###

>Android不允许在非UI线程中建立网络连接

><uses-permission android:name="android.permissioon.INTERNET"/>

>IP协议是Internet上使用的一个关键协议，可以使Internet成为一个允许连接不同类型的计算机和不同操作系统的网络，保证了计算机之间可以发送和接收数据

>TCP协议使用重发机制：当一个通信实体发送一个消息给另一个通信实体后，需要收到另一个通信实体的确认信息，若没有收到则会进行重发。


服务器端：
>ServerSocket用于接收其他通信实体的连接请求
>
>一般指定1024以上的端口作为ServerSocket的端口号，避免与其他应用程序冲突
>
>使用accept()会返回一个Socket

    ServerSocket ss = new ServerSocket(int port,int backlog,InetAddress localAddr);
	while(true){
		Socket s = ss.accept();
		new Thread(new ServerThread(s)).start();//防止阻塞 
	}

Socket

	//指定远程主机、远程端口的Socket,默认使用系统分配的端口
    Socket s = new Socket("192.168.1.88",30000);
	//
	Socket s = new Socket(InetAddress/String remoteAddress,int port,InetAddress localAddr,int localPort);
	//当执行new之后,就已经激活了服务器端SocketServer的accept()操作


	//Socket中的操作
	InputStream getInputStream();//获取输入流
	OutputStream getOutputStream();//获取输出流

	//设置超时
	socket.setSoTimeout(100000);//设置超时,会返回SocketTimeoutException

	//设置无连接的Socket,然后再进行连接
	Socket s = new Socket();
	s.connect(new InetSocketAddress(host,port),100000);


举例：
    
	//Server
	while(true){
		Socket socket = serverSocket.accept();
		OutputStream os = s.getOutputStream();
		os.write("hello world".getBytes("utf-8"));
		os.close();
		socket.close();
	}

	//Client
	Socket socket = new Socket("ip",30000);
	BufferedReader br = new BufferedReader(
			new InputStreamReader(
				socket.getInputStream()
			)
	);
	String line = br.readLine();
	br.close();
	socket.close(); 

### URL访问网络资源 ###

>URL Uniform Resource Locator代表同意资源定位器，指向互联网资源的指针。可以是简单文件或目录、对更复杂对象的引用，入数据库或搜索引擎的查询等。

>格式: protocol://host:port/resourceName

    URL类支持的方法
	String getFile()
	String getHost()
	String getPath()
	int getPort()
	String getProtocol()
	String getQuery()
	URLConnection openConnection()
	InputStream openStream()

#### 使用URL进行下载 ####
	InputStream is = url.openStream();
	//打开手机文件对应的输出流
	OutputStream os = openFileOutput("crazyit.png",MODE_PRIVATE);
	byte[] buff = new byte[1024];
	int hasRead = 0;
	while( (hasRead = is.read(buff)) >0 ){
		os.write(buff,0,hasRead);
	}
	is.close();
	os.close();

#### 使用URL提交请求 ####
1. 通过调用URL对象的openConnection()方法来创建URLConnection
2. 设置URLConnection的参数和普通请求属性
3. 如果只是发送GET方式的请求，那么使用connect方法建立和远程资源之间的实际连接即可；如果需要发送POST方式的请求，则需要获取URLConnection实例对应的输出流来发送请求参数
4. 远程资源变为可用，程序可以访问远程资源的投资端，或通过输入流读取远程资源的数据

