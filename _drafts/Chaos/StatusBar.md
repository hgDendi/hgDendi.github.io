# StatusBar #

    if (android.os.Build.VERSION.SDK_INT >= 19) {
        Window window = getWindow();
        window.setFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS,
                WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        mTintManager = new SystemBarTintManager(this);
        mTintManager.setStatusBarTintEnabled(true);
        mTintManager.setNavigationBarTintEnabled(true);
        mTintManager.setTintColor(getResources().getColor(R.color.background_statusbar));
        }
    
    public void fullScreen(boolean enabled) {
        if (Build.VERSION.SDK_INT >= 19) {
            mTintManager.setStatusBarTintEnabled(!enabled);
        }
        if (enabled) {
            getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
        } else {
            getWindow().clearFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
        }
    }
