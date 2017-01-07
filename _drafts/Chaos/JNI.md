# JNI #
# JaveNativeInterface #

## Reason ##
使用JNI是有风险的，会使得Java程序失去跨平台性。同时本地代码的不当使用会使整个java程序崩溃。

## JVM及其他 ##
在JNI中，jvm是一个全局的指针。Jvm中的每一个县城有一个JNIEnv指针，该指针是线程相关的，在同一个线程中获取到的JNIEnv是同一个指针。

利用JNIEnv指针我们可以获得在native中创建的全局变量