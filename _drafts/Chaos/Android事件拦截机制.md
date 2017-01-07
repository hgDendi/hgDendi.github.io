# 事件拦截机制 #
假设有ViewGroupA,ViewGroupB,View

    ViewGroupA dispatchTouchEvent()
	ViewGroupA onInterceptTouchEvent()
	ViewGroupB dispatchTouchEvent()
	ViewGroupB onInterceptTouchEvent()
	View       onTouchEvent()
	ViewGroupB onTouchEvent()
	ViewGroupA onTouchEvent()

Flase为继续、true为拦截

	//ViewGroup的onInterceptTouchEvent()返回true时
    ViewGroupA dispatchTouchEvent()
	ViewGroupA onInterceptTouchEvent()
	ViewGroupB dispatchTouchEvent()
	ViewGroupB onInterceptTouchEvent()
	ViewGroupB onTouchEvent()
	ViewGroupA onTouchEvent()

