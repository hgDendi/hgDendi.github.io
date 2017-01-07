# Activity过渡动画 #

## 提供了三种Transition类型 ##
- 进入
    - 决定Activity中的所有的视图怎么进入屏幕
- 退出
    - 决定一个Activity中的所有视图怎么退出屏幕
- 共享元素
    - 决定两个Activities之间的过渡，怎么共享他们的视图

### 进入、退出 ###
- explode
    - 从屏幕中间进或出，移动视图
- slide
    - 滑动，从屏幕边缘进或出，移动视图
- fade
    - 淡出，通过改变屏幕上的视图的不透明度达到添加或者移除视图

### 共享元素 ###
- changeBounds
    - 改变目标视图的布局边界
- changeClipBounds
    - 裁剪目标视图边界
- changeTransform
    - 改变目标视图的缩放比例和旋转角度
- changeImageTransform
    - 改变目标图片的大小和缩放比例

## 代码 ##

### 普通动画 ###
    //ActivityA
    startActivity(intent,ActivityOptions.makeSceneTransitionAnimation(this).toBundle());

    //ActivityB先声明后设置
    A在代码中声明
    getWindow().requestFeature(Window.FEATURE_CONTENT_TRANSITIONS);
    B在xml中声明
    <item name="android:windowContentTransitions">true</item>

    具体设置:
    getWindow().setEnterTransition(new Explode());
    getWindow().setExitTransition(new Slide());

### 淡出效果 ###

    在ActivityA和ActivityB中同时定义
    android:transitionName="XXX"

    如果只需要一个共享元素，可以在ActivityA中：
    startActivity(intent,ActivityOptions.makeSceneTransitionAnimation(this,view,"share").toBundle());

    如果需要多个共享元素
    startActivity(intent,
        ActivityOptions.makeSceneTransitionAnimation(
            this,
            Pair.Create(view,"share"),Pair.create(fab,"fab")
        ).toBundle()
    );