# Activity的切换动画 #

## 定义在代码中 ##

    overridePendingTransition(R.anim.activity_right_in, R.anim.activity_left_out);

## 定义在Theme中 ##

    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="android:windowAnimationStyle">@style/ActivityAnimTheme</item>
    </style>

    <style name="ActivityAnimTheme" parent="@android:style/Animation.Activity">
        <item name="android:activityOpenEnterAnimation">@anim/activity_right_in</item>
        <item name="android:activityOpenExitAnimation">@anim/activity_right_out</item>
        <item name="android:activityCloseEnterAnimation">@anim/activity_right_in</item>
        <item name="android:activityCloseExitAnimation">@anim/activity_right_out</item>
    </style>

## xml Sample ##

    <?xml version="1.0" encoding="utf-8"?>
    <set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:duration="500"
        android:fromXDelta="100%"
        android:fromYDelta="0"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:toXDelta="0%"
        android:toYDelta="0" />
    </set>
