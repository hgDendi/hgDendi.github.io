# TouchEvent #

- DOWN
- MOVE
- UP\CANCEL

## 关于DOWN ##
如果DOWN被onTouchEvent消费，则之后的MOVE和UP事件都会被传递到此控件消费。


## MOVE ##
一次被拦截，则之后的所有MOVE，onDispatchEvent都会返回true进行拦截，如果之前DOWN被下面的界面拦截，因为本次MOVE已被拦截，无法传递下去，则会传递CANCEL到下层