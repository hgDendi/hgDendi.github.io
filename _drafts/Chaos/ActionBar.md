# Action Bar #
TODO：似乎更推崇用ToolBar

## 一些接口 ##
### 添加和移除Action Bar ###

只需要在AndroidManifest.xml中指定Application或Activity的theme是ThemeHolo或其子类即可。

若不需要，可以指定theme为Theme.Holo.NoActionBar,或者调用

    ActionBar actionBar = getActionBar();
	actionBar.hide();

### 修改图标和标题 ###

    <activity
		android:logo="@drawable/weather"
		android:label="Title">
	</activity>

### 添加Action按钮 ###

当Acitivity启动的时候，系统会调用Activity的onCreateOptionsMenu()方法来取出所有的Action按钮，我们只需要在这个方法中去加载一个menu资源，并把所有的Action按钮都定义在资源文件里面就可以了。

    <menu xmlns:android="http://schemas.android.com/apk/res/android"  
   		 xmlns:tools="http://schemas.android.com/tools"  
   		 tools:context="com.example.actionbartest.MainActivity" >  
  
   	 	 <item  
       		 android:id="@+id/action_compose"  
       		 android:icon="@drawable/ic_action_compose"  
       		 android:showAsAction="always"  
       		 android:title="@string/action_compose"/>  
  	 	 <item  
      	 	 android:id="@+id/action_delete"  
      	 	 android:icon="@drawable/ic_action_delete"  
      	 	 android:showAsAction="always"  
      		 android:title="@string/action_delete"/>  
    	 <item  
       		 android:id="@+id/action_settings"  
       		 android:icon="@drawable/ic_launcher"  
       		 android:showAsAction="never"  
       		 android:title="@string/action_settings"/>  
  
	</menu>  


	@Override
	public boolean onCreateOptionsMenu(Menu menu){
		MenuInflater inflater = getMenuInflater();
		inflater.inflate(R.menu.main,menu);
		return super.onCreateOptionsMenu(menu);
	}


	//点击事件
	@Override
	public boolean onOptionsItemSelected(MenuItem item){
		switch(item.getItemId()){
			case R.id. :
				return true;
			default:
				return super.onOptionsItemsSelected(item);
		}
	}


其中showAsAction属性有如下几种：

- always
	- 永远在ActionBar中，如果空间不够则隐藏
- ifRoom
	- 空间足够的情况下显示
- never
	- 永远在overflow中

### 增加左侧返回键 ###

	@Override
	protected void onCreate(Bundle savedInstanceState){
		//TODO

		//启动返回键
		ActionBar actionBar = getActionBar();
		actionBar.setDisplayHomeAsIpEnabled(true);
	}

	@Override
	public boolean onOptionsItemSelected(MenuItem item){
		switch(item.getItemId()){
			//back键的默认id就是这个
			case android.R.id.home:
				finish();
				return true;
		}
	}

注意按下Back和onBackPressed()方法可能有不同的实现

如果需要配置父Activity

    <activity
		android:name="com.example.actionbartest.MainActivity"
		
		android:parentActivityName="com.example.actionbartest.LaunchActivity">

		<meta-data
			android:name="android.support.PARENT_ACTIVITY"
			android:value="com.example.actionbartest.LaunchActivity"/>


	<!--指定MainActivity的父Activity是LaunchActivity -->


	//重写按钮响应事件

	//获取到跳转到父Activity的Intent
	Intent upIntent = NavUtils.getParentActivityIntent(this);
	//判断两个Activity是否在同一个Task中
	if(NavUtils.shouldUpRecreateTask(this,upIntent)){
		//借助TaskStackBuilder来创建一个新的Task
		TaskStackBuilder.create(this)
			.addNextIntentWithParentStack(upIntent)
			.startActivities();
	}else{
		//使用navigateTpTo()方法进行跳转
		upIntent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
		NavUtils.navigateUpTo(this,upIntent);
	}

### 添加Action View ###

在menu资源中通过actionViewClass属性来指定一个控件

    <Menu
		<item
			android:id=
			android:actionViewClass="android.widget.SearchView"
			android:showAsAction="ifRoom|collapseActionView"



这里的collapseActionView表示该控件可以被合并成一个Action按钮

    //添加监听事件
	@Override
	public boolean onCreateOptionsMenu(Menu menu){
		MenuInflater inflater = getMenuInflater();
		inflater.inflate(R.menu.main,menu);
		MenuItem searchItem = menu.findItem(R.id.action_search);
		searchItem.setOnActionExpandListener(new OnActionExpandListener(){
			@Override
			public boolean onMenuItemActionExpand/Collapse(MenuItem item){
				//TODO
			}
		}

		SearchView searchView = (SearchView)searchItem.getActionView();
		//配置searchView
	}

### Overflow按钮不显示的情况 ###

如果手机没有物理Mune键的话，overflow按钮才会显示

    @Override  
	protected void onCreate(Bundle savedInstanceState) {  
    ......  
    setOverflowShowingAlways();  
	}  
  
	private void setOverflowShowingAlways() {  
    	try {
			//使用反射
    	    ViewConfiguration config = ViewConfiguration.get(this);  
      	  	Field menuKeyField = ViewConfiguration.class.getDeclaredField("sHasPermanentMenuKey");  
       	 	menuKeyField.setAccessible(true);  
       	 	menuKeyField.setBoolean(config, false);  
		} catch (Exception e) {  
        	e.printStackTrace();  
		}
	}  

### 让Overflow中的选项显示图标 ###
默认隐藏在overflow中的Action按钮都应该只显示文字

    @Override
	public boolean onMenuOpened(int featureId,Menu menu){
		if(featureId == Window.FEATURE_ACTION_BAR && menu!= null){
			try{
				Method m = menu.getClass().getDeclaredMethod("setOptionalIconsVisible",Boolean.TYPE);
				m.setAccessible(true);
				m.invoke(menu,true);
			}catch(Exception e){
			}
		}
	}
	 