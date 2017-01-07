# LifeCycle #

## 意外销毁时保存Bundle对象 ##
- onSaveInstanceState()
- onCreate()
- onRestoreInstanceState()
  - 在onStart()之后调用，若Bundle为null，则不会被调用


注意onRestart在系统调用onStop()之后再启动

### Activity之间互相传递信息 ###

#### 构建 ####

#### 回退 ####

    {
        Intent intent = new Intent();
        intent.putExtra(_KEY , _VALUE);
        setResult(_RESULT_CODE , intent);
    }


    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    	super.onActivityResult(requestCode, resultCode,  data);
    }

### A将数据存放在intent中，启动B，则B可以通过getIntent获取，即使APP被销毁亦然 ###

## Application ##


### 关于一键退出APP ###

网上有一种做法是用一个容器装下所有启动的activity，然后遍历他们进行finish，

	//后续所有Activiity继承BaseActivity
	class BaseActivity extends Activity{
		@Override 
		public onCreate(Bundle savednstanceState){
			ActivityList.register(this);
		}


		@Override
		public onDestroy(){
			ActivityList.unregister(this);
		}
	}
	
	public class ActivityList{
		public static List<Activity> activities = new ArrayList<Activity>();
		
		public static void register(Activity activity){
			activities.add(activity);
		}
	
		public static void unregister(Activity activity){
			activities.unregister(activity);
		}
	
		public static void finish(){
			for(Activity activity:activities){
				if(activity.sFinishing()){
					activity.finish();
				}
			}
		}
	}