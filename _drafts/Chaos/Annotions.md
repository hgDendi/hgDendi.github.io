# 注解

> 注解为我们在代码中添加信息提供了一种形式化的方法，使我们可以在稍后某个时刻非常方便的使用这些数据
>
> 也称元数据

## 定义注解

> 注解的定义看起来很像接口的定义



元注解：

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
 public interface Test{}
```
### 注解元素

注解元素可用的类型：

* 所有基本类型
* String
* Class
* enum
* Annotation

  > 元素不能有不确定的值，要么具有默认值，要么在使用注解时提供元素的值，且不能以null作为其值2去·1`

### 一个普通注解例子

```java
@Target(ElementType.Method)
@Retention(RetentionPolicy.RUNTIME)
public @interface UseCase{
  	public int id();
  	public String description() default "no description";
}

//xxx.class
@UseCase (id = 0,description = "stub")
public boolean XX{
  return false;
}

public class UseCaseTracker{
	public static void trackUseCases(List<Integer> useCases,Class<?> cl){
      	//很关键 getAnnotation(.class)
      	for(Method m : cl.getDeclaredMethods()){
          	UseCase uc = m.getAnnotation(UseCase.class);
          	if(uc != null){
              	System.out.println("Found Use Case: "+uc.id()+ uc.description);
              	useCases.remove(new Integer(uc.id()));
          	}
      	}
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

