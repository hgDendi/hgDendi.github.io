# FUTURE #
Future模式可以在连续流程中满足数据驱动的并发需求，既获得了并发执行的性能提升，又不失连续流程的简洁优雅。

核心在于：去除了主函数的等待时间，并使得原本需要等待的时间段可以用于处理其他业务逻辑。

## Future ##
表示异步计算的结果

### cancel ###
尝试取消此任务
### isCanceled ###
### isDone ###
### get ###
只有在计算完成时获取，否则会一直阻塞知道任务完成。
### get(long timeout,TimeUnit unit) ###
增加超时

## FutureTask ##
是Future的一个实现，并实现了Runnable，可通过Executor和Thread对象执行。

## Runnable 和 Callable ##
- Runnable实现没有返回值的并发编程
- Callable实现存在返回值（泛型）的并发编程
## ExecutorService ##

    ExecutorService executor = Executors.newSingleThreadExecutor();
    FutureTask<String> future = new FutureTask<String>(new Callable<String>){
        public String call(){}
    }
    executor.execute(future);

    try{
        result = future.get(5000,TimeUnit.MILLISECONDS);
    }catch^^^^{
    }finally{
        executor.shutdown();
    }

    //2
    FutureTask<String> future = executor.submit(new Callable<String>.....);

## Lock ##
提供了比使用synchronized方法和语句可获得的更广泛的锁定操作，能以更优雅的方式处理线程同步问题

但是Lock需要手动释放锁

    Lock lock = new ReentrantLock();
    lock.lock();
    try{

    }catch(Exception e){
        
    }finally{
        lock.unlock();
    }
    
读写锁
    //读和写不需要互斥
    ReadWriteLock rwl = new ReentrantReadWriteLock();
    rwl.writeLock().lock()/unlock();
    rwl.readLock().lock()/unlock();
    
## Condition ##
Condition将Object监视器方法（wait,notify,notifyAll）分解成截然不同的对象，以便通过将这些对象与任意lock实现组合使用，为每个对象提供多个等待set。其中Lock替代了synchronized方法和语句的使用，Condition替代了Object监视器方法的使用

    class BoundedBuffer {  
        final Lock lock = new ReentrantLock();//锁对象  
        final Condition notFull  = lock.newCondition();//写线程条件   
        final Condition notEmpty = lock.newCondition();//读线程条件   
  
        final Object[] items = new Object[100];//缓存队列  
        int putptr/*写索引*/, takeptr/*读索引*/, count/*队列中存在的数据个数*/;  
  
        public void put(Object x) throws InterruptedException {  
            lock.lock();  
            try {  
                while (count == items.length)//如果队列满了   
                    notFull.await();//阻塞写线程  
                items[putptr] = x;//赋值   
                if (++putptr == items.length) putptr = 0;//如果写索引写到队列的最后一个位置了，那么置为0  
                ++count;//个数++  
                notEmpty.signal();//唤醒读线程  
            } finally {  
                lock.unlock();  
            }  
        }  
  
        public Object take() throws InterruptedException {  
            lock.lock();  
            try {  
                while (count == 0)//如果队列为空  
                    notEmpty.await();//阻塞读线程  
                Object x = items[takeptr];//取值   
                if (++takeptr == items.length) takeptr = 0;//如果读索引读到队列的最后一个位置了，那么置为0  
                --count;//个数--  
                notFull.signal();//唤醒写线程  
                return x;  
            } finally {  
                lock.unlock();  
            }  
        }   
    }  

亮点在于可以指定唤醒

## CAS ##
Compare And Swap是CPU广泛支持的一种对内存中的共享数据进行操作的特殊指令。会对内存中的共享数据做原子的读写操作。

首先，CPU会将内存中将要被更改的数据域期望的值做比较，当这两个值相等时，CPU才会将内存中的数值替换为新的值。

这是一种乐观锁，认为在它修改之前没有其他线程去修改它。二Synchronized是一种悲观锁，认为在它修改之前，一定会有其他线程去修改它。

## Fork/Join Pool ##
能够接受ForkJoinTask，并得到计算结果。ForkJoinTask有两个自雷，RecursiveTask（有返回值）和RecursiveAction（无返回结果）

例子：计算超大数组所有元素的和

    public class SumTask extends RecursiveTask<Integer>{
        private static final int THRESHOLD = 50000;
        private long[] array;
        private int low;
        private int high;

        public SumTask(long[] array,int low,int high){
            this.array = array;
            this.low = low;
            this.high = high;
        }

        @Override
        protected Integer compute(){
            int sum = 0;
            if(high - low <= THRESHOLD){
                for(int i = low ; i<high ; i++){
                    sim+=array[i];
                }
            }else{
                int mid = (low+high)>>>1;
                SumTask left = new SumTask(array,low,mid);
                SumTask right = new SumTask(array,mid+1,high);

                left.fork();
                right.fork();

                sum = left.join()+right.join();
            }
            return sum;
        }
    }
