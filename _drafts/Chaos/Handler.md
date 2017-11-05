>   介绍Android中的Handler，包括Android消息机制的架构

消息队列由pipe机制进行同步

## Looper

用来执行消息队列中的消息，本质是一个while循环，通过pipe机制进行同步

每一个Thread最多拥有一个Looper，而Thread只有拥有了Looper，才能初始化Handler，否则new的时候会直接报Exception

```java
class LooperThread extends Thread{
  	public Handler mHandler;
  	
  	public void run(){
  		// 初始化Looper
      	Looper.prepare();
      	
      	mHandler = new Handler(){
          	// todo
      	}
      	
      	// 开启Looper
      	Looper.loop();
  	}
}
```



### ThreadLocal

>   Looper的prepare和loop方法都是static的，没有指定目标，这是为什么？

要回答这个问题，首先要了解一下ThreadLocal的概念。

ThreadLocal<T>可以近似的认为是Map<Thread,T>，它的get方法就是以当前线程为key去map中取对应的T，不同的线程调用ThreadLocal的get方法，会拿到不同的结果。

因为线程间共享进程的内存资源，而不拥有独立的系统资源（除了必不可少的资源如程序计数器、寄存器、栈等），ThreadLocal的出现就是为了给线程以区别其他线程的资源。

Looper内部依赖ThreadLocal进行的同步，ThreadLocal是一个容器，用来存放线程x相关的变量，保证每一个线程都拥有单独的成员变量，保证了线程安全。

故而Looper.prepare()和Looper.loop()其实就是当前线程对应的Looper的prepare()和loop()。

### pipe

## Handler

## MessageQueue

## Message