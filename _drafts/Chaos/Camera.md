# Camera #

## 请求相机权限 ##

    <ueses-feature android:name="android.hardware.camera"
                  android:required="true"/>

如果程序需要使用，但是为了整个功能而不强制要求使用相机，可以使用false参数。

可以通过hasSystemFeature(PackageManager.FEATURE_CAMERA)方法检查设备上的相机是否可用。

## 调用Intent进行拍照 ##

    static final int REQUEST_IMAGE_CAPTURE = 1;
    private void dispatchTakePictureIntent(){
        Intent takePictureIntent = new Intent(MediaStore.Action_IMAGE_CAPTURE)
        if(takePicktureIntent.resolveActivity(getPackageManager()) != null){
            startActivityForResult(takePictureIntent,REQUEST_IMAGE_CAPTURE);
        }
    }

这里startActivityForResult()被一个调用resolveActivity()方法的条件所保护，这个方法返回了可以处理这个Intent的第一个Activity组件，执行这项操作是非常重要的，因为如果你调用startActivityForResult()方法所使用的Intent没有APP可以处理的话，那么你的APP将会崩溃。所以只要结果不是null，那么就意味着可以安全使用这个Intent

## 获取缩略图像 ##

Android的相机应用汇将照片作为一个小的Bitmap对象打包进Intent中，然后通过onActivityResult()方法将该Intent返回。具体的Bitmap对象会附加在键Data后

    @Override
    protected void onActivityResult(int requestCode,int resultCode,Intent data){
        if(requestCode == REQUEST_IMAGE_CAPTURE && resultCode == RESULT_OK){
            Bundle bundle = data.getExtras();
            Bitmap imageBitmap = (Bitmap) bundle.get("data");
            mImageView.setImageBitmap(imageBitmap);
        }
    }

Notice: 从data中获取的缩略图适合于图标，但不使用于很大的图标。处理全尺寸的图像需要花费更多一点的工作。

## 保存全尺寸照片 ##



- getExternalStoragePublicDirectoru()
    - 共享照片目录
    - 需要权限WRITE_EXTERNAL_STORAGE
- getExternalFilesDir()
    - APP的私有目录
    - 当用户卸载APP时，通过getExternalFilesDir()方法所提供的目录下的文件也会被一并删除
>

    String mCurrentPath;
    private File createImageFile() throws IOException{
        //Create an image file name
        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmSS").format(new Date());
        String imageFileName = "JPEG_" + timeStamp + "_";
        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURE);

        File file = File.createTempFile(imageFileName,".jpg",storageDir);

        mCurrentPath = "file:" + file.getAbsolutePath();

        return file;
    }

## 调用系统扫描器 ##

    private void galleryAddPic(){
        Intent mediaScanIntent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
        File f = new File(mCurrentPhotoPath);
        Uri contentUri = Uri.fromFile(f);
        mediaScanIntent.setData(contentUri);
        this.sendBroadcast(mediaScanIntent);
    }

## 解码缩放图像 ##

    private void setPic(){
        //Get the dimensions of the View
        int targetW - mImageView.getWidth();
        int targetH = mImageView.getHeight();

        //Get the dimensions of the bitmap
        BitmapFactory.Options bmOptions = new BitmapFactory.Options();
        bmOptions.inJustDecodeBounds = true;
        BitmapFactory.decodeFile(mCurrentPhotoPath , bmOptions);
        int photoW = bmOptions.outWidth;
        int photoH = bmOptions.outHeight;

        //Determine how much to scale down the image
        int scaleFactor = Math.min(photoW/targetW,photoH/targetH);
    
        //Decode the image file into a Bitmap sized to fill the View
        bmOptions.inJustDecodeBounds = false;
        bmOptions.inSampleSize = scaleFactor;
        bmOptions.inPurgeable = true;
        Bitmap bitmap = BitmapFactory.decodeFile(mCurrentPhotoPath,bmOptions);
        mImageView.setImageBitmap(bitmap);
    }


# 相机API #

## 打开相机 ##

推荐访问相机的方式是在独立的线程打开Camera，这种方式是应对阻塞UI线程的一个号的解决方法。在更加基础化的实现中，开启相机这一步可以推迟到onResume()方法中执行。这样可以促使代码重用并且保持简单的控制流。

    private boolean safeCameraOpen(int id){
        boolean qOpened = false;

        try{
            releaseCameraAndPreview();
            mCamera = Camera.open(id);
            qOpened = (mCamera != null);
        }catch(Exception e){
            e.printStackTrace();
        }

        return qOpened;
    }

    private void releaseCameraAndPreview(){
        mPreview.setCamera(null);
        if(mCamera != null){
            mCamera.release();
            mCamera = null;
        }
    }

## 创建相机预览 ##

使用SurfaceView来绘制相机传感器捕获到的图像

预览需要一个SurfaceHolder.Callback接口的实现，它被用来从相机硬件给应用传递图像数据

    class Preview extends ViewGroup implements SurfaceHolder.Callback{
        SurfaceView mSurfaceView;
        SurfaveHolder mHolder;
        Preview(Context context){
            super(context);
            mSurfaceView = new SurfaceView(context);
            addView(mSurfaceView);
            
            mHolder = mSurfaceView.getHolder();
            mHolder.addCallback(this);
            mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
        }
    }

开始预览前，必须将预览对象传递给Camera对象

    public void setCamera(Camera camera){
        if(mCamera == camera){
            return;
        }

        stopPreviewAndFreeCamera();

        mCamera = camera;

        if(mCamera != null){
            List<Size> localSizes = mCamera.getParameters().getSupportPreviewSizes();
            mSupportedPreviewSizes = localSizes;
            requestLayout();

            try{
                mCamera.setPreviewDisplay(mHolder);
            }catch(Exception e){
                e.printStackTrace();
            }

            mCamera.startPreview();
        }
    }

## 修改相机设置 ##

    public void surfaceChanged
\
## 拍照 ##
    Camera.takePicture()

 可以创建Camera.PictureCallback对象和Camera.ShutterCallback对象然后将他们传递给Camera.takePicture()方法

## 关闭相机 ##

    public void surfaceDestroyed(SurfaceHolder holder){
        if(mCamera != null){
            mCamera.stopPreview();
        }
    }

    private void stopPreviewAndFreeCamera(){
        if(mCamera != null){
            mCamera.stopPreview();
            mCamera.release();
            mCamera = null;
        }
    }