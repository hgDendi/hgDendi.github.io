# RxJava #

## 3 major things

1. A set of classes for representing data sources
2. A set of classes for listening to data sources
3. A set of methods for modifying and composing the data

## 最核心的两个东西是Observables和Subscribers ##

- Observable和Subscriber可以做任何事情
  - Observable可以是一个数据库查询、点击事件、网络请求等
- Observable和Subscriber是独立于中间的变换过程的
  - 在Observable和Subscriber中间可以增减任何数量的map，系统是高度可组合的，操作数据是一个很简单的过程

类似设计模式中的观察者模式，但是一点不同是如果一个Observerble没有任何的Subscriber，那么这个Observable是不会发出任何事件的。


	//效果同Observable<String> myObservable = Observable.just("Hello, world!"); 
	Observable<String> myObservable = Observable.create(
		new Observable.OnSubscribe<String>(){
			@Override
			public void call(Subscriber<? super String> sub){
				sub.onNext("Hello,world!");
				sub.onCompleted();
			}
		}
	);


	Subscriber<String> mySubscriber = new Subscriber<String>(){
		@Override
		public void onNext(String s){
			System.out.println(s);
		}
	
		@Override
		public void onCompleted(){
		}
	
		@Override
		public void onError(Throwable e){
		}
	}


	Action1<String> onNextAction = new Action1<String>(){
		@Override
		public void call(String s){
			System.out.println(s);
		}
	}


	//完成订阅
	myObservable.subscribe(mySubscriber);
	
	//通过Action订阅，完整的顺序为onNextAction,onErrorAction,onCompleteAction
	myObservable.subscribe(onNextAction);


	//如果使用lambda可以使代码更简洁
	Observable.just("Hello,world!")
		.subscribe(s->System.put.println(s));

## Operator ##
用于在Observable和最终的Subscriber之间修改事件，即在事件传递过程中对事件进行更改，以保证Subscribers的轻量

### map ###
    Observable.just("Hello,world")
    	.map(new Func1<String,String>(){
    		@Override
    		public String call(String s){
    			return s + " -Dendi";
    		}
    })
    .subscribe(s -> Ststem.out.println(s));


	//使用Lambda后
	Observable.just("Hello,world")
		.map(s -> s+"-Dendi"
		.scribe(s -> System.out.println(s));


### from ###
接收一个集合作为输入，每次输出一个元素给subscriber

    Observable.from("url1","url2","url3")
    	.subscribe(url -> System.out.println(url);

### flatMap ###
接收一个Observable的输出作为输入，同时输出另外一个Observable
    Observable<List<String>> urlsObservable;
    urlsObservable.flatMap(new Func1<List<String>,Observable<String>>(){
    	@Override
    	public Observable<String> call(List<String> urls){
    		return Observable.from(urls);
    	}
    })
    .subscribe(url -> System.out.println(url));

### filter , take ， doOnNext ###
	query("Hello world!")
		.flatMap(urls -> Observable.from(urls))
		.flatMap(url -> getTitle(url))
		//不输出null
		.filter(title -> title != null)
		//只想要最多5个结果
		.take(5)
		//doOnNext
		.doOnNext(title -> doSomething(title))
		.subscribe(title -> System.out.println(title);

### 错误处理 ###

每一个Observerable对象在中介的时候都会调用onCompleted()或者onError()方法
- 只要有异常发生onError()一定会被调用
- Operator不需要处理异常
- 你能够知道什么时候订阅者已经接收了全部的数据

### 调度器 ###

使用subscribeOn()指定观察者代码运行的线程，使用observeOn()指定订阅者运行的线程
    myObservableServices.retrieveImage(url)
    	.subsscribeOn(Schedulers.io())
    	.observeOn(AndroidSchedulers.mainThread())
    	.subscribe(bitmap -> myImageView.setImageBitmap(bitmap));
    //任何在subscribe前面执行的代码都是在I/O线程中运行，操作view的代码在主线程中运行

### 订阅Subscriptions ###

当调用Observable.subscribe()，会返回一个Subscription对象。这个对象代表了被观察者和订阅者之间的联系。

可以使用这个Subscription对象来操作被观察者和订阅者之间的联系

    Subscription subscription = Observable.just("Hello , World!").subscribe(s -> System.out.println(s));

	subscription.unsubscribe();`

## AndroidObservable ##
提供了更多的功能来配合Android的声明周期

### bindActivity/Fragment()  ###
bindActivity()和bindFragment()方法会在Activity或者Fragment结束的时候通知Observable停止发出新的消息

	AndroidObservable.bindActivity(this,retrofitService.getImage(url))
		.subscribeOn(Schedulers.io())
		.subscribe(bitmap -> myImageView.setImageBitmap(bitmap);

### fromBroadcast ###
创建一个类似BroadcastReceiver的Observable对象
	//网络变化的时候被通知到
	IntentFilter filter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION);
	AndroidObservable.fromBroadcast(context,filter)
		.subscribe(intent -> handleConnectivityChange(intent));

### ViewObservable ###
可以给View添加一些绑定
- ViewObservable.clicks()
  - 在点击view的时候收到事件
- ViewObservable.text()
  - 监听TextView内容变化

    ViewObservable.clicks(mCardNameEditText,false)
    .subscribe(view -> handleClick(view));

## 解绑 ##

常见的模式是使用CompositeSubscription来持有所有的Subscriptions，然后在onDestroyView()里取消所有的订阅

    private CompositeSubscription mCompositeSubscription = new CompositeSubscription();

	private void doSomething(){
		mCompositeSubscription.add(AndroidObservable.bindActivity(this,Observable.just("Hello,World!"))
			.subscribe( s -> System.out.println(s)));
	}
	
	@Override
	protected void onDestroy(){
		super.onDestroy();
	
		//一旦调用了unsubscribe()这个CompositeSubscription对象就不可用了
		mCompositeSubscription.unsubscribe();
	}