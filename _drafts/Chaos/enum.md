# ENUM #

是单键模式最好的实现

## 例子 ##

    public enum Color{
        RED,BLUE,BLACK,YELLOW,GREEN
    }

字节码文件：

    final enum XX.Color{
        //所有的枚举值都是类静态常量
        public static final enum Color RED;
        public static final enum Color BLUE;
        public static final enum Color BLACK;
        public static final enum Color YELLOW;
        public static final enum Color GREEN;

        private static final synthetic Color[] ENUM$VALUES;

        // 初始化过程，对枚举类的所有枚举值对象进行第一次初始化  
        static{
            0  new hr.test.Color [1]   
            3  dup  
            4  ldc <String "RED"> [16] //把枚举值字符串"RED"压入操作数栈  
            6  iconst_0  // 把整型值0压入操作数栈  
            7  invokespecial hr.test.Color(java.lang.String, int) [17] //调用Color类的私有构造器创建Color对象RED  
            10  putstatic hr.test.Color.RED : hr.test.Color [21]  //将枚举对象赋给Color的静态常量RED。  
            .........  枚举对象BLUE等与上同  
            102  return  
        }

        //私有构造器
        private Color()
    }

### 注意点 ###
- Color枚举类就是class，而且是一个不可以被继承的final类。其枚举值都是Color类型的类静态常量；
- 这些枚举值都是public static final的
- 在枚举类型中有构造器，方法和数据域，但是
    - 构造器只是在构造枚举值的时候被调用
    - 构造器只能是private的。以此保证外部代码无法新构造枚举类的实例。
    - 所有的枚举类都继承了Enum的方法
        - ordinal()返回枚举值在枚举类中的书含蓄，如Color.RED.ordinal()==0
        - compareTo(),返回两个枚举值的差
        - values() 返回一个包含全部枚举值的数组
        - toString() 返回枚举常量的名称
        - valueOf()  返回带指定名称的指定枚举类型的枚举常量
        - equals()  比较两个枚举类对象的引用