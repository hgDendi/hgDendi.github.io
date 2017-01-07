# View #

### LayoutInflater ###

使用Android提供的pull解析方法来解析布局文件


	LayoutInflager layoutInflater = LayoutInflater.from(context);
	LayoutInflater layoutInflater = (LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);



	/**
	*	如果root为null,attachToRoot将失去作用
	*	如果root不为null，attachToRoot为true，则会给加载的布局文件指定一个父布局，即root
	*	如果root不为null，attachToRoot为false，则会将布局文件最外层的所有layout属性进行设置，当view被添加时，这些layout属性自动生效
	*	默认attachToRoot为true
	*/
    inflate(int resource,ViewGroup root,boolean attachToRoot)

### setContentView ###

是利用LayoutInflater进行渲染的

会自动在布局文件的最外层嵌套一个FrameLayout

### onMeasure ###
onMeasure()方法用于测量视图的大小

measure()是final的，但是onMeasure()却是可以进行重写的

View系统的绘制流程会从ViewRoot的performTraversals()方法中开始，在其内部调用View的measure方法。measure()接收两个参数，widthMeasureSpec和heightMeasureSpec

#### MeasureSpec ####
都是由父视图计算后传递给子视图的

- specSize
- specMode
	- EXACTLY
		- 子视图的大小由specSize的值来决定
	- AT_MOST
		- 子视图最多只能是specSize中指定的大小
	- UNSPECIFIED

### onLayout ###

layout()方法中，首先会调用setFrame()方法来判断视图的大小是否发生过变化，以确定是否有必要对当前的视图进行重绘。如果需要重绘则调用onLayout方法。

onLayout()方法View中是空方法，ViewGroup中为抽象方法，每个ViewGroup的子类必须重写这个方法。

### getMeasuredHeight和getHeight ###
- getMeasuredHeight/Width
	- 前者在measure()过程结束后就可以获取到了，是通过setMeasuredDimension()方法进行设置的
- getHeight/Width
	- getWidth()在layout过程结束后才能获取到，是用视图右边的坐标减去左边的坐标得到

### onDraw ###


### Android自定义控件 ###

- 自绘控件
	- 自己定义onDraw()
- 组合控件
	- 使用系统原生控件
	- 如定义一个RelativeLayout,里面有一个Button和TextView，定义一个类TitleView extends FrameLayout，在初始化方法里进行渲染
- 继承控件