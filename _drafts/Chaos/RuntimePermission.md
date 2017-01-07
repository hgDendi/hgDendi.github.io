# 运行时权限 #

从6.0开始，不再是安装应用时用户确定获得全部的权限，而是在使用软件的过程中需要该权限时，弹出对话框让用户选择权限。

Android权限有normal和dangerous两种，normal权限系统会自动赋予给应用程序，dangerous权限需要运行时权限处理

## 检查是否获得权限 ##
    通过ContextCompat返回值确定（PackageManager.PERMISSION_GRANTED/DENIED):
    ContextCompat.check
    
    int permissionCheck = ContextCompat.checkSelfPermission(this,Manifest.permission.WRITE_CALENDAR);
    if(permissionCheck == PackageManager.PERMISSION_GRANTED){
        
    }else{
    
    }

## 请求权限 ##
    ActivityCompat.requestPermissions(activity,permissions,requestCode)

- permissions是一个String数组
- 第三个参数是请求码，用于在onRequestPermissionsResult方法中根据requestCode进行判断

    ActivityCompat.requestPermissions(
        PermissionActivity.this,
        new String[]{Manifest.permission.READ_CONTACTS},
        MY_PERMISSIONS_REQUEST_READ_CONTACTS);

## 请求权限后回调 ##

在activity中重写onRequestPermissionsResult(requestCode,permissions,grantResults)

    @Override
    public void onRequestPermissionsRusult(int requestCode,@NonNull String[] permissions,@NonNull int[] grantResults){
        switch(requestCode){
            if(grantResult.length>0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED){
    
            }else{
    
            }
        }
    }

## 告诉用户为何需要权限 ##

    ActivityCompat.shouldShowRequestPermissionRationale(activity,permission)

此方法在用户拒绝权限后返回true，即当用户拒绝掉该权限，下次点击此权限处时，该方法会返回true。可以在里面进行对该权限的说明，然后弹出权限让用户选择。

​    