# Android Fragments #

### SurfaceView ###
Android系统中，View通过刷新来重绘视图，通过发出VSYNC信号来进行屏幕的重绘，刷新的间隔时间为16ms。如果在16ms内View完成了你所需要执行的所有操作，那么用户在视觉上，就不会产生卡顿的感觉；而如果执行的操作逻辑太多，特别是需要频繁刷新的界面上，例如游戏界面，那么就会不断阻塞出线程，从而导致画面卡顿。

- View主要用于主动更新，而SurfaceView主要适用于被动更新，例如频繁的刷新
- View在主线程中对画面进行刷新，而SurfaceView通常会通过一个子线程来进行页面的刷新
- View在绘图时没有使用双缓冲机制，而SurfaceView在底层实现机制中就已经实现了双缓冲机制

SurfaceView的使用

    public class SurfaceViewTemplate extends SurfaceView implements SurfaceHolder.Callback,Runnable{

		/**
		*mHolder = getHolder();
		*mHolder.addCallback(this);
		*/
		private SurfaceHolder mHolder;
		private Canvas mCanvas;
		//子线程标志位
		private boolean mIsDrawing;
	
		//Callback的三个接口
		@override
		public void surfaceCreated(SurfaceHolder holder){
			mIsDrawing = true;
			new Thread(this).start();
		}
	
		@override
		public void surfaceChanged(SurfaceHolder holder,int format,int width,int height){
		}
	
		@override
		public void surfaceDestroyed(SurfaceHolder holder){
			mIsDrawing = false;
		}
	
		//Runnable接口
		@override
		public void run(){
			while(mIsDrawing){
				draw();
			}
		}
	
		public void draw(){
			try{
				mCanvas = mHolder.lockCanvas();
				//draw sth
			catch(Exception e){
			}finally{
				if(mCanvas!=null){
					mHolder.unlockCanvasAndPost(mCanvas);
				}
			}}
		}
	}


### inflate ###
>inflate()的作用就是将一个用xml定义的布局文件查找出来

>inflate是加载一个布局文件,而findViewById是从布局文件中查找一个控件

	//取得Inflater的方法
	LayoutInflater.from(this);
	getLayoutInflater();
	(LayoutInflater)this.getSystemService(LAYOUT_INFLATER_SERVICE);
	
	//Inflater方法
	pulic View inflate(int resource,ViewGroup root,boolean attachToRoot){
		//attachToRoot如果为false，则加载root的layout_width和layout_height组成LayoutParams,赋值给View
		//attachToRoot如果设置为true,则将View作为子视图添加到root视图
	}

### ListView ###

1. View	
   - ListView
   - AotuCompleteTextView
   - GridView
   - ExpandableListView
   - AdapterViewFlipper
   - StackView
   2. Adapter用来将数据映射到ListView上的中间
   - ArrayAdapter
     - new ArrayAdapter<String>(Context,id,String[])
   - SimpleAdapter
     - new SimpleAdapter(Context,List<Map<String,Object>>,layoutId,String[],int[])
   - SimpleCursorAdapter
   - BaseAdapter  
     - getCount()
     - getItem(int position)
     - getItemId(int position)
     - getView(int position,View convertView,ViewGroup parent)
2. 数据
   - listView.clearTextFilter()
   - listView.setFilterText(String);


[博客](http://blog.csdn.net/guolin_blog/article/details/44996879)

### ProgressBar ###

style="@android:style/Widget.ProgressBar.

- Horizontal	水平进度条
  - Inverse普通大小的环形进度条
- Large
- Small

>更新操作进度

	setProgress(int)
	incrementProgressBy(int)

#### SeekBar  拖动条 extends ProgressBar####
    seekBar.setOnSeekBarChangeListener(new OnSeekBarChangeListener(){
    	@override
    	public void onProgressChanged(SeekBar arg0,int progress,boolean fromUser){
    	}
    });

#### RatingBar  星级评分条 ####

    android:isIndicator //true为不允许用户更改
    android:numStars  //总共的星级数
    android:rating	//默认的星级
    android:stepSize //每次至少要改变多少个星级
    
    ratingBar.setOnRatingBarChangeListener(new OnRatingBarChangeListener(){
    	@override
    	public void onRatingChanged(params...){
    		//TODO
    	}
    }

### ViewAnimator ###



![](C:\Users\Dendi\Pictures\Android\ViewAnimator.png)

#### ViewSwitcher ####

	android:inAnimation=""
	android:outAnimation=""
	
	switcher.setFactory(new ViewSwitcher.ViewFactory(){
		@override
		public View makeView(){
			
		}
	})

#### ViewFlipper ####
>与AdapterViewFlipper不用的是需要开发者通过addView(View)添加多个View，而不是传入Adapter

### 杂项 ###
#### 获取时间 ####
    Calendar c = Calendar.getInstance();
    int year = c.get(Calendar.YEAR);
    //month,day,hour,minute同理

- CalendarView
- DataPicker

  `dataPicker.init(year,month,day,new OnDataChangedListener(){});`

- TimePicker
- NumberPicker

#### SearchView ####

	//该搜索框是否默认自动缩小为图标
	setIconifiedByDefault(boolean)
	//是否显示搜索按钮
	setSubmitButtonEnabled(boolean)
	//搜索框内默认显示的提示文本
	setQueryHint(CharSequence)
	//事件监听
	setOnQueryTextListener(OnQueryTextListener)

#### TabHost ####
	//xml
	<TabHost
		<TabWidge
		<FrameLayout
	
	//java
	TabSpec tab1 = tabHost.newTabSpec("tab1")
		.setIndicator("")//标题
		.setContent(R.id.tab01);//内容
	tabHost.addTab(tab1);

>已过时，推荐使用Fragment

#### StrollView ####

>由FrameLayout派生而出，是为普通组件添加滚动条的组件，最多只能包含一个组件，作用就是为该组件添加垂直滚动条
>若需要水平滚动条，可以添加HorizontalScrollView


### 对话框 ###

![](C:\Users\Dendi\Pictures\Android\AlertDialog.png)

#### AlertDialog ####
>主要分为四块：图标、标题、内容、按钮

1. 创建AlertDialog.Builder对象
2. 调用AlertDialog.Builder的setTitle()或setCustomTitle()设置标题
3. setIcon()设置图标
4. 设置对话框内容 
   - setMessage()设置对话内容为简单文本
   - setItems()设置对话框内容为简单列表项
   - setSingleChoiceItems()单选列表项
   - setMultiChoiceItems()多选列表项
   - setAdapter()自定义列表项
   - setView()自定义View
5. setPositiveButton()、setNegativeButton()和setNeutralButton()添加按钮
6. create()创建出AlertDialog,使用show()方法显示

#### ProgressDialog ####
    progressDialog = new ProgressDialog(MainActivity.this);
    progressDialog.setTitle("");
    progressDialog.setMessage("");
    progressDialog.setCancelable("");
    progressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
    //设置对话框的进度条是否显示进度
    progressDialog.setIndeterminate(true);
    progressDialog.show();


	progressDialog.setProgress(int);
	progressDialog.setMax(int)
#### Menu ####

>SubMenu:代表一个子菜单，可以包含1~N个MenuItem
>
>ContextMenu:代表一个上下文菜单，可以包含1~N个MenuItem

    /**Menu接口定义
    **通常在add时传入ID便于以后的判断
    **/
    MenuItem add(...);
    SubMenu addSubMenu(...);
    
    /**SubMenu接口定义**/
    SubMenu setHeaderIcon(...);
    SubMenu setHeaderTitle(...);
    SubMenu setHeaderView(View view);
    
    /**MenuItem接口定义**/
    setIntent(Intent intent);
    setOnMenuItemClickListener(MenuItem.OnMenuItemClickListener menuItemClickListener);

1. 重写Activity的onCreateOptionsMenu(Menu menu)方法
2. 重写onOptionsItemSelected(MenuItem mi)
   - 通过mi.getItemId()获得ID针对性的做出响应

#### ContextMenu ####
1. 重写Activity的onCreateContextMenu(ContextMenu menu,View source,MenuInfo menuInfo);
2. 调用Activity的registerForContextMenu(View view)为view组件注册上下文菜单
3. 希望为菜单项提供响应，可以重写onContextItemSelected(MenuItem mi)

#### PopupMenu ####
1. 调用new popUpMenu(Context context,View anchor)创建下拉菜单，anchor代表要激发该弹出菜单的组件
2. 调用MenuInflater的inflate()方法将菜单资源填充到PopupMenu中
3. 调用PopupMenu的show()方法显示弹出式菜单

#### ActionBar ####
    android:theme="@android:style/Theme.Material.NoActionBar"

	//item中的属性always always|withText
	android:showAsAction=""
	
	//是否将应用程序图标转变成可点击的图标,并在图标上增加向左的箭头
	setDisplayHomeAsUpEnabled(boolean)
	//是否显示应用程序
	setDisplayShowHomeEnabled(boolean)
	//是否将应用程序图标转变成可点击的按钮
	setHomeButtonEnabled(boolean)
- 实现Tab导航

1. 调用ActionBar的setNavigationMode(ActionBar.NAVIGATION_MODE_TABS)方法设置使用Tab导航方式
2. 调用ActionBar的addTab()方法添加多个Tab标签，并为每个Tab标签添加事件监听器




#### Intent和Intent-filter ####

Intent对象大致包含

- Component
- Action
- Category
- Data
- Type
- Extra
- Flag
>

	Intent intent = new Intent();

	//Component定义显示Intent
	ComponentName comp = new ComponentName(MainActivity.this,SecondActivity.class);
	intent.setComponent(comp);
	
	//Action表示该Intent所要完成的一个抽象动作,只可指定一个
	intent.setAction(String action);
	
	//Category制定额外的附加类别信息，可指定多个
	intent.addCategory(String category);
	
	//Data属性用于向Action属性提供操作的数据,通常接收一个Uri对象
	//Uri总满足如下格式: scheme://host:port/path
	intent.setData(Uri.parse("lee://www.fkjava.org:8888/test"));
	
	//Type属性用于指定该Data属性所指定Uri对应的MIME类型，这种MIME类型可以是任何自定义的MIME类型
	//Data和Type会互相覆盖,除非执行setDataAndType方法
	intent.setType("abc/xyz");
	
	//Extra属性
	//通常用于在多个Action之间进行数据交换,是一个Bundle对象


	//Flag属性
	//为该Intent添加一些额外的控制旗标
	//如:FLAG_ACTIVITY_CLEAR_TOP





	//在XML中定义
	<activity 
		<intent-filter>
			<action android:name=""/>
			<category android:name=""/>
		<intent-filter>
	</activity>

### StateListDrawable ###
>stateListDrawable用于组织多个Drawable对象,当使用StateListDrawable作为目标组件的背景、前景图片时,StateListDrawable对象所显示的Drawable对象会随目标组件状态的改变而自动切换

- android:color或android:drawable:指定颜色或Drawable对象
- android:state_XXX指定特定状态
  - state_checked	
  - state_enabled
  - state_pressed
  - and so on

### ClipDrawable ###

    <?xml versoin = "1.0" encoding="utf-8"?>
    <clip xmlns:android="http://schemas.android.com/apk/res/android"
    	android:drawable="@drawable/drawable_resource"
    	android:clipOrientation=["horizontal" | "vertical"]
    	android:gravity=["top" | "center" | ...]
    />
    
    ClipDrawable drawable = (ClipDrawable) imageview.getDrawable();
    drawable.setLevel(0~10000);

### AnimationDrawable ###

	<set xmlns:android
		android:interpolator="@android:anim/linear_interpolator"
		android:duration="5000">
		<alpha
			android:fromAlpha = ""
			android:toAlpha = ""
		/>
		<scale
			android:from[X|Y]Scale
			android:pivot[X|Y]
		/>
		<translate
			android:from[X|Y]Delta
		/>
		<rotate
			android:fromDegrees
			android:pivot[X|Y]
		/>
	</set>
	
	final Animation anim = AnimationUtils.loadAnimation(this,R.anim.my_anim);
	//设置动画结束后保留图片的变换结果
	anim.setFillAfter(true);
	image.startAnimation(anim);

### Style ###

	<!--  res\values\my_style.xml  -->
	<resources>
		<style name="style1">
			<item name="android:textSize">20sp</item>
		</style>
		<style name="style2" parent="@style/style1">
			<item name="android:textSize">25sp</item>
		</style>
	<resources>


	<!-- when apply to View -->

	<EditText
		style = "@style/style1"/>

### Theme ###
>只能作用于Activity,不能作用于单个的View组件

>主题定义的格式应该是改变窗口外观的格式,例如窗口标题、窗口边框等

### 使用原始资源 ###

- 位于/res/raw目录下,可以使用R文件语法进行访问
- 位于/assets/目录下,可以使用AssetManager来进行管理
>
    AssetManager am = getAssets();
    AssetFileDescriptor afd = am.openFd("shot.mp3");
    MediaPlayer mediaPlayer = new MediaPlayer();
    mediaPlayer.setDataSource(afd.getFileDescriptor());
    mediaPlayer.prepare();
    mediaPlayer.start();

### Bitmap ###
    BitmapDrawable drawable = new BitmapDrawable(bitmap);
    Bitmap bitmap = drawable.getBitmap();
    
    //返回该Bitmap对象是否已被回收
    bitmap.isRecycled()
    //强制一个Bitmap对象立即回收自己
    bitmap.recycle()
    
    BitmapFactory.decodeByteArray(byte[] data,int offset,int length);
    BitmapFactory.decodeFile(String pathName);
    BitmapFactory.decodeFileDescriptor(FileDescriptor fd);
    BitmapFactory.decodeResource(Resources res,int id);
    BitmapFactory.decodeResource(InputStream is);

### 绘图 ###
>Android的绘图应继承View组件,并重写它的onDraw(Canvas canvas)方法

Canvas

- drawArc/Circle/Line/Oval/Path/Point/Rect/Text
- drawBitmap
- rotate旋转/scale缩放/skew倾斜/translate

>

	//保存画布，将之前所有已绘制的图像保存起来
	canvas.save()
	//合并图层，将save之后的图像与之前的图像合并
	canvas.restore()
	//坐标系的平移
	canvas.translate()
	//坐标系的旋转
	canvas.rotate()

Paint
>表示Canvas上的画笔
>
- setARGB、Color、Style
- setAntiAlias(true)
- setShader(Shader shader)//渲染效果
>
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


### 图形特效处理 ###
使用Matrix控制变换
>是一个矩阵工具类，本身并不能对图形或组件进行变换，但它可与其他API结合来控制图形、组件的变换。

>变换步骤
>
- 获取Matrix对象，既可新建，也可直接获取其他对象内封装的Matrix
- 利用Matrix对象进行评议、旋转、缩放、倾斜等
- 将程序对Matrix所做的变换应用到指定图形或组件

    //平移
  setTranslate(float dx,float dy)
  //倾斜
  setSkew(float kx,float ky,float px,float py);//px、py为轴心
  setSkew(float kx,float ky)//kx、ky为倾斜距离
  //旋转
  setRotate(float degrees);
  setRotate(float degrees,float px,float py);
  //缩放
  setScale(float sx,float sy);
  setScale(float sx,float sy,float px,float py);

  canvas.drawBitmao(bitmao , matrix, paint)

#### drawBitmapMesh图像扭曲 ####

	/**
	*bitmap:源位图
	*meshWidth:在横向上把该源位图划分成多少格
	*meshHeight:在纵向上把该源位图划分成多少格
	*verts:长度是(meshWidth+1)*(meshHeight+1)*2的数组，记录了扭曲后的位图各顶点的位置
	*vertOffset:控制verts数组中从第几个数组元素开始才对bitmap进行扭曲
	*
	*/
	drawBitmapMesh(Bitmap bitmap,int meshWidth,int meshHeight,
		float[] verts,int vertOffset,int[] colors,int colorOffset,Paint paint)

#### 逐帧动画 ####

在anim的xml文件中定义

    <animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    	android:oneshot="true|false"
    	<item 
    		android:drawable=""
    		android:duration=""/>
    </animation-list>

一般将其作为ImageView的背景

    ImageView imageView = new ImageView(this);
    imageView.setBackgroundResource(R.anim.blast);
    AnimationDrawable anim = (AnimationDrawable)imageView.getBackGround();
    
    anim.start();
>如果最后一帧不是空白，而程序又没有控制影藏播放动画的
>
>
>
>,用户会看到动画结束了，但是动画效果依然残留在那里。对此可以重写ImageView的onDraw()方法
>
	@override
	protected void onDraw(Canvas canvas){
		try{
			Field field = AnimationDrawable.class.getDeclaredField("mCurFrame");
			field.setAccessible(true);
			int curFrame = field.getInt(anim);//获取anim动画的当前帧
			if(curFrame == anim.getNumberOfFrames()-1){
				setVisibilirt(View.INVISIBLE);
			}
		}catch(Exception e){
		}
		super.onDraw(canvas);
	}

#### 补间Tween动画 ####
>开发者只需指定动画开始、动画结束等关键帧，而动画变化的中间帧由系统计算并补齐。
>实际上任为播放的逐帧动画

Animation

- AlphaAnimation
- ScaleAnimation
- TranslateAnimation
- RotateAnimation
>
    Animation anim = AnimationUtils.loadAnimation(this,R.main.reverse);
    imageView.startAnimation(anim);

Interpolator
>根据特定算法计算出整个动画所需要动态插入帧的密度和位置。
>
>简单的说，控制动画的变化速度。
>
- LinearInterpolator
- AccelerateInterpolator
- AccelerateDecelerateInterpolator
- CycleInterpolator

## ImageView ##

### ScaleType ###
- Center
  - 居中显示，当图片长宽超过View的长宽，则截取图片的居中部分显示
- CENTER_CROP
  - 按比例扩大图片的size居中显示，使得图片长宽>=View的长宽
- CENTER_INSIDE
  - 按比例扩大图片的size居中显示，使得图片长宽<=View的长宽
- FIT_CENTER
- FIT_START
- FIT_END
  - 把图片缩放到屏幕宽度进行显示
- FIT_XY
  - 不按比例缩放图片，目标把整个View塞满

### Android背景平铺模式 ###
tileMode
- disable
  - 不使用平铺
- clamp
  - 复制边缘色彩
- repeat
  - X、Y轴进行重复图片显示
- mirror
  - 在水平和垂直方向上使用交替镜像的方式重复图片的绘制
####  将背景以XML Bitmap的形式定义： ####
    <?xml >
    <bitmap xmlns:android="http://schemas.android.com/apk/res/android"
    	android:id="@id/toolbar_bg_bmp"
    	android:src="@drawable/toolbar_bg"
    	android:tileMode="disabled"
    	android:gravity=""
    </bitmap>
    
    android:background="@drawable/toolbar_bg_bmp"
#### selector ####

	<selector xmlns:android="">
		<item android:state_pressed="true">
			<bitmap>
				android:src="@drawable/toolbar_bg_sel"
				android:tileMode="disable"
			</bitmap>
		</item>
	
		<item>
		</item>
	<selector>

