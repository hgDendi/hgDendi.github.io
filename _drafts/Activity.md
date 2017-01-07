# Activity

## 生命周期

![complete_android_fragment_lifecycle](/Users/dendich/Desktop/complete_android_fragment_lifecycle.png)

### 系统资源的释放

一般将系统资源的释放放在onPause中，比如Camera、Sensor、Receivers



## 启动模式

### Activity启动模式

* lunchMode
  * standard
  * singleTop
  * singleTask
    * 检查当前栈中是否存在需要启动的Activity, 清理栈顶直到此Activity位于栈顶
  * singleInstance
    * 会出现在一个新的任务栈中，且此任务栈中只存在这一个Activity
    * 此新的任务栈可被多个应用共享
    * 应用于需要与程序分离的界面，如调用紧急呼叫，就是使用这种启动模式
* clearTaskOnLaunch
  * **只对根Activity生效**
  * 每次返回该TASK，都会将该TASK之上的所有其他Activity清除
* finishOnTaskLaunch
  * 当用户离开这个TASK，再返回的时候，该Activity就会被finish掉
* alwaysRetainTaskState
  * **只对根Activity生效**
  * 所在的TASK栈不接受任何清理命令，一直保持Task状态
* allowTaskReparenting
  * 在应用推到后台时，能否从启动它的哪个Task移动到具有公共affinity的task
  * 比如app启动了web浏览器的一个Activity,这个activity此时在app的task上，当app退到后台再进入前台，会发现这个activity已经到了浏览器应用的task中了

### IntentFlag启动模式

* NEW_TASK
  * Service因为不依附任何栈，所以若从Service中创建新的Activity需要使用此方法
* SINGLE_TOP
  * 同single_top
* CLEAR_TOP
  * 同single_task
* NO_HISTORY
  * 该Activity启动其他Activity后，就消失了

### Android任务栈

> Android任务栈被称为一个TASK,一个TASK中的Activity可以来自不同的APP，同一个App的Activity也可能不在一个Task中

* 不同TASK栈之间**不能传递数据**，只能由Intent传递
  * 比如调用startActivityForResult启动一个SingleInstance模式的Activity,会立即返回Activity.RESULT_CANCELED

​	

## 内存重启

**原则** View的记录信息由View记录，Fragment的记录信息由Fragment记录。

* intent中的信息会被自动保存
* 其他需要持久化保存的信息需要在onSavedInstantceState()中保存