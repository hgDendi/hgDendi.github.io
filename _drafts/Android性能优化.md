# 应用UI卡顿原因

## 分析方法

### HierarchyViewer

### GPU过度绘制

1.  蓝
2.  绿
3.  淡红
4.  红色

### GPU呈现模式图

*   蓝色 绘制DisplayList的时间
*   红色 OpenGL渲染DisplayList的时间
*   黄色 CPU等待GPU处理的时间

```
adb shell dumpsys gfxinfo [packageName]
```
### 使用Lint检测

### 使用Memory与AllocationTracker进行检测（GC会造成卡顿）

触发GC原因：

*   GC_MALLOC
    *   内存分配失败
*   GC_CONCURRENT
    *   分配对象大小超过限定值
*   GC_EXPLICIT
    *   显示调用，如System.gc()
*   GC_EXTERNAL_ALLOC
    *   外部内存分配失败

### 使用raceview和dmtracedump进行分析优化

```
dmtracedump -g result.png target.trace
```
### Systrace进行性能优化

### 使用traces.txt文件分析ANR

```
adb pull /data/anr/traces.txt ./
```

# UI性能分析总结

## 布局优化

*   尽量使用include、merge、ViewStub标签
*   不使用冗余嵌套和过于复杂布局
*   尽量使用GONE替换INVISIBLE
*   使用weight后尽量将width和height设置为0
*   item存在复杂嵌套关系时用自定义View替代，减少measure与layout次数

## 列表及Adapter

*   复用
*   滑动过程中不加载图片

## 背景和图片等内存分配

*   减少不必要的背景设置
*   图片尽量压缩处理现实
*   避免频繁内存抖动

## 自定义View等绘图与布局优化

*   避免在draw、measure、layout中做耗时及耗内存操作

## 避免ANR

# 内存优化

## 复写onTrimMemory

```Java
@Override
public void onTrimMemory(int level) {
   if (level >= ComponentCallbacks2.TRIM_MEMORY_BACKGROUND) {
       clearCache();
   }
}
```

## 大图片资源、全屏图片资源要不放在assets下，要不放在nodpi下，要不都带，否则缩放会带来额外耗时与内存问题

## 静态化内部类，使用WeakReference引用外部类

# 耗电优化

*   执行操作前先进行网络状态判断
*   使用高效率的数据格式与解析方法如JSON
*   传输用户返回或者下载升级包时采用压缩数据进行传输且延迟到设备充电和WIFI状态
*   有必要情况下通过WakeLock和JobScheduler来控制一些逻辑操作达到省电优化
*   对定位要求不高的场景使用网络定位而不是GPS定位
*   定时任务使用AlarmManger
*   尽可能减少网络请求次数和减少网络请求时间间隔
*   后台任务要尽可能少的唤醒CPU，比如IM通信的长连接心跳时间间隔，一些应用的后台定时唤醒时间间隔

# 基础相关

## 选择合适的数据结构

## 编码习惯

*   尽量简化、不要做不需要的操作

*   static比非static调用提升15~20%的速度

*   尽量使用static final，在编译时就直接将常量替换代码中使用的位置，而不需要调用<clinit>来创建static方法

*   类内直接访问变量而不是方法，对方法的调用开销远大于对变量的直接访问，7倍开销

*   尽量避免使用float

*   使用乘法代替除法

*   尽量使用系统sdk中提供的方法，而非自己实现。比如String.indexOf()相关的API，Dalvik将会替换为内部方法；System.arrayCopy()在NexusOne手机上回比我们上层写的类似的代码执行速度快9倍

*   谨慎编写NDK
    *   需要额外开销在维持Java-native的通信
    *   native中创建的资源在native heap上，因此需要主动的释放，但也因此对应用而言没有OOM的问题，且不需要考虑GC时锁线程带来的掉帧，如Facebook的Facebook的Fresco就是将图片缓存到Native Heap中

*   重复访问的变量赋值为本地变量
  *   在没有JIT的设备中，访问本地化变量相对于成员变量会快20%，但是在存在JIT的设备中，两者速度差不多

*   尽量使用Iterable而不是通过长度判断来进行遍历

    *   ```java
        // 这种性能是最差的，JIT也无法对其优化。
        public void zero() {
            int sum = 0;
            for (int i = 0; i < mArray.length; ++i) {
                sum += mArray[i].mSplat;
            }
        }
        // 相对zero()来说，这种写法会更快些，在存在JIT的情况下速度几乎和two()速度一样快。
        public void one() {
            int sum = 0;
            // 1) 通过本地化变量，减少查询，在不存在JIT的手机下，优化较明显。
            Foo[] localArray = mArray;
            // 2) 获取队列长度，减少每次遍历访问变量的长度，有效优化。
            int len = localArray.length;
            for (int i = 0; i < len; ++i) {
                sum += localArray[i].mSplat;
            }
        }
        // 在无JIT的设备中，是最快的遍历方式，在存在JIT的设备中，与one()差不多快。
        public void two() {
            int sum = 0;
            for (Foo a : mArray) {
                sum += a.mSplat;
            }
        }
        ```

# 网络优化

## 策略层面

*   If-Modified-Since与Last-modified
    *   第一次请求服务端在头部带下来Last-modified
    *   之后客户端请求在请求头中通过If-Modified-Since带上值
    *   判断最后一次修改的时间距离目前数据没有修改过，就直接返回304 NOT MODIFIED
*   Etag与If-None-Match
    *   服务器头部通过Etag带下来请求数据的hash值
    *   之后的请求，在服务头中通过If-None-Match带上之前服务端返回的Etag值

# 其他

*   预加载资源
*   做好监控crash、anr、掉帧、异常状态监控统计上报
*   文件存储放在Context#getExternalFilesDir()，在应用卸载时，会随即删除
*   gradle的shrinkResources与minifyEnabled
*   控制合理加载资源的时间区间，比如activity不可见时，就暂停所有加载