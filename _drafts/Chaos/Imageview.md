# ImageView #

### ScaleType ###
保持比例不变

- center
- centerCrop
	- 保证图像充满屏幕，但使比例不变（部分图像会在屏幕之外）
- centerInside
	- 在不遗漏像素的情况下尽可能的充满图像（屏幕会出现空白）
- fitCenter
- fitEnd
- fitStart
- fitXY
	- 充满图像，但是不使之
- matrix
	- 使用图像矩阵方式缩放，可以通过setImageMatrix(Matrix)设置