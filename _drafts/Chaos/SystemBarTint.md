# SystemBarTint #
沉浸式状态栏


>Android 4.4以上版本使用

## 使用方法 ##

xml布局的顶层布局中加入两行代码

    //根据系统窗口（如status bar）来调整自己的布局，如果值为true，就会调整view的padding属性来给system windows留出空间
    android:fitsSystemWindows = "true"
    //空间的绘制区域是否在padding里，若false表示可以在空间外面显示
    android:clipToPadding = "false"


Activity代码中更改
    protected void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        setContentView(XX);

        if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT){
            setTranslucentStatus(true);
        }

        SystemBarTintManager tintManager = new SystemBarTintManager(this);
        tintManager.setStatusBarTintEnabled(true);
        tintManager.setNavigationBarTintEnabled(true);

        tintManager.setTintColor(Color.parseColor("#000000"));
    }