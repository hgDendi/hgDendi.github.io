# Android绘图机制 #
## 基础概念 ##
- 屏幕大小
	- 对角线长度，如5.5寸手机
- 分辨率
	- 手机屏幕的像素点个数
- PPI
	- 每英寸像素Pixels Per Inch， 又被称为Dots Per Inch，由对角线的像素点除以屏幕的大小得到

>度量单位
>
- dp\dip
	- 设备独立像素
- px、pixels
	- 像素，不同设备显示效果相同
- pt、point
	- 标准长度单位
- sp:scaled pixels
	- 放大像素，主要用于字体显示
- in
	- 英寸
- mm

## Layer图层 ##
>使用saveLayer()方法来创建一个图层，图层是基于栈结构进行管理的

>通过saveLayer()方法、saveLayerAlpha()方法将一个图层入栈，使用restore()方法、restoreToCount()方法将一个图层出栈。

>入栈时，后面所有的操作都发生在这个图层上，而出栈的时候，则会把图像绘制到上层Canvas上。

    @override
	protected void onDraw(Canvas canvas){
		canvas.drawColor(Color.WHITE);
		mPaint.setColor(Color.BLUE);
		canvas.drawCircle(150,150,100,mPaint);

		canvas.saveLayerAlpha(0,0,400,400,127,LAYER_FLAGS);
		mPaint.setColor(Color.RED);
		canvas.drawCircle(200,200,100,mPaint);
		canvas.restore();
	}

## 组件 ##
- Activity
	- 基本的页面单元,Activity包含一个Window,window上可以绘制各种view
- View
	- 最基本的UI组件，表示屏幕上的一个矩形区域
- Window
	- 表示顶层窗口，管理界面的显示和事件的相应；每个Activity均会创建一个
- PhoneWindow
	- Activity和整个View系统交互的接口
	- phoneWindow是把一个FrameLayout进行了一定的包装，并提供了一组通用的窗口操作接口
- DecorView
	- 是PhoneWindow中的一个内部类
- ViewRoot

### 绘制流程 ###
整个View树的绘图流程是在ViewRoot.java的performTraversals()函数展开的，该函数做的执行过程可简单概括为根据之前设置的状态：


- 判断是否需要重新计算视图大小(measure)
- 判断是否需要安置视图的位置（layout）
- 是否需要重绘（draw）

#### measure ####
为整个View树计算实际的大小，即设置实际的高(mMeasuredHeight和mMeasureWidth),每个View控件的实际宽高都是由父视图和本身视图决定的
#### layout ####
根据子视图的大小以及布局参数将View树放到合适的位置上
#### draw ####
ViewRoot对象的performTraversals()方法调用draw()方法发起绘制该View树，判断DRAWN位，看是否需要重绘
#### requestLayout() ####
调用measure()和layout()