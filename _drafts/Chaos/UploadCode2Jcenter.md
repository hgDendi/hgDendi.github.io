JCenter

## compile

```groovy
compile 'com.android.support:recyclerview-v7:27.0.0'
compile 'com.squareup:otto:1.3.7'
compile 'com.squareup.picasso:picasso:2.5.2'
compile 'com.squareup.okhttp:okhttp:2.4.0'
compile 'com.squareup.retrofit:retrofit:1.9.0'
```

只是把对应库的jar或aar文件放到了远程服务器上，通过compile进行拉取。

### AAR是什么

因为AndroidLibrary一般需要内置一些Android特定文件，比如Manifest，Resources，Assets或者JNI等超出jar文件标准的格式。

jar包是被嵌入在aar文件包中的一部分。

### 排布规则

compile后面的字符串排布规则是

GROUP_ID:ARTIFACT_ID:VERSION

#### GROUP_ID

通常用开发者的包名进行命名。

很有可能同一个上下文中有好几个library，这些可以共享同一个GROUP_ID

#### ARTIFACT_ID

定义library的真实名字，比如picasso

#### VERSION

## Android项目准备

## Module划分

一般需要把需要上传的库作为一个独立的module，即1 module per 1 library。

一般分为库的模块和使用demo模块。

如果想要有一个以上的库，请划分多个module。