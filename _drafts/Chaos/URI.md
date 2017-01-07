# Intent #

## Action ##
## Category ##

## Data ##


    //DATA
    scheme://host:port/path

## Flag ##
- FLAG_ACTIVITY_BROUGHT_TO_FRONT
    - 通过该Flag启动的Activity已存在，下次再启动时，只是把该Activity带到前台
    - 如A→B(FLAG)→C→D  在D中启动B，则栈中会是ACDB
- FLAG_ACTIVITY_CLEAR_TOP
    - 相当于lauchmode:SINGLE_TASK
- FLAG_ACTIVITY_NEW_TASK
- FLAG_ACTIVITY_NO_ANIMATION
    - 不使用过渡动画
- FLAG_ACTIVITY_NO_HISTORY
    - 不会被保留在栈中
- FLAG_ACTIVITY_REORDER_TO_FRONT
    - 如果当前已有FLAG，则直接将Activity带到前台
- FLAG_ACTIVITY_SINGLE_TOP