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

# 耗电优化

*   执行操作前先进行网络状态判断

*   使用高效率的数据格式与解析方法如JSON

*   传输用户返回或者下载升级包时采用压缩数据进行传输且延迟到设备充电和WIFI状态

*   有必要情况下通过WakeLock和JobScheduler来控制一些逻辑操作达到省电优化

*   对定位要求不高的场景使用网络定位而不是GPS定位

*   定时任务使用AlarmManger

*   尽可能减少网络请求次数和减少网络请求时间间隔

*   后台任务要尽可能少的唤醒CPU，比如IM通信的长连接心跳时间间隔，一些应用的后台定时唤醒时间间隔

    ​