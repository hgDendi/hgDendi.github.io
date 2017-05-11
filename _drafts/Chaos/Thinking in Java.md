# Thinking in Java #

### 存储数据的五个区域 ###
- 寄存器
  - 最快的存储区
- 堆栈
  - 位于通用RAM中
  - 通过堆栈指针从处理器获得直接支持，堆栈指针向下移动，分配新的内存；向上移动，释放内存
  - 为方便移动指针，必须知道存储项的生命周期，所以一般存储对象引用，而对象并不存储在其中
- 堆
  - 通用的内存池，用于存放所有的Java对象
  - 用堆进行存储分配和清理可能比用堆栈需要更多的时间
- 常量存储
- 非RAM存储
  - 流对象
  - 持久化对象

#### 基本类型 ####
不用new来创建变量，而是创建一个并非是引用的“自动”变量，这个变量直接存储值，并置于堆栈中。

8-bits		boolean  byte

16-bits		char 	short

32-bits		int		float

64-bits		long 	double

Java中所有数据类型在所有机器中的大小都是相同的，所以不需要像C和C++一样使用sizeof()进行移植

若作为类的成员使用时，会被赋予初始默认值。但是作为局部变量时，不会被赋予初始值。

### JavaDoc ###
- @see
  - 引用其他类的文档
  - @see classname
  - @see fully-qualified-classname
  - @see fully-qualified-classname#method-name
- {@link package.class#menber label}
- {@docRoot}
  - 产生到文档根目录的相对路径
- {@inheritDoc}
  - 从当前这个类的最直接的基类中继承相关文档到当前的文档注释中
- @version
- @author
- @since
  - 指定JDK版本
- @param
- @return
- @throws
- @deprecated

### Random对象 ###
产生随机数一般需要创建Random对象，如果在创建过程中没有传递任何参数，那么Java就会将当前时间作为随机数生成器的种子，并由此在程序每一次执行时都产生不同的输出。

通过在创建Random对象时提供种子，可以在每一次执行程序时都生成相同的随机数。

>种子是用于随机数生成器的初始化值，随机数生成器对于特定的种子值总是产生相同的随机数序列。

	//每次输出结果都是一样的
	Random aRandom = new Random(1);
	System.out.println(aRandom.nextInt());
	System.out.println(aRandom.nextInt());
	System.out.println(aRandom.nextInt());
	System.out.println(aRandom.nextInt());

所以不存在真正意义上的全随机.

### 异或^ ###
>对应位相同返回0 相异返回1

    //swap the value of a ,b
    int a = 5;
    int b = 9;
    a = a ^ b;
    b = a ^ b;
    a = a ^ b;

### 移位操作符 ###
- <<
- \>>
  - 有符号右移
- \>>>
  - 无符号右移

若对char、byte、short类型的数值进行移位，会首先进行int的转型，并且得到的结果也为int型
若移位操作符右值大于精度值，则会只取数值右端的有效地位。

如：int类型进行移位，只有数值右端的低5位才有用

### Cast ###
- narrowing conversion
- widening conversion

>使用组合还是继承，一个判断方法就是问一问自己是否需要从新类向基类进行向上转型

### GOTO 、 Label ###

    outer:
    outer-iteration{
    	inner-iteration{
    		//DO STH
    		continue outer;
    		//DO STH
    		break outer;
    	}
    }

>在Java里需要使用标签的唯一理由就是因为有循环嵌套存在，而且想从多层嵌套中break或continue。

### switch ###

只可传入int或char那样的整数值

判断是if( == )吗？还是有优化？

答：编译器在switch是连续整数的时候会根据二分法查找进行优化，其他情况没有了解

### 重载 ###
	//class Son extends class Father
	Father a = new Father();
	f(a);		//调用f(Father)
	a.toString();	//调用father.toString();
	
	//此处注意…………………………………………………………………………（1）
	Father b = new Son();
	f(b);			//调用f(Father)
	b.toString();	//调用son.toString();
	
	Son c = new Son();
	f(c);			//调用f(Son)，如果没有f(Son),再调用f(Father)
	c.toString();	//调用son.toString();


（1）

Thinking in Java里说遇到 a.doSth(); 编译器编译之后会变为doSth(a)这样的写法。

但是object.doSth  取object的类型是new后指定的类型

doSth(object) 则是取的声明时候声明的类型

应该和编译原理有关，没有深究。感觉是object.doSth()是动态加载，doSth(object)是静态加载

### this关键字 ###
- 代表调用方法的那个对象
- 表示在构造器中调用构造器
- 非静态内部类中用 OutClass.this 获取外部类对象


### finalize() ###

一旦垃圾回收器准备好释放对象占用的存储空间，将首先调用其finalize()方法，并且在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。

之所以要有finalize()方法，是由于在分配内存时可能采用了类似C语言中的做法，而非Java中的通常做法。

### 对象的初始化过程 ###
- 构造器实际上是静态方法。当首次创建类型为Dog的对象时，或者Dog类的静态方法、静态域首次被访问时（常数静态域不会触发类的初始化过程），Java解释器必须查找类路径，以定位Dog.class文件
- 载入Dog.class，有关静态初始化的所有动作都会执行。静态初始化只在Class对象首次加载的时候进行一次
- 当用new Dog()创建对象的时候，首先将在堆上为Dog对象分配足够的存储空间
- 这块存储空间会被清零，这就自动的将Dog对象中的所有基本类型数据都设置成了默认值，引用设置为null
- 执行所有出现于字段定义处的初始化动作
- 执行构造器

### 编译单元 ###


- 当编写一个Java源代码文件时，此文件通常被称为编译单元。

- 每个编译单元都必须有一个后缀名.java，而在编译单元内则可以有一个public类，该类的名称必须与文件的名称相同。

- 每个编译单元只能有一个public类，否则编译器无法接受

- 如果在该编译单元之中还有额外类的话，那么在包之外的世界是无法看到这些类的，因为他们不是public类，主要用来为public类服务。

### 动态绑定 ###
将一个 方法调用 同一个 方法主体 关联起来被称为动态绑定。

若在程序执行前进行绑定，叫做前期绑定。否则叫做后期绑定、动态绑定、运行时绑定。

Java中除了static方法和final方法之外，其他所有的方法都是后期绑定。


### 避免构造器内部的多态方法的行为 ###

先了解对象的初始化过程再看这个代码。

	class Glyph{
		void draw(){
			print("Glyph.draw()");
		}
	
		Glyph(){
			draw(); 									………………………………1
		}
	}
	
	class RoundGlyph extends Glyph{
		private int radius = 1;
	
		RoundGlyph(int r){
			radius = r;
			print("RoundGlyph(),radius = " + radius);		…………………………………………2
		}
	
		void draw(){
			print("RoundGlyph.draw(),radius = " + radius);
		}
	}
	
	//main方法如下
	public static void main(String[] args){
		new RoundGlyph(5);
	}
	
	/*output :
	*RoundGlyph.draw(),radius = 0;
	*RoundGlyph(),radius = 5;
	*/


先执行Glyph()，再执行RoundGlyph()
Glyph()中会调用RoundGlyph.draw()，而此时radius还未调用成员的初始化方法，只是存储空间被清零了，故输出0

### 协变返回类型 ###

导出类中的被覆盖方法可以返回基类方法的返回类型的某种导出类型：

    class Grain{
    }
    
    class Wheat extends Grain{
    }
    
    class Mill{
    	Grain process(){
    		return new Grain();
    	}
    }
    
    class WheatMill extends Mill{
    	Wheat process(){
    		return new Wheat();
    	}
    }

### 内部类 ###
好处之一是使对某接口的实现能够做到不可见并且不可用。隐藏细节。

如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，则会要求参数引用为final

#### nested class ####
如果不需要内部类与其外围类之间有联系，可以将内部类声明为static，这通常称为嵌套类。

普通的内部类不能有static数据和字段，也不能包含嵌套类。但是嵌套类可以。

#### 接口内部的嵌套类 ####
正常情况下不能在接口内部放置任何代码，但是嵌套类可以作为接口的一部分，放到接口中的任何类都是默认static和public的。

因为类的修饰符是static的，只是将嵌套类置于接口的命名空间中，这并不违反接口的规则。甚至可以在内部类中实现其外围接口。

### closure 闭包###
指一个可调用的对象，记录了一些信息，来自于创建它的作用域

### Queue ###
- peek()&element()
  - peek()在队列为空时返回null,element()返回NoSuchElementException
- remove()&pull()
  - poll()在队列为空时返回null，而remove()会抛出NoSuchElementException

### printStackTrace() ###
这个方法将返回一个由栈轨迹中的元素所构成的数组，其中每一个元素都表示栈中的一帧。

如果想在catch中再次throw，则最好使用fillInStackTrace更新调用栈信息
    try{
    	throw new Exception("bad one");
    }catch(Exception e){
    	//TODO
    
    	throw (Exception)e.fillInStackTrace();
    }

### 异常链 ###
当想要捕获一个异常后抛出另一个异常，并且希望把原始异常的信息保存下来。

所有Throwable的子类在构造器中都可以接受一个cause对象作为参数，表示原始异常。

Error、Exception、RuntimeException具有带cause参数的构造器，其他类型的异常提供了initCause()方法

### Throwable ###
- Error
  - 编译时和系统错误
- Exception
  - 可以被抛出的基本类型

#### RuntimeException ####
会自动被Java虚拟机抛出，不必再异常说明中把它们列出来。

尽管通常不用捕获RuntimeException，但是可以抛出。

如果RuntimeExcepton没有被捕获而直达main()，那么在程序退出前嫁过调用异常的printStackTrace()方法

### 异常丢失 ###
前一个异常被后一个冲掉

    try{
    	try{
    		throw new MissingException();
    	}finally{
    		throw new CatchedException();
    	}
    }catch(Exception e){
    	print
    }

在finally子句中返回

### 异常的限制 ###
当覆盖方法的时候，智能抛出在基类方法的异常说明里列出的那些异常。这意味着当基类使用的代码应用到其派生类对象的时候，一样可以工作。

### 格式化说明符 ###

%[argument_index$][flags][width][.precision]conversion

- argument_index
- flags
- width
  - 控制一个域的最小尺寸
- .precision
  - 指明最大尺寸
    - 应用于String时，表示输出字符的最大数量
    - 应用于浮点数时，表示小数部分要显示出来的位数，如果小数位数过多则舍入，太少则补0
- conversion

### 正则表达式 ###
在其他语言中\\表示普通的反斜线，而在Java中，\\表示插入正则表达式中的反斜线(有点拗口，即Java中一般是需要将其他语言中\翻倍)

Java中表示数字为\\d,如果想插入一个普通的反斜线，则应该是\\\\，不过换行和制表符之类的东西只需使用单反斜线\n\t

- ?
  - 0或1
- +
  - 1或多个

字符类
- .
  - 任意字符
- [abc]
  - 包含a、b、c的任何字符
- [^abc]
  - 除了abc的任何字符
- [a-zA-Z]
  - 从a到z或A到Z的任何字符
- [a-z&&[hij]]
  - 交，即任意h、i或j
- \s
  - 空白符（空格、tab、换行、换页、回车）
- \S
  - 非空白符（[^\s]）
- \d
  - 数字
- \D
- \w
  - 词字符[a-zA-Z0-9]
- \W


- ^ 
- $
- \b
- \B
- \G

### 类字面常量 ###
当使用.class来创建对Class对象的引用时，不会自动的初始化该Class对象，为了使用类而做的准备工作实际包含三个步骤：
- 加载
    - 由类加载器执行，查找字节码，并从这些字节码中创建一个Class对象
- 链接
    - 在链接阶段将验证类中的字节码，为静态域分配存储空间，并且如果必须的话，将解析这个类创建的对其他类的所有引用
- 初始化
    - 如果该类具有超类，则对其初始化，执行静态初始化器和静态初始化块

### 协变 ###
泛型不是协变的，但数组是协变的。

比如：

    //传入Collection<Square>将会报错
    public static void test(Collection<Shape> arr){
        //do sth
    }

解决方法：

    //使用通配符
    public static void test(Collection<? extends Shape>){}

### 加速 ###

HashMap中的代码：

    /**
     * Returns the value of the mapping with the specified key.
     *
     * @param key
     *            the key.
     * @return the value of the mapping with the specified key, or {@code null}
     *         if no mapping for the specified key is found.
     */
    public V get(Object key) {
        if (key == null) {
            HashMapEntry<K, V> e = entryForNullKey;
            return e == null ? null : e.value;
        }
    
        // Doug Lea's supplemental secondaryHash function (inlined)
        int hash = key.hashCode();
        hash ^= (hash >>> 20) ^ (hash >>> 12);
        hash ^= (hash >>> 7) ^ (hash >>> 4);
    
        //使用tab
        HashMapEntry<K, V>[] tab = table;
        for (HashMapEntry<K, V> e = tab[hash & (tab.length - 1)];
                e != null; e = e.next) {
            //使用eKey
            K eKey = e.key;
            if (eKey == key || (e.hash == hash && key.equals(eKey))) {
                return e.value;
            }
        }
        return null;
    }

其中使用tab和eKey应该是将对象堆中持有的内存放到栈里，达到加速的效果。

### Arrays ###
Arrays.fill(,)
System.arraycopy()
Arrays.equals()

### Thread ###
- run
    - 用当前线程跑Thread中run的方法体
- start
    - 用新线程进行run方法的调用