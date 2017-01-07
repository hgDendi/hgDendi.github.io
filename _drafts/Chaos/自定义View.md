# 自定义View #
步骤：

- 自定义View属性
- 在View的构造方法中获得我们自定义的属性
- 重写onMeasure
- 重写onDraw

## 定义属性

在res/values下建立attrs.xml,在里面定义我们的属性和声明我们的整个样式

```
<?xml version="" encoding=""?>
<resources>
    
    <attr name = "" format= "" />

    <declare-styleable name="">
        <attr name = ""/>
    </declare-styleable>
</resources>
```

> 

format可选字段

- string
- color
- demension
- integer
- enum
- reference
- float
- boolean
- fraction
- flag

## 获得属性

```
TypedArray typedArray = context.obtainStyledAttributes(attrs,R.styleable.);
```

```
typedArray.getXX(R.styleable.XX_name);
```

```
typedArray.recycle();
```

## 重写onMeasure

```
@Override
protected void onMeasure(int widthMeasureSpec , int heightMeasureSpec){
    int widthMode = MeasureSpec.getMode(widthMeasureSpec);
    int widthSize = MeasureSpec.getSize(widthMeasureSpec);
    int heightMode = MeasureSpec.getMode(heightMeasureSpec);
    int heightSize = MeasureSpec.getSize(heightMeasureSpec);
```

```
    setMeasuredDimension(calculateWidth(widthMode,widthSize),calculateHeight(heightMode,heightSize));
}

private int getMeasuredWidth(int widthMode, int widthSize) {
    int result = 0;
    if(widthMode==MeasureSpec.EXACTLY){
        result = widthSize;
    }else{
        result = 200;
        if(widthMode == MeasureSpec.AT_MOST){
            result = Math.min(result,widthSize);
        }
    }
    return result;
}
```



## 重写onDraw

- getMeasuredWidth
- getPaddingLeft
- mWidth

## 获取坐标方法 ##

    //获取View自身的顶边到其父布局顶边的距离
    getTop()
    getLeft()
    getBottom()
    getRight()

## 定义自定义View的过程 ##
- 自定义View的属性
- 在View的构造方法中获得我们自定义的属性
- 重写onMeasure
- 重写onDraw

### View的测量 ###
在方法onMeasure()中进行，并用到MeasureSpec类，这是一个短小精悍却功能强大的类,是一个32位的int值，其中高2位是测量的模式，低30位是测量的大小。在计算中使用位运算的原因是为了提高并优化效率。

测量的模式：

- EXACTLY
  - 当我们将空间的layout_width\layout_height或指定为match_parent属性时
- AT_MOST
  - wrap_content，此时空间的长度只要不超过父控件允许的最大尺寸即可
- UNSPECIFIED
  - View默认的onMeasure方法只支持EXACTLY方法，所以如果在自定义控件的时候不重写onMeasure()方法的话，就只能使用EXACTLY模式。

onMeasure()方法最终是调用setMeasuredDemension(int measuredWidth,int measuredHeight)方法将测量后的宽高值设置进去

    @override
    protected void onMeasure(int widthMeasureSpec,int heightMeasureSpec){
    	setMeasuredDimension(
    		measureWidth(widthMeasureSpec),
    		measureHeight(heightMeasureSpec)
    	);
    }
    
    private int measureWidth(int measureSpec){
    	int result = 0;
    	int specMode = MeasureSpec.getMode(measureSpec);
    	int specSize = MeasureSpec.getSize(measureSpec);
    
    	if(specMode == MeasureSpec.EXACTLY){
    		result = specSize;
    	}else{
    		result = 200;
    		if(specMode == MeasureSpec.AT_MOST){
    			result = Math.min(result,specSize);
    		}
    	}
    	return result;
    }

### View的绘制 ###
onDraw(Canvas canvas)
>如果在其他地方需要使用代码创建Canvas，需要传入一个bitmap

Canvas

- drawArc/Circle/Line/Oval/Path/Point/Rect/Text
- drawBitmap
- rotate旋转/scale缩放/skew倾斜/translate

Paint
>表示Canvas上的画笔
>
- setARGB、Color、Style
- setAntiAlias(true)
- setShader(Shader shader)//渲染效果

    Path path = new Path();
  path.moveTo(x,y);
  path.quadTo(middleX,middleY,x,y);
  path.lineTo(x,y);
  path.close();

  canvas.drawPath(path,paint);

>通知组件的重绘需要调用invalidate()或者在非UI线程中调用postInvalidate()

例子：
画图板的实现

双缓冲技术：
>当程序需要在指定的View上进行绘制时，程序并不直接绘制到该View组件上，而是先绘制到内存中的一个Bitmap图片（作为缓冲区）上，等到内存中的Bitmap绘制好后，再一次性的将Bitmap绘制到View组件下。

### ViewGroup的测量、绘制 ###
>ViewGroup会去管理其子View，当ViewGroup的大小为wrap_content时，就需要调用每一个子View的Measure方法来获得每一个子View的测量结果，前面所说的对View的测量就是在这时进行的。

>当子View测量完毕后，需要将子View放到核实的位置，这个过程就是View的Layout过程。ViewGroup执行Layout过程时同样是使用遍历来调用子View的Layout方法，并制定其具体显示的位置，从而来决定其布局位置。

>自定义ViewGroup时，通常回去重写onLayout()来控制其子View显示位置的逻辑。同样，如果需要支持wrap_content属性，那么它还必须重写onMeasure()方法，这点与View是相同的

 

>ViewGroup通常情况下不需要绘制，因为其本身没有需要绘制的东西，如果不是指定了ViewGroup的背景颜色，那么ViewGroup的onDraw()方法都不会被调用。但是，ViewGroup会使用dispatchDraw()方法来绘制其子View,其过程同样是通过遍历所有子View，并调用子View的绘制方法来完成绘制工作。

### 重要的回调方法 ###
- onFinishInflate()
  - 从XML加载组件后回调
- onSizeChanged()
  - 组件大小改变时回调
- onMeasure()
  - 测量大小
- onLayout()
  - 确定显示的位置
- onTouchEvent()
  - 监听触摸事件

## 使用自定义View的一般模式 ##
### 对现有控件进行拓展 ###
重写onDraw()
### 通过组合来实现新的控件 ###
创建复合控件

    // res/value/attrs.xml 通用模板定义
    <?xml version="1.0" encoding="TopBar">
    <resources>
    	<declare-styleable name="TopBar">
    		<attr name="title" format="string" />
    		<attr name="titleTextSize" format="dimension" />
    		<attr name="titleTextColor" format="color" />
    	<declare-styleable/>
    </resources>
    
    //Java中使用
    TypedArray ta = context.obtainStyledAttributes(attrs,R.styleable.TopBar);
    mTitle = ta.getString(R.styleable.TopBar_title);
    ta.recycle();
    
    第三方控件需要使用如下代码来引入名字空间
    xmlns:custom = "http://schemas.android.com/apk/res-auto"
    
    <com.imooc.systemwidget.TopBar
    	android:id = "@+id/topBar"
    	custom:title="自定义标题"
    	custom:titleTextColor="#FFFFFF"
    </com.xys.mytopbar.Topbar>
### 重写View来实现全新的控件 ###

### 事件拦截机制 ###
假设有ViewGroupA,ViewGroupB,View

    ViewGroupA dispatchTouchEvent()
    ViewGroupA onInterceptTouchEvent()
    ViewGroupB dispatchTouchEvent()
    ViewGroupB onInterceptTouchEvent()
    View       onTouchEvent()
    ViewGroupB onTouchEvent()
    ViewGroupA onTouchEvent()

Flase为继续、true为拦截

	//ViewGroupB的onInterceptTouchEvent()返回true时

    ViewGroupA dispatchTouchEvent()
    ViewGroupA onInterceptTouchEvent()
    ViewGroupB dispatchTouchEvent()
    ViewGroupB onInterceptTouchEvent()
    ViewGroupB onTouchEvent()
    ViewGroupA onTouchEvent()

>

    //View的onTouchEvent()返回true时

    ViewGroupA dispatchTouchEvent()
    ViewGroupA onInterceptTouchEvent()
    ViewGroupB dispatchTouchEvent()
    ViewGroupB onInterceptTouchEvent()
    ViewGroupA onTouchEvent()
    View       onTouchEvent()
