# 反射 #
可以于运行时加载，探知和使用编译期间完全未知的类

程序在运行状态中，可以动态加载一个只有名称的类，杜宇任意一个已经加载的类，都能够只掉这个类的所有属性和方法；对于任意一个对象，都能调用它的任意一个方法和属性

加载完类以后，在堆内存中会产生一个Class类型的对象，这个对象包含了完整的类的结构信息，而且这个Class对象就像一面镜子，透过这个镜子可以看到类的结构，所以称为反射。

## 动态语言 ##
在程序运行时，允许改变程序结构或变量类型

## Class对象的获取 ##

- 对象的getClass()方法
- 类的.class(最安全、性能最好）属性
- 运用Class.forName(String className)动态加载类，className是类的全限定名

## 从Class中获取信息 ##

First Header  | Second Header
------------- | -------------
构造器  | Constructor<T> getConstructor(Class<?>... parameterTypes)
包含的方法  | Method getMethod(String name,Class<?>... parameterTypes)
包含的属性  | Field getField(String name)
包含的Annotation  | //<A extends Annotation//> A getAnnotatioin(Class<A> annotationiClass)
内部类  | Class<?>[] getDeclaredClasses()
外部类  | Class<?> getDeclaringClass()
实现的接口  | Class<?>[] getInterfaces()
修饰符  | int getModifiers()
所在包  | Package getPackage()
类名  | String getName()
简称  | String getSimpleName()

Constructor.newInstance(paramter...)

method.invoke(object,paramter)

## 判断类本身信息的方法 ##
First Header  | Second Header
------------- | -------------
注解类型 | boolean isAnnotation()
使用了该Annotation修饰 | boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)

