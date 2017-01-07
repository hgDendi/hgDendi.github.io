# Material Design #

## Riddle效果 ##

- 在控件的xml中定义
>
    波纹有边界：
    android:background="?android:attr/selectableItemBackground"
    波纹无边界：
    android:background="?android:attr/selectableItemBackgroundBorderless"

- 将Riddle效果定义在XML中
>
    <ripple
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="@android:color/holo_blue_bright">
        <item>
            <shape
                android:shape="oval">
            <solod android:color="?android:colorAccent"/>
            </shape>
        </item>
    <ripple>

    <Button
        android:layout***
        android:background="@drawable/ripple" />

## Curcular Reveal ##
表现为一个View以一个圆形