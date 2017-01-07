# Android横竖屏 #

## 禁止屏幕的切换 ##
    
    android:screenOrientation

## 横竖屏切换更改布局 ##

>在res目录下建立layout-land和layout-port目录，响应的layout文件名不变

## onConfigChange() ##
在Manifest里对activity加上如下配置就不会重走销毁和创建了

    android:configChanges="orientation|keyboardHidden|screenSize"
但是需要注意的是，在onConfigChange()中获取不到新的Layout和控件的尺寸位置信息，必须通过消息异步或者延时调用。
## 数据的保存 ##

数据保存在onSaveInstanceState(Bundle outState)
读取在onCreate(Bundle savedInstanceState)