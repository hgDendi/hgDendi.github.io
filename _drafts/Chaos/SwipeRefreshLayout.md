# SwipeRefreshLayout #

## 重要操作 ##

- setProgressViewOffset(false,0,(int)TypedValue).applyDimension(TypedValue.COMPLEX_UNIT_DIP,24,getResources().getDisplayMetrics()));

    mSwipeRefreshLayout.setColorScheme(int);
	mSwipeRefreshLayout.setOnRefreshListener(listener);
	mSwipeRefreshLayout.setProgressViewOffset(boolean scale,int start,int end);

	mSwipeRefreshLayout.setProgressViewOffset(false, 0, (int) TypedValue  
    .applyDimension(TypedValue.COMPLEX_UNIT_DIP, 24, getResources()  
      .getDisplayMetrics()));  