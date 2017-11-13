介绍如何将自己的项目上传到JCenter。

## 前言

我们经常在Android的gradle文件中看到这些compile脚本，这些脚本其实就是因为之前库的开发者把对应库的jar或aar文件放到了远程服务器上，所以我们可以通过compile进行拉取。

```groovy
compile 'com.android.support:recyclerview-v7:27.0.0'
compile 'com.squareup:otto:1.3.7'
compile 'com.squareup.picasso:picasso:2.5.2'
compile 'com.squareup.okhttp:okhttp:2.4.0'
compile 'com.squareup.retrofit:retrofit:1.9.0'
```

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

版本号，一般是x.x.x格式

## Android项目准备

以一个简单的demo为例，项目地址在

https://github.com/hgDendi/AndroidJCenterDemo

是一个HelloWorld项目，其中字符串来自于mylib module下的MyLib抽象类的静态方法。

mylib也是我们这次需要上传到JCenter的类。

![project](https://ws3.sinaimg.cn/large/006tKfTcgy1flfkm0i210j31f2104n53.jpg)

### Module划分

一般需要把需要上传的库作为一个独立的module，即1 module per 1 library。

一般分为库的模块和使用demo模块。

如果想要有一个以上的库，请划分多个module。

比如如下的项目结构。![1510486146328](https://ws2.sinaimg.cn/large/006tKfTcgy1flfj850vuxj30n20lmwgx.jpg)

### bintray

注册账号，登录bintray.com。

#### 新建仓库

在个人管理界面点击“Add New Repository”

![1510486324520](https://ws3.sinaimg.cn/large/006tKfTcgy1flfj872g3ej30pq0a4wfb.jpg)

然后创建一个Maven仓库

![1510486374030](https://ws4.sinaimg.cn/large/006tKfTcgy1flfj86ad46j314g1jgn1w.jpg)

创建成功之后点击"Create New Package"

![WX20171112-193514@2x](https://ws1.sinaimg.cn/large/006tKfTcgy1flfj85onarj31j40my774.jpg)

输入对应信息，其中Website和VersionControl可以填写github地址，issue填写github的issue地址

![WX20171112-193746@2x](https://ws3.sinaimg.cn/large/006tKfTcgy1flfk8n6qqyj31hu1hc0z4.jpg)

点击CreatePackage，你在Bintray上的Maven服务器就已经创建成功了。

### 项目文件准备

#### Project#gradle

在Project的gradle文件中加入依赖

```groovy
buildscript {
    ...
    
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.0'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.6'
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.4.1'
    }
}
```

#### local.properties

在local.properties中加入用户信息。

因为gitignore自动忽略local.properties，所以私人信息放在这里是比较安全的。

```
bintray.user=[...]
bintray.apikey=[...]
bintray.gpg.password=[...]
```

apikey可以在EditProfile里看到

![APIKEY](https://ws4.sinaimg.cn/large/006tKfTcgy1flfk8oyqi2j31i60sqgpx.jpg)

#### module的build.gradle

根据上面步骤中的信息，在lib module的build.gradle文件中增加信息。

```groovy
apply plugin: 'com.android.library'

ext {
  	// 刚刚创建的仓库名
    bintrayRepo = 'testMaven'
  	// package的名字
    bintrayName = 'mylib'

  	// owner的groupID
    publishedGroupId = 'com.hgDendi.test'
    libraryName = 'MyLib'
    artifact = 'mylib'

    libraryDescription = 'lib test demo'

    siteUrl = 'https://github.com/hgDendi/AndroidJCenterDemo'
    gitUrl = 'https://github.com/hgDendi/AndroidJCenterDemo.git'

    libraryVersion = '0.0.1'

    developerId = 'dendi'
    developerName = 'Dendi Chan'
    developerEmail = 'hg.dendi@gmail.com'

    licenseName = 'The MIT License'
    licenseUrl = 'https://rem.mit-license.org'
    allLicenses = ["MIT"]
}
```

如上所示，则最后生成的gradle script会是

```
compile 'com.hgdendi.test:mylib:0.0.1'
```

#### 增加脚本依赖

在lib module的build.gradle文件下增加一行apply from

```groovy
apply from: 'https://raw.githubusercontent.com/hgDendi/AndroidJCenterDemo/master/bintray.gradle'
apply from: 'https://raw.githubusercontent.com/hgDendi/AndroidJCenterDemo/master/maveninstall.gradle'
```

## 上传代码

### 执行gradlew命令将lib上传

```
./gradlew install
./gradlew bintrayUpload
```

这时候登录bintray，就可以看到上传结果了。

![uploadSuccess](https://ws4.sinaimg.cn/large/006tKfTcgy1flfk8pysvxj31hy1a8dmc.jpg)

### 同步到jcenter

此时代码还是只在你的maven仓库，并未同步到JCenter中。

这时候点进你的package，点击AddToJCenter就可以将代码上传到JCenter。

![add2Jcenter](https://ws2.sinaimg.cn/large/006tKfTcgy1flfk8o2357j31i21dmgtg.jpg)

## 上传成功

同步到JCenter有时候可能需要半天到一天的时间。

同步成功后，便可以使用compile从服务器拉取lib了。

```groovy
dependencies {
//    implementation project(':mylib')
    compile 'com.hgDendi:mylib:1.0.0'
}
```

