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
