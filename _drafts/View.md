

> A view occupies a rectangular area on the screen and is responsible for drawing and event handling
>
> View占据了屏幕上一个矩形区域，并负责他的绘制和事件监听

要点：

* 矩形区域
* 绘制和事件监听

### onMeasure

> Called to determine the size requirements for this view and all of its children

* 一般调用setMeasuredDimension()

#### MeasureSpec

> ```
> is used by views to tell their parents how they want to be measured and positioned.
>
> MeasureSpecs are used to push requirements down the tree from parent to
>  child.
> ```

父容器的规则+View的LayoutParams = MeasureSpec

* 如果父View是match_parent,子View是match_parent,则子View的MeasureSpec为AT_MOST

```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) { 					    			    setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
}

public static int getDefaultSize(int size, int measureSpec) {
    int result = size;
    int specMode = MeasureSpec.getMode(measureSpec);
    int specSize = MeasureSpec.getSize(measureSpec);

    switch (specMode) {
    case MeasureSpec.UNSPECIFIED:
    	//getSuggestedMinimumWidth()
        result = size;
        break;
    case MeasureSpec.AT_MOST:
    	//子View为wrap_content时为父view的size
    case MeasureSpec.EXACTLY:
        result = specSize;
        break;
    }
    return result;
}
```

### onLayout

> Called when this view should assign a size and position to all of its children

* 一般由ViewGroup重写，调用childView的layout方法

#### requestLayout



### onSizeChange

> Called when the size of this view has changed

* 此处的size不一定与getMeasuredSize()一致，需要看layout的策略


### Draw

六步骤

1. Draw the background
2. If necessary, save the canvas' layers to prepare for fading
3. Draw view's content (onDraw)
4. Draw children
5. If necessary, draw the fading edges and restore layers
6. Draw decorations (scrollbars for instance)


### onDraw

> Called when the view should render its content

#### Layer

相当于PS中图层的操作

```
//新建图层，将图层入栈，后续的操作就都发生在图层上
canvas.saveLayerAlpha();
canvas.saveLayer();

//图层出栈
canvas.restore();
canvas.restoreToCount();
```

#### setChildrenDrawingOrderEnabled

#### invalidate



### onTouchEvent

> Called when a touch screen motion event occurs

注意如果在其中生成点击事件，调用onClickListener，最好使用performClick()，保持系统内部一致性

#### TouchSlop

系统能识别出的最小滑动距离

```
ViewConfiguratin.get(this).getScaledTouchSlop();
```

#### Velocity Tracker

得到滑动速度

```java
//init()
VelocityTracker velocityTracker = VelocityTracker.obtain();
velocityTracker.addMovement(event);

//int参数是以毫秒为单位的时间
velocityTracker.computeCurrentVelocity(int);
velocityTracker.getXVelocity();

//destroy()
velocityTracker.clear();
velocityTracker.recycle();
```

#### GestrueDetector

辅助检测用户的手势动作

```java
GestureDetector gestureDetector = new GestureDetector(new GestureDetector.OnGestureListener() {
    @Override
    public boolean onDown(MotionEvent e) {
        return false;
    }

    @Override
    public void onShowPress(MotionEvent e) {

    }

    @Override
    public boolean onSingleTapUp(MotionEvent e) {
        return false;
    }

    @Override
    public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
        return false;
    }

    @Override
    public void onLongPress(MotionEvent e) {

    }

    @Override
    public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
        return false;
    }
});

//add this to onTouchEvent
@Override
public boolean onTouchEvent(MotionEvent event) {
    boolean consume = mGestureDetector.onTouchEvent(event);
    return consume;
}
```

### DrawingCache



### 滑动

* scrollTo/By
* ObjectAnimator
* 改变MarginLayoutParams
* layout
* Scroller

#### Scroller

原理是不断通过不断的computeScroll 不断的重新绘制

```java
//init()
Scroller mScroller = new Scroller(context);

//invoke
mScroller.startScroll(getScrollX(),getScrollY(),-50,-50);
invalidate();

//override View#computeScroll()，此方法会在View的draw方法中调用
@Override
public void computeScroll(){
  if(mScroller.computeScrollOffset()){
    //true means has not finished
    scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
  	invalidate();
  }
  
}
```

#### 滑动冲突

dispatchTouchEvent()

onInterceptTouchEvent()

onTouchEvent

#### 父控件拦截

一般重写onInterceptTouchEvent(MotionEvent ev)



### 四大构造方法

* View(Context)
  * 代码中构造
* View(Context,AttributeSet)
  * style默认是使用Context的主题
* View(Context,AttributeSet,int)
* View(Context,AttributeSet,int,int)
  * 归根结底XML构造方法都会调用到此方法
  * 这里面有TypedArray的标准读取方法，自定义View的模板可以直接模仿此处

=======================================================

一个有趣的地方，因为View有很多属性，比如clickable、focusable等等，可能高达几十上百个。

这里并没有采用简单的boolean值，而是采用一个全局的int变量和对应的静态BITMASK进行判断，

比如：

```java
static final int PFLAG_FOCUSED = 0x00000002;
int mPrivateFlags;
public boolean hasFocus() {
        return (mPrivateFlags & PFLAG_FOCUSED) != 0;
}
```

这样只需要极少量的mFlags，和所有View公用的static bitmask，就可以指示出极多的属性值，是一个非常好的思路。

```
public static final int MASK = 0x00000006;
public static final int FLAG_A = 0x00000000;
public static final int FLAG_B = 0x00000002;
public static final int FLAG_C = 0x00000004;
```

Android中可以使用IntDef注解来声明常量值

```
public class Toast {
    static final String TAG = "Toast";
    static final boolean localLOGV = false;

    /** @hide */
    /*定义部分*/
    @IntDef({LENGTH_SHORT, LENGTH_LONG})
    @Retention(RetentionPolicy.SOURCE)
    public @interface Duration {}

    public static final int LENGTH_SHORT = 0;
    public static final int LENGTH_LONG = 1;

    ...

    /*作为类型使用时*/
     /**
     * Set how long to show the view for.
     * @see #LENGTH_SHORT
     * @see #LENGTH_LONG
     */
    public void setDuration(@Duration int duration) {
        mDuration = duration;
    }

    /*做为返回值时*/
    /**
     * Return the duration.
     * @see #setDuration
     */
    @Duration
    public int getDuration() {
        return mDuration;
    }

}
```



======================================================

对于聚簇的Listener，封装成一个实体

```
static class ListenerInfo {
        protected OnCreateContextMenuListener mOnCreateContextMenuListener;

        ************

        private OnSystemUiVisibilityChangeListener mOnSystemUiVisibilityChangeListener;

        OnApplyWindowInsetsListener mOnApplyWindowInsetsListener;
    }

    ListenerInfo mListenerInfo;
```

=========================================================

StringBuilder

1. 指定大小，因为内部是使用char[]存储，默认大小为16，变长时候和ArrayList一样的策略，申请新char[]并逐个复制。
2. 相比String的+操作，虽然编译器有优化，但是在循环语句中还是会生成多个对象

============================================================

方法中使用临时变量

1.保持原子性

2.栈区速度比堆更快

```java
/**
* Returns the width of the vertical scrollbar.
*
* @return The width in pixels of the vertical scrollbar or 0 if there
*         is no vertical scrollbar.
*/
public int getVerticalScrollbarWidth() {
    ScrollabilityCache cache = mScrollCache;
    if (cache != null) {
        ScrollBarDrawable scrollBar = cache.scrollBar;
        if (scrollBar != null) {
            int size = scrollBar.getSize(true);
            if (size <= 0) {
                size = cache.scrollBarSize;
            }
            return size;
        }
        return 0;
    }
    return 0
}
```

### measuredWidth和width的区别

* measuredWidth

  > ```
  > These dimensions define how big a view wants to be within its parent (see <a href="#Layout">Layout</a> for more details.
  > ```

  测量后的值，是View期望分配的值，是onMeasure中setMeasuredDimension设置的值

* width

  > ```
  > These dimensions define the actual size of the view on screen, at drawing time and
  > after layout. These values may, but do not have to, be different from the
  > measured width and height.
  > ```

  layout之后的值，表示画面在屏幕上的真是宽高，未必和measuredWidth相同

  ​	比如自定义View的策略就是layout（0,0,50,50）,而不管measuredDiemnsion

### padding和margin

> Even though a view can define a padding, it does not provide any support for
>
> margins. However, view groups provide such a support. 

故而尽量使用padding

### Picture

### Region

#### 如果想要监听一个区域，而不是View的扇形区域的点击事件，该怎么办？

传统的setOnClickListener只是监听整个View的矩形区域，想要监听View的特定区域需要使用Region判断点击事件。