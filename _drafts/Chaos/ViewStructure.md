

## 控件架构

- phoneWindow
  - DecorWindow
    - DecorView
      - TitleView
      - ContentView

这里面所有View的监听事件，都通过WindowManagerService来进行接收，并通过Activity对象来回调对应的onClickListener

如果调用requestWindowFeature(Window.FEATURE_NO_TITLE)，视图树的布局中就仅有Content了，但是requestWindowFeature必须在setContentView之前才能生效

在onCreate()中调用setContentView()后，ActivityManagerService会回调onResume()，此时系统才会把整个DecorView添加到PhoneWindow上，最终完成界面的绘制。

## onMeasure()

### MeasureSpec

- 前2位，测量的模式
  - EXACTLY
    - 具体数值
    - match_parent
  - AT_MOST
    - wrap_content
  - UNSPECIFIED
    - ​
- 后32位，测量的大小

 

系统默认的View的onMeasure(int widthMeasureSpec,int heightMeasureSpec)只支持EXACTLY模式。所以其他情况需要重写onMeasure()#setMeasuredDimension(int measuredWidth,int measuredHeight)



### ViewGroup的测量

​	当ViewGroup的大小为wrap_content时，ViewGroup就需要对子View进行遍历（调用View的measure方法）。其他模式下则会通过具体的指定值来设置自身的大小。



## 事件拦截

- ViewGroup
  - dispatchTouchEvent
  - onInterceptTouchEvent
  - onTouchEvent
- View
  - diapatchTouchEvent
  - onTouchEvent



#### TouchEvent

- ACTION_DOWN
  - ACTION_MOVE,ACTION_UP发生的前提是曾经发生了ACTION_DOWN,如果没有消费ACTION_DOWN,系统不会捕获ACTION_MOVE和ACTION_UP。onTouchEvent的事件回传到父控件置灰发生在ACTION_DOWN中。ACTION_MOVE和ACTION_UP会跟随ACTION_DOWN进行处理
- ACTION_UP
- ACTION_MOVE
- ACTION_CANCEL
  - ACTION_CANCEL是收到事件前驱后，后续事件被父空间拦截的情况下产生的
- ACTION_OUTSIDE
- ACTION_CANCEL
- ACTION_POINTER_DOWN
- ACTION_POINTER_UP



## 坐标系

- Android坐标系
  - getRawXY
  - getLocationOnScreen
- 视图坐标系
  - getXY
  - layout(l,t,r,b)
  - getLeft



## 位移方法

- 在onEvent中调用layout方法

- 使用offsetLeftAndRight()或者offsetTopAndBottom

- 通过getLayoutParams和setLayoutParams，修改LayoutMarginParams

  ```Java
  ViewGroup.MarginLayoutParams layoutParams = (ViewGroup.MarginLayoutParams) getLayoutParams;
  layoutParams.leftMargin = getLeft() + offset;
  layoutParams.topMargin = getTop() + offset;
  setLayoutParams(layoutParams);
  ```

- scrollTo、scrollBy

  - 在View中移动的是content
  - 如果在ViewGroup中，则是移动的所有子View
  - 移动的是盖板，所以如果位移是正值，将会看到屏上的物体向左、上移动
  - 是一个直接完成的位移，瞬间完成

- Scroller类

  - 通过Scroller类可以实现平滑移动的效果

  ```java
  mScroller = new Scroller(context);

  //系统在draw()方法中调用此方法
  //但因为其不会自动调用，所以需要invalidate()>draw()>computeScroll()
  @Override
  public void computeScroll(){
  	super.computeScroll();
  	if(mScroller.computeScrollOffset()){
        	((View)getParent).scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
        	invalidate();
  	}
  }

  //onTouchEvent
  case MotionEvent.ACTION_UP:
  	View viewGroup = ((View)getParent());
  	(滑动到原位置)
  	mScroller.startScroll(
  		viewGroup.getScrollX(),
  		viewGroup.getScrollY(),
  		-viewGroup.getScrollX(),
  		-viewGroup.getScrollY())
  	invalidate();
  	break;
  ```


- 属性动画

- ViewDraggerHelper

  - 基本可以实现不同的滑动、拖放需求
  - 写在ViewGroup中
  - 内部也是使用Scroller来实现的，所以也需重写computeScroll()

  ```java
  mViewDraggerHelper = ViewDragHelper.create(this,callback);

  @Override
  public boolean onInterceptTouchEvent(MotionEvent ev){
    	return mViewDraggerHelper.shouldInterceptTouchEvent(ev);
  }

  @Override
  public boolean onTouchEvent(MotionEvent event){
    	mViewDragHelper.processTouchEvent(event);
    	return true;
  }

  @Override
  public void computeScroll(){
    	if(mViewDragHelper.continueSettling(true)){
        	ViewCompat.postInvalidateOnAnimation(this);
    	}
  }

  private ViewDragHelper.Callback callback = new ViewDragHelper.Callback(){

    	@Override
    	public boolean tryCaptureView(View child , int pointerId){
    		//指定哪一个子View可以被移动
        	return child == XXXView;
    	}
    	
    	//返回值为0，表示在该方向上不发生滑动
    	@Override
    	public int clampViewPositionVertical(View child,int top,int dy){
        	return top;
    	}
    	
    	@Override
    	public int clampViewPositinoHorizontal(View child,int left,int dx){
        	return left;
    	}
    	
    	//结束拖动后调用
    	@Override
    	public void onViewReleased(View releasedChild,float xvel,float yvel){
        	super.onViewReleased(releasedChild,xvel,yvel);
        	if(mMainView.getLeft()<500){
            	mViewDragHelper.smoothSlideViewTo(mMainView,0,0);
            	ViewCompat.postInvalidateOnAnimation(this);
        	}
    	}
  }

  ```

