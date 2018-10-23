牛客网错题
---

**1.** 下面所示的java代码，运行时，会产生（）类型的异常
		
	     int Arry_a[] = new int[10];
		 System.out.println(Arry_a[10]);

解析：int类型数组，默认值为0，在长度允许范围类，即使没有赋值，打印出来也为0；若访问数组角标越界会抛出 ArrayIndexOutOfBoundsException


**2.** final 定义的变量，不是必须要在定义的同时完成初始化，可以在构造方法中完成初始化；final修饰方法，不能被子类重写，但是可以被重载

**3.** sleep是线程类的方法，wait是object的方法；wait进入等待锁定池，只有针对此对象发出notify方法获得对象锁进入就绪状态

**4.** 判断输出     
     
    Boolean flag = false;
     if (flag = true)
	{
	System.out.println(“true”);
	}
	else
	{
	System.out.println(“false”);
    }

=是赋值，==是比较运算符

**5.** 类的加载包括：加载，验证，准备，解析，初始化；生成java.lang.Class对象是在加载时进行的，作为方法区这个类的各种数据的访问入口；赋予类变量的初值；执行static块代码；类方法解析

**6.** 判断输出 
			
	public static void main (String[] args) { 
	    String classFile = "com.jd.". replaceAll(".", "/") + "MyClass.class";
		System.out.println(classFile);
	}

由于replaceAll方法的第一个参数是一个正则表达式，而"."在正则表达式中表示任何字符，把所有字符都替换了

**7.** 一个线程一个栈，一个方法一个栈帧。

String str = "test ok" 直接在运行时常量池内分配字符串“test ok”，并将该地址传递给str;

String str = new String(“test ok”) 在堆上新建一个字符串对象，将地址传给str。

**8.** == 若为基本类型，且都是数值类型返回true；若比较的是引用，只要指向同一引用对象，即相等

**9.** 接口中的变量默认是public static final 的，方法默认是public abstract 的。接口就是提供一种统一的协议,而接口中的属性也属于协议中的成员.它们是公共的,静态的,最终的常量.相当于全局常量。接口中不能出现变量。

10.重写：方法名、参数、返回值相同；**访问修饰符的限制一定要大于被重写方法的访问修饰符（public>protected>default>private）**；重写方法一定不能抛出新的检查异常或者比被重写方法申明更加宽泛的检查型异常。（继承的时候用）

重载：参数类型、个数、顺序至少有一个不相同；**不能重载只有返回值不同的方法名**（可以用于同一类里边）

**11.** 输出结果：0

	package algorithms.com.guan.javajicu; 
	public class Inc { 
	    public static void main(String[] args) { 
	       Inc inc = new Inc(); 
	       int i = 0; 
	       inc.fermin(i); 
	       i= i ++; 
	       System.out.println(i);
	    
	    } 
	    void fermin(int i){ 
	       i++; 
	    } 
	}
解析：
i=i++;等同于：temp=i；i=i+1; i=temp   

i=++i;则等同于：i=i+1;temp=i;i=temp

i=i++和i++的JVM指令不一样

[https://www.nowcoder.com/test/question/done?tid=19209497&qid=14765#summary](https://www.nowcoder.com/test/question/done?tid=19209497&qid=14765#summary)


**12.** 普通类也就是外部类只能用 public, abstract 和 final 修饰。

（成员）内部类：可理解为外部类的成员，所以修饰类成员的public、protected、default、private、static等关键字都能使用。

局部内部类：出现在方法里的类，不能用上述关键词来修饰。

接口必须为public abstract，**且没有方法体**,因为接口是高度抽象的类，它的存在就是被其他类实现；接口中声明的变量必须为 public static final；接口中定义的方法必须为public abstract，**默认也为public abstract**,目前也可以用static了，**但是必须得有方法体**。

**13.** 值传递和引用传递

值传递：方法调用时，实际参数把它的值传递给对应的形式参数，方法执行中形式参数值的改变不影响实际参数的值。（创建副本）

引用传递：方法调用时，实际参数的引用（地址）被传递给方法中相对应的形式参数，在方法执行中，对形式参数的操作实际上就是对实际参数的操作，方法执行中形式参数值的改变将会影响实际参数的值。
（不创建副本）

**在JAVA中都是值传递，基本类型传递的是值的副本，引用类型传递的是引用也就是地址的副本（交换不会影响原值，修改会影响原值）**

传递基本数据类型

	 1 public class TestValue {
	 2     public static void main(String[] args) {
	 3         int i = 3;
	 4         int j = 4;
	 5         change(i,j);
	 6         System.out.println(i);     //输出的是3
	 7         System.out.println(j);     //输出的是4 
	 8     }    
	 9     public static void change(int i,int j){    //创建了副本，没有改变实参值
	10         int temp = i;
	11         i = j;
	12         j = temp;
	13     }
	14 }

传递引用数据类型

		 1 public class TestValue1 {
		 2     public static void change(int[] count){   //传递了引用count的副本
                                 //但是他们指向的是同一个对象
		 3         count[0] =10;
		 4     }
		 5     public static void main(String[] args) {
		 6         int[] count = {1,2,3,4,5};
		 7         change(count);
		 8         System.out.println(count[0]);   //输出10
		 9     }
		10 }

Java中引用类型的值传递

	class Student{
	　　private float score;
	　　public Student(float score) {
	        this.score = score;
	    }
	    public void setScore(float score) {
	        this.score = score;
	    }
	    public float getScore() {
	        return score;
	    }
	}
	public class ParamTest {
	    public static void main(String[] args) {
	        Student a = new Student(0);
	        Student b = new Student(100);
	        System.out.println("交换前：");
	        System.out.println("a的分数：" + a.getScore() + "--- b的分数：" + b.getScore());
	
	        swap(a, b);
	
	        System.out.println("交换后：");
	        System.out.println("a的分数：" + a.getScore() + "--- b的分数：" + b.getScore());    
                   //交换前和交换后的值一样，都是0和100
	    }
	    public static void swap(Student x, Student y) { //将a和b的拷贝分别赋给x和y
	        Student temp = x;
	        x = y;      //swap方法体完成的是x和y的交换，a和b并没有变化
	        y = temp;
	    }
	}


![](https://i.imgur.com/e734vda.png)


**14.**基本数据类型（**八种基本类型。六种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。**） 整数型的默认是 **int** 类型，带小数的默认是 **double** 类型

byte:8位，有符号

short:16位，有符号

int:32位，4个字节，有符号

long:64位，8个字节，有符号

float:浮点数，32位

double:浮点数，64位

char:16位 Unicode 字符

boolean:1位

boolean=byte(1字节)<char=short(2字节)<int =float (4字节)<long=double(8字节) C语言中char一个字节

ANSI码 对于windows系统来说的话就是指当前的系统编码。

**java中如果碰到char、byte和short参与运算时，会自动将这些值转换为int类型然后再进行运算。**

**字符用单引号，字符串用双引号**

**引用数据类型：数组，类，接口**

**15.** Java中有两种初始化块:静态初始化块和非静态初始化块

**静态函数块static{}：** 会在类被加载的时候执行且仅会被执行一次，一般用来初始化静态变量和调用静态方法，不管new多少次对象实例，static{}都只执行一次。（主要目的就是给类变量赋初值）

**普通初始化块：** 在每个对象生成时都会被执行一次,可以初始化类的实例变量，会在构造函数执行时,且在构造函数主体代码执行之前被运行。（主要目的实例化一些共有的实例变量，减少构造方法中的代码量）

**static{}语句块执行的时机，即类被加载准确含义：**

1. 用Class.forName()显示加载的时候;

2. 实例化一个类的时候，如:Test t=new Test(); **其实 Test t=new Test() 和 Test t=(Test)Class.forName().newInstance() 这两条语句效果相同**

3. 调用类的静态方法的时候;

4. 调用类的静态变量的时候。**调用类的静态常量的时候，是不会加载类的**

当一个类中有多个static{}的时候，按照static{}的定义顺序，从前往后执行；先执行完static{}语句块的内容，才会执行调用语句；

[https://blog.csdn.net/lubiaopan/article/details/4802430](https://blog.csdn.net/lubiaopan/article/details/4802430)

涉及到继承的**静态初始化代码块、构造代码块、构造方法**，执行顺序如下：

执行父类的静态代码块、执行子类的静态代码块、执行父类的初始化代码块、执行父类的构造函数、执行子类的初始化代码块、执行子类的构造函数

**16.** Java中数组的表示 **float average[]** 和 **float[] average**

**在java 中，声明一个数组时，不能直接限定数组长度，只有在创建实例化对象时，才能对给定数组长度**


**17.** 基本数据类型与包装类进行==操作时，包装类会自动拆包装，然后进行值比较；

Byte,Short,Integer,Long,Character这5种整型的包装类也只是在对应值小于等于127并且大于等于-128时才可使用常量池；

Integer.valueOf方法中也有判断，**如果传递的整型变量>= -128并且小于127时会返回IntegerCache类中一个静态数组中的某一个对象**（加入缓存是为了优化代码），** 否则会返回一个新的Integer对象**。

	public static void main(String[] args) {
	         
	        Integer a = 127;
	        Integer b = 127;
	         
	        Integer c = 128;
	        Integer d = 128;
	         
	        System.out.println(a == b);  //true
	        System.out.println(c == d);  //false
	    }



	public class EqualsMethod
	{
	    public static void main(String[] args)
	    {
	        Integer n1 = new Integer(47);
	        Integer n2 = new Integer(47);
	        System.out.print(n1 == n2);  //false
	        System.out.println(n1 != n2); //true
			Integer n3 = Integer.valueOf(47);
			Integer n4 = Integer.valueOf(47);
			System.out.print(n3 == n4);  //true
	        System.out.println(n3 != n4); //false
	    }
	}

![](https://i.imgur.com/8IsjBBJ.png)

**18.** 自动拆装箱

* 基本型和基本型封装型进行“==”运算符的比较，基本型封装型将会自动拆箱变为基本型后再进行比较；
* 两个Integer类型进行“==”比较，如果其值在-128至127，那么返回true，否则返回false, 这跟Integer.valueOf()的缓冲对象有关，在这个区间之外，会返回一个对象；
* 两个基本型的封装型进行equals()比较，首先equals()会比较类型，如果类型相同，则继续比较值，如果值也相同，返回true；
* 基本型封装类型调用equals(),但是参数是基本类型，这时候，先会进行自动装箱，基本型转换为其封装类型，再进行上面的比较；
* 两个基本型的封装型进行==比较，不会拆箱，且不能比较。

**19.** java中String内部是用char[]数组实现的

**20.** math.round(double a) 返回的是最接近参数的long值，如果出现向上向下距离一样的数值，取较大的一个。如：Math.round(11.5) ，首先与 11.5最接近的有两个整数 11 和 12，取较大的那结果就是12；Math.round(-11.5)， 首先与 -11.5最接近的有两个整数 -11 和 -12，取较大的那结果就是-11。

**21.** Java修饰符：

* 访问权限修饰符：

![](https://i.imgur.com/mGxB0cn.png)

* 非访问权限修饰符：

 * static 修饰符，用来创建类方法和类变量；
 * final 修饰符，用来修饰类、方法和变量，final 修饰的类不能够被继承，修饰的方法不能被继承类重新定义，修饰的变量为常量，是不可修改的；
 * abstract 修饰符，用来创建抽象类和抽象方法( **抽象方法不能有方法体 {}** )；
 * synchronized 用于多线程的同步；
 * volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值；当成员变量发生变化时，会强制线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值；
 * transient 序列化的对象包含被 transient 修饰的实例变量时，java 虚拟机(JVM)跳过该特定的变量。

在类和接口前面应用总结：

![](https://i.imgur.com/71dWLKC.png)


**22.** 任何字符与字符串相加都是字符串，字符串前面的按原来格式相加，字符串后面的按字符串相加。

	public static void main(String args[]) {
	        int x = 20;
	        int y = 5;
	        System.out.println(x + y + "" + (x + y) + y);  //输出25255
	    }
	}

**23.** 创建socket连接：

* 服务器端：ServerSocket提供的实例 ServerSocket server = new ServerSocket(端口号) 
* 客户端：Socket提供的实例 Socket client = new Socket(IP地址，端口号)

**24.** StringBuffer是线程安全，StringBuilder不是线程安全的

**25.** 异常：如果try语句里有return，返回的是try语句块中变量值。 

详细执行过程如下：
如果有返回值，就把返回值保存到局部变量中；
执行jsr指令跳到finally语句里执行；
执行完finally语句后，返回之前保存在局部变量表里的值。
如果try，finally语句里均有return，忽略try的return，而使用finally的return。

	public abstract class Test {
	    public static void main(String[] args) {
	        System.out.println(beforeFinally());    //输出1
	    }
	     
	    public static int beforeFinally(){
	        int a = 0;
	        try{
	            a = 1;
	            return a;      //return将返回结果保存到一个临时栈
	        }finally{
	            a = 2;         //执行完finally后再执行return
	        }
	    }
	}


return有两个作用，1是返回值，2是结束运行，就是不再执行后面的代码。

**26.** Java反射的功能：

* 在运行时判断任意一个对象所属的类；
* 在运行时构造任意一个类的对象；
* 在运行时判断任意一个类所具有的成员变量和方法；
* 在运行时调用任意一个对象的方法；
* 生成动态代理。


**27.** this不能在static的方法中使用，原因是this代表的是调用这个函数的对象的引用，静态方法在类加载的时候创建加载，此时没有创建对象

**28.** Java的程序种类包括：

* 内嵌于web文件中，有浏览器观看的applet
* 可独立运行的application
* 服务器端的servlets

**29.** 运算符优先级口诀：**单目算术位关系，逻辑三目后赋值**\

**30.** 输出结果：

	public class HelloSogou{
	     public static synchronized void main(String[] a){
	         Thread t=new Thread(){
	             public void run(){Sogou();}
	     };
	     //t.run();  输出的是SogouHello
  		 //t.start();    输出的是HelloSogou
	     System.out.print("Hello");
	     }
	     static synchronized void Sogou(){
	     System.out.print("Sogou");
	    }
	}

解析：当t.run时,为直接调用方法，此时只有一个主线程，调用main获得了HelloSogou.class的锁，当调用run方法时，此时还持有类对象的锁，所以可以直接调用。

当t.start时，为开启一个线程，但是main和Sogou是同一个锁，会发生冲突，所以main函数执行完毕后才会释放锁，Sogou方法才会执行。

**31.**

* ConcurrentHashMap采用分段锁技术，写操作加锁，读操作一般不加锁；
* CopyOnWriteArrayList添加的时候是需要加锁的，读的时候不需要加锁，适用于读多写少的并发场景

**32.**

泛型通配符： 

* 尖括号里的所有点之间互相赋值都是错，除非是俩相同的点；
* 尖括号小范围赋值给大范围，对；大范围赋值给小范围，错；如果某点包含在某个范围里，那么可以赋值；
* <? extends A> 代表小于等于A的范围，<? super A>代表大于等于A的范围，<?>代表全部范围。

**33.**

	public class Test{
	static{
	   int x=5;            //为局部变量，JVM加载完类文件后执行，执行完后释放
	}
	static int x,y;
	public static void main(String args[]){
	   x--;
	   myMethod( );
	   System.out.println(x+y+ ++x);        //输出为3
	}
	public static void myMethod( ){
	  y=x++ + ++x;
	 }
	}


**34.**

	package NowCoder;
	class Test {
	    public static void hello() {
	        System.out.println("hello");
	    }
	}
	public class MyApplication {
	    public static void main(String[] args) {
	        // TODO Auto-generated method stub
	        Test test=null;           //只要是使用到了Test类，都会加载静态hello方法
	        test.hello();             //输出hello
	    }
	}

**35.**

不指定ArrayList类型，存入数据，再次取出时，默认是Object类型。

**RTTI：** 运行时类型识别

* 传统的类型转换，由RTTI保证类型转换的正确性，如果执行一个错误的类型转换，就会抛出ClassCastException异常；
* 代表对象的类型的Class对象，通过查询Class对象（即调用Class类的方法）可以获取运行时所需的信息（**反射**）；
* 关键字instanceof，返回一个布尔值，告诉对象是不是某个特定类型的示例；

[https://www.cnblogs.com/Harley-Quinn/p/5263802.html](https://www.cnblogs.com/Harley-Quinn/p/5263802.html)

**36.**

常见的ASCII码值：空格32, 0是48，A是65，a是97

**37.**

Java集合框架

![](https://i.imgur.com/piJjWg8.png)

**38.**

管道：

1. 管道为空，读操作会被阻塞；管道满了，写操作会被阻塞；
2. 可以多个进程进行读写操作，但是不能同时写，可以同时读；
3. 匿名管道只能单向，命名管道可以双向。
 
**39.**

**在静态类里面调用类成员变量，不需要类名前缀。**

**抽象类可以申明对象，只是不能实例化。**

**String类不可变，指的是常量池中的String对象内容不可变。**

**40. JVM**

* -Xmx：最大堆大小
* -Xms：初始堆大小
* -Xmn：年轻代大小
* -XXSurvivorRatio：年轻代中Eden区与Survivor区的大小比值

![](https://i.imgur.com/hAwNzXx.png)

**41.**

**多态编程，编译看左边，运行看右边。**

**42.String, StringBuffer,StringBuilder的区别**

**可变与不可变：**

String类中使用字符数组保存字符串，因为有“final”修饰符，所以可以知道string对象是不可变的，如下：

**private final char value[];**

String 为不可变对象，一旦被创建，就不能修改它的值。对于已经存在的String对象的修改都是重新创建一个新的对象，然后把新的值保存进去。

StringBuilder与StringBuffer都继承自AbstractStringBuilder类，这两种对象都是可变的，如下：

**char[] value;**

StringBuffer 是一个可变对象，当对他进行修改的时候不会像String那样重新建立对象，它只能通过构造函数来建立，如 **StringBuffer sb = new StringBuffer();** 不能通过赋值符号对他进行付值，如 **sb = "welcome to here! **，对象被建立以后，在内存中就会分配内存空间，并初始保存一个null。向StringBuffer中赋值的时候可以通过它的append方法。
**sb.append("hello")**;

**是否多线程安全：** 

String中的对象是不可变的，也就可以理解为常量，显然 **线程安全**。

StringBuffer对方法加了同步锁或者对调用的方法加了同步锁，所以是 **线程安全的**。 

StringBuilder并没有对方法进行加同步锁，所以是 **非线程安全的**。

**StringBuilder 与 StringBuffer 共同点：**

StringBuilder、StringBuffer的方法都会调用AbstractStringBuilder中的公共方法，如super.append(...)。只是StringBuffer会在方法上加synchronized关键字，进行同步。

如果程序不是多线程的，那么使用StringBuilder效率高于StringBuffer。

**效率比较String < StringBuffer < StringBuilder，但是在String S1 =“This is only a”+“simple”+“test”时，String效率最高。**


**43.异常：**

**catch中的参数类型有父类子类的关系，此时应该将父类放在后面，子类放在前面。**

**44.**
**把一个文件的字符写进另一个文件中，首先应该把当前文件的字符写进计算机内存中。**

**45.一个.java文件中：**

* public权限类最多只能有一个；
* .java文件名只能是 public 权限的类的类名；
* 倘若这个文件中没有 public 类，则它的.java文件的名字是随便的一个类名；
* 用javac命令生成编译这个.java 文件的时候，则会针对每一个类生成一个.class文件。

**46.** Java语言中，如果"xyz"没有被创建过，String s =new String(“xyz”);创建了 **2** 个string object。

解析：

* jvm 首先在字符串常量池内里面看看找不找到字符串"xyz"；找到，进入下一步；否则，创建新的 String 对象，并“放到”字符串常量池里面；
* 然后由于遇到了 new，还会在堆创建 String 对象，其实是拷贝的常量池的那个，最后将其返回给 s1。将 new String("xyz") 拆分为 new String() 和 "xyz"。

**47.移位运算符：**

* << : 左移运算符，num << 1,相当于num乘以2
* >> : 右移运算符，num >> 1,相当于num除以2
* >>> : 无符号右移，忽略符号位，空位都以0补齐

**48.** Parent p1 = new Child()

对于成员变量，则p1只能调用父类的，不能调用子类的；p1是不能调用子类独有的方法的，会产生编译错误。

**绑定：一个方法的调用与方法所在的类关联起来**

**静态绑定：**（final、static、private）在程序执行前已经被绑定，也就是说在编译过程中就已经知道这个方法是哪个类的方法，此时由编译器获取其他连接程序实现。

**动态绑定：** 在运行根据具体对象的类型进行绑定。

**49.枚举：枚举类型的每个值都是对象**

创建枚举类时使用 enum 关键字，隐含了所创建的类型都是 java.lang.Enum 抽象类的子类，枚举类型的每一个值都将映射到 protected Enum(String name, int ordinal) 构造函数中，参数分别为每个值的名称，和被创建的顺序数。

	public enum EnumTest {
	    MON, TUE, WED, THU, FRI, SAT, SUN;
	}

相当于调用了七次 Enum(String name, int ordinal)

	ew Enum<EnumTest>("MON",0);
	new Enum<EnumTest>("TUE",1);
	new Enum<EnumTest>("WED",2);
	...


原理分析：enum 的语法结构和 class 的语法不一样，但是经过编译器编译之后产生的是一个class文件。该class文件经过反编译可以看到实际上是生成了一个类，该类继承了java.lang.Enum<E>，有构造函数，有final常量值，有方法等等。示例如下：

枚举类：

	public enum ColorEnum {
	    RED,BLUE,GREEN
	}

反编译后的类：

	public final class ColorEnum extends Enum
	{
	
	    //返回存储枚举实例的数组的副本。values()方法通常用于foreach循环遍历枚举常量。
	    public static ColorEnum[] values()
	    {
	        return (ColorEnum[])$VALUES.clone();
	    }
	    //根据实例名获取实例
	    public static ColorEnum valueOf(String s)
	    {
	        return (ColorEnum)Enum.valueOf(ColorEnum, s);
	    }
	
	    //私有构造方法，这里调用了父类的构造方法，其中参数s对应了常量名，参数i代表枚举的一个顺序(这个顺序与枚举的声明顺序对应，用于oridinal()方法返回顺序值)
	    private ColorEnum(String s, int i)
	    {
	        super(s, i);
	    }
	
	    //我们定义的枚举在这里声明了三个 ColorEnum的常量对象引用,对象的实例化在static静态块中
	    public static final ColorEnum RED;
	    public static final ColorEnum BLUE;
	    public static final ColorEnum GREEN;
	    //将所有枚举的实例存放在数组中
	    private static final ColorEnum $VALUES[];
	
	    static 
	    {
	        RED = new ColorEnum("RED", 0);
	        BLUE = new ColorEnum("BLUE", 1);
	        GREEN = new ColorEnum("GREEN", 2);
	        //将所有枚举的实例存放在数组中
	        $VALUES = (new ColorEnum[] {
	            RED, BLUE, GREEN
	        });
	    }
	}

[https://blog.csdn.net/javazejian/article/details/71333103](https://blog.csdn.net/javazejian/article/details/71333103)

[https://www.ibm.com/developerworks/cn/java/j-lo-enum/index.html](https://www.ibm.com/developerworks/cn/java/j-lo-enum/index.html)

**50. 继承关系中的构造函数**

父类没有无参构造函数的话，子类构造方法会报错。解决办法：

* 在父类加上无参构造函数
* 通过使用super去显式地调用父类有参构造函数
* 通过this去调用本类的其他构造函数

**默认构造函数是系统自动生成的无参构造函数**

	public class Demo { 	
		    class Super {  
				int flag=1;
	         	Super() {
	             	test();
	         	} 	
				void test() {
	            	System.out.println("Super.test() flag="+flag);
	         	}
	    	} 
			class Sub extends Super {
	            Sub(int i) {  
					flag=i;
	            	System.out.println("Sub.Sub() flag="+flag);
	       		}  
				void test() {
	            	System.out.println("Sub.test() flag="+flag);
	        	}
	    	}  
			public static void main(String[] args) {  
				new Demo().new Sub(5);
	     	}
	}

	输出：
	Sub.test() flag=1
	Sub.Sub() flag=5

**51. 可以将null赋给引用类型变量，但不可以将null赋给基本类型变量**

**可以把任何一种数据类型的变量赋给Object类型的变量**

**52.**

	package com.sunline.java;
	public class A implements B extends C{
	    public static void main(String args[]){
	        System.out.println("hello sunline!");
	    }
	}                 //编译错误，应该先继承再实现

**53.**

	class Chinese{
	    private static Chinese objref =new Chinese();
	    private Chinese(){}
	    public static Chinese getInstance() { return objref; }
	}
	
	public class TestChinese {
	    public static void main(String [] args) {
	    Chinese obj1 = Chinese.getInstance();
	    Chinese obj2 = Chinese.getInstance();
	    System.out.println(obj1 == obj2);
		}
	}             //单例模式，obj1和obj2其实是一个对象,返回true