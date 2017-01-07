# 3D入门 #
## OpenGL ##
Open Graphhics Library  开放的图形库接口

## OpenGL ES ##
OpenGL for Embedded System

- 创建GLSurfaceView组件，使用Activity来显示GLSurfaceView组件
- 为GLSurfaceView创建Renderer实例
	- void onDrawFrame(GL10 gl) 绘制GLSurfaceView的当前帧
	- void onSurfaceChanged(GL10 gl,int width,int height)  大小改变时回调该方法
	- void onSurfaceCreated(GL10 gl,EGLConfig config)  被创建时回调该方法
- 调用GLSurfaceView组件的setRenderer指定Renderer对象

>

    @Override
	public void onSurfaceCreated(GL10 gl,EGLConfig config){
		//关闭抗抖动
		gl.glDisable(GL10.GL_DITHER);
		//设置系统对透视进行修正
		gl.glHint(GL10.GL_PERSPECTIVE_CORRECTION_HINT,GL10.GL_FASTSET);
		//设置黑色用于清屏
		gl.glClearColor(0,0,0,0);
		//设置阴影平滑模式
		gl.glShadeModel(GL10_GL_DEPTH_TEST);
		//启用深度测试
		gl.glEnable(GL10.GL_DEPTH_TEST);
		//设置深度测试的类型
		gl.glDepthFunc(GL10.GL_LEQUAL);
	}

	@Override
	public void onSurfaceChanged(GL10 gl,int width,int height){
		//设置3D视窗的大小及位置
		gl.glViewport(0,0,width,height);
		//将当前矩阵模式设为投影矩阵，即满足近大远小，另外还有MODELVIEW,模型视图矩阵，意味着任何新的变换都会影响该矩阵中的所有物体
		gl.glMatrixMode(GL10.GL_PROJECTION);
		//初始化单位矩阵，相当于reset()方法
		gl.glLoadIdentity();
		//计算透视视窗的高度、高度比
		float ratio = (float)width/height;
		//设置透视视窗的空间大小
		gl.glFrustumf(-ratio,ratio,-1,1,1,10);
	}

	@Override
	public void onDrawFrame(GL10 gl){
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT|GL10.GL_DEPTH_BUFFER_BIT);
		//DO STH
	}