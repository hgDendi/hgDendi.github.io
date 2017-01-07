# Path #

| 方法  | 作用 |
| ------------- | ------------- |
| moveTo  | 移动下一次操作的起点位置,不会改变之前的轨迹  |
| setLastPoint  | 重置当前path中最后一个点的位置，会改变之前绘制的效果，如果在绘制之前调用，效果和moveTo相同  |
| lineTo  | 添加上一个点到当前点之间的直线到Path  |
| close  | 连接当一个点和最后一个点，形成一个闭合区域  |
| add  | Content Cell  |
| isEmpty  |  |
| isRect  | 判断path是否是一个矩形 |
| set  | 用新的路径替换到当前路径的所有内容 |
| offset  | 对当前路径之前的操作进行偏移 |
| quadTo,cubicTo  | 二次和三次贝塞尔曲线的方法 |
| rXXXTo  | 不带r的方法是基于原点的坐标系，rXxx方法是基于当前点坐标系 |
|setFillType,getFillType,isInverse,FillType,toggleInverseFillType  | 设置、获取、判断、切换填充模式  |
| incReserve  | 提示Path哈有多少个点等待加入 |
| op  | 对两个Path进行布尔运算（交、并）  |
| computeBounds  | 计算Path的边界  |
| reset,rewind  | 清楚Path中的内容
reset不保留内部数据结构，但会保留FillType
rewind会保留内部的数据结构，但不保留FllType  |
| transform  | 矩阵变换  |

## 可能需要关闭硬件加速，以免引起不必要的问题 ##

    android:hardwareAccelerated="false"

## 定义 ##
- CW
	- clockwise
	- 顺时针
- CCW
	- counter-clockwise
	- 逆时针