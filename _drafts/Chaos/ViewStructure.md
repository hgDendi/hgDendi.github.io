

## 控件架构

- phoneWindow
  - DecorWindow
    - DecorView
      - TitleView
      - ContentView

这里面所有View的监听事件，都通过WindowManagerService来进行接收，并通过Activity对象来回调对应的onClickListener

如果调用requestWindowFeature(Window.FEATURE_NO_TITLE)，视图树的布局中就仅有Content了，但是requestWindowFeature必须在setContentView之前才能生效

在onCreate()中调用setContentView()后，ActivityManagerService会回调onResume()，此时系统才会把整个DecorView添加到PhoneWindow上，最终完成界面的绘制。


