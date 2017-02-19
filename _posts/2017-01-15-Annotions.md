---
layout: post
comments: true
title: "Java Annotations"
date: 2017-01-15
categories: java
tags: [java]
---



> 注解为我们在代码中添加信息提供了一种形式化的方法，使我们可以在稍后某个时刻非常方便的使用这些数据
>
> 也称元数据
>

## 定义注解

> 注解的定义看起来很像接口的定义



```java
public @interface Demo{
  String value(); //方法的形式表示属性,没有默认值的话需要手动赋值
  String value2() default "helloWorld";
}
// 等价于,本质上是一个集成了Annotation接口的接口
interface Demo extends java.lang.annotation.Annotation{}
```



### 元注解：（修饰注解的注解）

* @Target
  * 用来定义注解应用于什么地方
  * CONSTRUCTOR
  * FIELD
  * LOCAL_VARIABLE
  * METHOD
  * PACKAGE
  * PARAMETER
  * TYPE
* @Rectetion
  * 定义注解在哪一个级别可用
    * SOURCE
      * 将被编译器丢弃
    * CLASS
      * 注解在class文件中可用，但会被VM丢弃
    * RUNTIME
      * 可使用反射
* @Document
  * 将此注解包含在JavaDoc中
* @Inherited
  * 允许子类继承父类中的注释

### marker annotation 标注注解

* 没有元素的注解


```java
 @Target(ElementType.Method)
 @Retention(RetentionPolicy.RUNTIME)
 public @interface Test{}
```
### 注解元素

注解元素可用的类型：

* 所有基本类型

* String

* Class

* enum

* Annotation

* 数组

  > 元素不能有不确定的值，要么具有默认值，要么在使用注解时提供元素的值，且不能以null作为其值

### 运行时注解和编译时注解

#### 运行时注解

在程序运行时通过反射来获取注解和注解的属性值。但是因为大量使用反射，对程序会有性能上的影响。

调用Class对象、Method对象、Field对象的getAnnotation(Class<A> annotationType)

#### 编译时注解

在代码编译时通过APT（Annotation Processing Tool）获取注解和注解的属性值，并据此生成Java代码。在程序运行时只要调用编译时生成的代码即可，不需要再通过反射区解析注解。比如Dagger、ButterKnife等。

### 比如使用注解来实现一个简陋版的ButterKnife

> 例子来自[Ryon](http://www.jianshu.com/p/bc70d5d71a61)

```java
//Annotation
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ViewInject {  
    int value() default 0;  
}

//Activity
public void autoInjectAllField() {  
        try {  
            Class<?> clazz = this.getClass();  
            Field[] fields = clazz.getDeclaredFields();
            for (Field field : fields) {  
                // 查看这个字段是否有我们自定义的注解类标志的  
                if (field.isAnnotationPresent(ViewInject.class)) {  
                    ViewInject inject = field.getAnnotation(ViewInject.class);  
                    int id = inject.value();  
                    if (id > 0) {  
                        field.setAccessible(true);  
                        field.set(this, this.findViewById(id));//给我们要找的字段设置值  
                    }  
                }  
            }  
        } catch (IllegalAccessException e) {  
            e.printStackTrace();  
        } catch (IllegalArgumentException e) {  
            e.printStackTrace();  
        }  
}
```


### Android中的View

。Android中可以使用IntDef注解来声明常量值（配合BitMask使用更酸爽）

```java
public class Toast {
    static final String TAG = "Toast";
    static final boolean localLOGV = false;

    /** @hide */
    /*定义部分*/
    @IntDef({LENGTH_SHORT, LENGTH_LONG})
    @Retention(RetentionPolicy.SOURCE)
    public @interface Duration {}

    public static final int LENGTH_SHORT = 0;
    public static final int LENGTH_LONG = 1;

    ...

    /*作为类型使用时*/
     /**
     * Set how long to show the view for.
     * @see #LENGTH_SHORT
     * @see #LENGTH_LONG
     */
    public void setDuration(@Duration int duration) {
        mDuration = duration;
    }

    /*做为返回值时*/
    /**
     * Return the duration.
     * @see #setDuration
     */
    @Duration
    public int getDuration() {
        return mDuration;
    }
}
```

