# ProgressDialog #



## 问题复现 ##

	/*
	*	一个按钮的Listener中
	*/
    startTask(singleTask);//发送拍照请求

	//跳出一个ProgressDialog
	if(blockDialog==null){
		blockDialog = new ProgressDialog(this);
	}
	blockDialog.setMessage(string);
	blockDialog.setCancelable(false);
	blockDialog.show();


	/*
	*	响应放在onEventMainThread中
	*/
	public void onEventMainThread(CmdEvent event) {
		if(seqnum==event.seqNum){
			blockDialog.dismiss();
		}
	}

发现有极小情况下dialog.show()之后无法被dismiss掉，会卡死。
百思不得其解。

## 错误分析 ##


如果show()和dismiss()操作均是发送消息给UI线程，在UI线程中操作，那么流程会是如下：

**UI线程**：

1. **onClick（）**
	- startUITask	//收到event，产生2操作
	- progressDialog.show()//发送showMessage,即3操作

2. **onEventMainThread(Event event)**
	- progressDialog.dismiss()//发送dismissMessage，即4操作


3. **showMessage**


4. **disMessage**

其中2、3可能因为具体操作而互换位置，但是3、4操作是必定有序的（因为1操作必定在2操作之前）
所以一定会先show()再dismiss(),不会引起问题。

## 关于show()和dismiss()的具体实现 ##

### show() ###

    /**
     * Start the dialog and display it on screen.  The window is placed in the
     * application layer and opaque.  Note that you should not override this
     * method to do initialization when the dialog is shown, instead implement
     * that in {@link #onStart}.
     */
    public void show() {
        if (mShowing) {
            if (mDecor != null) {
                if (mWindow.hasFeature(Window.FEATURE_ACTION_BAR)) {
                    mWindow.invalidatePanelMenu(Window.FEATURE_ACTION_BAR);
                }
                mDecor.setVisibility(View.VISIBLE);
            }
            return;
        }

        mCanceled = false;
        
        if (!mCreated) {
            dispatchOnCreate(null);
        }

        onStart();
        mDecor = mWindow.getDecorView();

        if (mActionBar == null && mWindow.hasFeature(Window.FEATURE_ACTION_BAR)) {
            final ApplicationInfo info = mContext.getApplicationInfo();
            mWindow.setDefaultIcon(info.icon);
            mWindow.setDefaultLogo(info.logo);
            mActionBar = new WindowDecorActionBar(this);
        }

        WindowManager.LayoutParams l = mWindow.getAttributes();
        if ((l.softInputMode
                & WindowManager.LayoutParams.SOFT_INPUT_IS_FORWARD_NAVIGATION) == 0) {
            WindowManager.LayoutParams nl = new WindowManager.LayoutParams();
            nl.copyFrom(l);
            nl.softInputMode |=
                    WindowManager.LayoutParams.SOFT_INPUT_IS_FORWARD_NAVIGATION;
            l = nl;
        }

        try {
            mWindowManager.addView(mDecor, l);
            mShowing = true;
    
            sendShowMessage();
        } finally {
        }

	/*
	*	使用Handler进行Show的操作
	*/
	private void sendShowMessage() {
        if (mShowMessage != null) {
            // Obtain a new message so this dialog can be re-used
            Message.obtain(mShowMessage).sendToTarget();
        }
    }




### dismiss() ###

    /**
     * Dismiss this dialog, removing it from the screen. This method can be
     * invoked safely from any thread.  Note that you should not override this
     * method to do cleanup when the dialog is dismissed, instead implement
     * that in {@link #onStop}.
     */
    @Override
    public void dismiss() {
        if (Looper.myLooper() == mHandler.getLooper()) {
            dismissDialog();
        } else {
            mHandler.post(mDismissAction);
        }
    }

	void dismissDialog() {
        if (mDecor == null || !mShowing) {
			/*
			*	Warning
			*	如果此时发现show()方法没有调用，则会直接return，什么都不做
			*	而不是发送一个Msg到消息队列中
			*/
            return;
        }

        if (mWindow.isDestroyed()) {
            Log.e(TAG, "Tried to dismissDialog() but the Dialog's window was already destroyed!");
            return;
        }

        try {
            mWindowManager.removeViewImmediate(mDecor);
        } finally {
            if (mActionMode != null) {
                mActionMode.finish();
            }
            mDecor = null;
            mWindow.closeAllPanels();
            onStop();
            mShowing = false;

            sendDismissMessage();
        }
    }

	private void sendDismissMessage() {
        if (mDismissMessage != null) {
            // Obtain a new message so this dialog can be re-used
            Message.obtain(mDismissMessage).sendToTarget();
        }
    }


## 错误原因 ##

>源码中发现dismiss()操作并不是一定通过传递消息去UI线程的消息队列进行操作的，在没有调用show()方法时，它会判断此次操作无效，而不会发送任何消息，所以实际上步骤2中的dismiss()操作判断show()没有被调用后,直接判定此次dismiss()操作失效。
>所以在某些情况下(Task任务返回的event先于show()的Message到达主线程的消息队列)，会导致dialog锁死。

1. **onClick（）**
	- startUITask	//收到event，产生2操作
	- progressDialog.show()//发送showMessage,即3操作

2. **onEventMainThread(Event event)**
	- progressDialog.dismiss()//判断isShowing()为false,抛弃这次操作


3. **showMessage** //卡死

## 小节 ##
>show()操作如果发现对话框已处于活跃状态(isShowing为true)，会直接抛弃这次操作
>dismiss()操作如果发现对话框当前并不处于活跃状态，会直接抛弃这次操作
>正常流程下会发送消息给UI线程进行操作处理。