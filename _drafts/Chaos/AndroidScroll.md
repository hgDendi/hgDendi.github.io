# Android Scroll #
>
## Android坐标系和视图坐标系 ##
>Android坐标系是绝对坐标，使用getRawX()和getRawY()得到

>视图坐标系是以父视图左上角为坐标原点，通过getX()和getY()得到

## MotionEvent ##
	//单点触摸按下动作
    public static final int ACTION_DOWN = 0;
	//单点触摸离开动作
	public static final int ACTION_UP = 1;
	//触摸点移动
	public static final int ACTION_MOVE = 2;
	//触摸动作取消
	public static final int ACTION_CANCEL = 3;
	//触摸动作超出边界
	public static final int ACTION_OUTSIDE = 4;
	//多点触摸按下动作
	public static final int ACTION_POINTER_DOWN = 5;
	//多点离开动作
	public static final int ACTION_POINTER_UP = 6;

通常会在onTouchEvent(MotionEvent event)中通过event.getAction()方法来获取触控时间的类型，并使用switch-case来进行筛选

## 实现滑动 ##

>基本思想都是：触摸View时，系统记下当前触摸点坐标；当手指移动时，系统记下移动后的触摸点坐标，从而获取到相对于前一次坐标点的偏移量，并通过偏移量来修改View的坐标，这样不断重复，从而实现滑动效果。

### Layout() ###
    @override
	public boolean onTouchEvent(MotionEvent event){
		int rawX = (int)(event.getRawX());
		int rawX = (int)(event.getRawX());
		switch(event.getAction()){
			case MotionEvent.ACTION_DOWN:
				lastX = rawX;
				lastY = rawY;
				break;
			case MotonEvent.ACTION_MOVE:
				int offsetX = rawX - lastX;
				int offsetY = rawY - lastY;
				layout(getLeft()+offsetX,
					getTop()+offsetY,^^^);
				lastX = rawX;
				lastY = rawY;
				break;
		}
		return true;
	}
### offset() ###
	//当然layout()方法可以通过下面的方法代替
	offsetLeftAndRight(offsetX);
	offsetTopAndBottom(offsetY);

### LayoutParams ###

	//也可以使用ViewGroup.MarginLayoutParams来实现
	LinearLayout.LayoutParams layoutParams = (LinearLayout.LayoutParams)getLayoutParams();
	layoutParams.leftMargin = getLeft() + offsetX;
	layoutParams.topMargin = getTop() + offsetY;
	setLayoutParams(layoutParams);

### scrollTo与scrollBy ###

	scrollTo(x,y);
	scrollBy(offsetX,offsetY);

一般在ViewGroup来使用scroll方法
>View使用，移动的是View的content
>
>ViewGroup使用，移动的是所有子View

>scrollBy中的参数若为正数，那么View将向坐标轴 **负方向** 移动

### Scroller ###
    
    mCcroller = new Scroller(context);
	
	@override
	public void computeScroll(){
		super.computeScroll();
		//判断Scroller是否执行完毕
		if(mScroller.computeScrollOffset()){
			((View)getParent()).scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
			//通过重绘不断调用computeScroll
			invalidate();
		}
	}
>只能通过invalidate()调用draw()调用computeScroll()来间接调用computeScroll()方法，所以需要在代码中使用invalidate()方法实现循环获取scrollX和scrollY的目的。而在模拟过程结束后，scroller.computeScrollOffset()方法会返回false

	//滚动后将View滑动到原位置
    case MotionEvent.ACTION_UP:
		View viewGroup = ((View)getParent());
		mScroller.startScroll(
			viewGroup.getScrollX(),
			viewGroup.getScrollY(),
			-viewGroup.getScrollX(),
			-viewGroup.getScrollY()
		);
		invalidate();
	break;

## 属性动画 ##

### ViewDragHelper ###