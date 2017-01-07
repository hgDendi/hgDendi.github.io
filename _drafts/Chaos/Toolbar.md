# ToolBar #

## 使用 ##
1. 废除掉ActionBar

	将AndroidMenifest.xml中android:theme替换为@style/Theme.AppCompat.NoActionBar
2. 在布局xml中添加android.support.v7.widget.Toolbar控件
>

		<android.support.v7.widget.Toolbar  
        	android:id="@+id/demo_toolbar"  
        	android:layout_width="fill_parent"  
        	android:layout_height="wrap_content"  
        	android:background="?attr/colorPrimary"  
        	android:minHeight="?attr/actionBarSize"  
        	app:title="@string/hello_world" />  
