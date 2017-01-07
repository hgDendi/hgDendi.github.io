# 关于dpi #

## dpi对应屏幕密度 ##
<table class="table table-bordered table-striped table-condensed">
<thead>
<tr>
<th>dpi范围</th>
<th>密度</th>
</tr>
</thead>
<tbody>
<tr>
  <td>0dpi ~ 120dpi</td>
  <td>ldpi</td>
</tr>
<tr>
  <td>120dpi ~ 160dpi</td>
  <td>mdpi</td>
</tr>
<tr>
  <td>160dpi ~ 240dpi</td>
  <td>hdpi</td>
</tr>
<tr>
  <td>240dpi ~ 320dpi</td>
  <td>xhdpi</td>
</tr>
<tr>
  <td>320dpi ~ 480dpi</td>
  <td>xxhdpi</td>
</tr>
<tr>
  <td>480dpi ~ 640dpi</td>
  <td>xxxhdpi</td>
</tr>
<tbody>
</table>

获取屏幕dpi值

    float xdpi = getResources().getDisplayMetrics().xdpi;
	float xdpi = getResources().getDisplayMetrics().ydpi;

## 系统获取图片 ##
- 首先找对应密度文件夹下的图片资源
- 寻找更高密度下的图片资源，并进行缩小
- 查找是否有nodpi文件夹下的图片资源，不会进行缩放
- 寻找更低密度下的图片资源，并进行放大

上面表格中dpi范围的最大值就是图片缩放的比例

## Tips ##
- 若只有一张，则尽量放在xxhdpi目录下