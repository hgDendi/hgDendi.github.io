# 屏幕方向适配相关 #

## 横竖屏切换时Activity的生命周期 ##

1、默认情况下，切屏会重新调用各个生命周期

2、设置Activity的android:configChanges=”orientation|keyboardHidden”时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法（3.2之前还是会调用各个生命周期）

如果横竖屏的界面布局不同，可以在res下新建layout-land和layout-port目录，然后把布局文件扔到这两个目录文件中

## Configuration ##
描述了设备的所有配置信息，会影响到应用程序检索的资源。包括了用户指定的选项(locale和scaling)，也包括设备本身的配置(例如input modes,screen size and screen orientation)，可以在该类里查看所有影响Configuration Change的属性

常见的银发Configuration Change的属性：
- 横竖屏切换
    - android:configChanges="orientation"
- 键盘可用性
    - android:configChanges="keyboardHidden"
- 屏幕大小变化
    - android:configChanges="screenSize"
- 语言的更改
    - android:configChanges="locale"

## 可能引发的问题 ##

### 重走生命流程

界面中用户选择了checkbox和radiobutton选项或者通过网络请求显示在界面上的数据在屏幕旋转后Activity被destroy-recreate，这些空间上被选择的状态和界面上的数据都会消失。

进入某个Activity时加载页面进行网络请求，此时旋转屏幕会重新创建网络请求，这样的用户体验非常不好。

伴随异步操作显示一个progressDialog的话，异步任务未完成去旋转屏幕，程序会因为Activity has leaked window而终止。而当old activity被销毁后，线程执行完毕后还是会把结果返回给old activity而非新的activity

建议要override onSaveInstanceState()方法

### onConfigChange()

在onConfigChange()中**获取不到新的Layout和控件的尺寸位置信息**，必须通过消息异步或者延时调用

不可以动态加载xml，可能会有很多硬代码