
### 引用介绍 ###
---

强引用  >  软引用  >  弱引用  >  虚引用


* 如果一个对象具有强引用，那垃圾回收器绝不会回收它。
* 如果一个对象只具有软引用，则内存空间足够，垃圾回收器就不会回收它；如果内存空间不足了，就会回收这些对象的内存。软引用可用来实现内存敏感的高速缓存。
* 一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。
* 如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。


软引用和弱引用可以和一个引用队列联合使用,虚引用必须和引用队列 （ReferenceQueue）联合使用。

<table>
<tr>
<th>级别</th>
<th>什么时候被GC</th>
<th>用途</th>
<th>生存时间</th>
</tr>
<tr>
<td>强引用</td>
<td>从来不会</td>
<td>对象的一般状态</td>
<td>JVM停止运行时终止</td>
</tr>
<tr>
<td>软引用</td>
<td>内存不足时</td>
<td>内存高速缓存</td>
<td>内存不足时终止</td>
</tr>
<tr>
<td>弱引用</td>
<td>垃圾回收时</td>
<td>对象缓存</td>
<td>GC运行后终止</td>
</tr>
<tr>
<td>虚引用</td>
<td>Unknown</td>
<td>Unknown</td>
<td>Unknown</td>
</tr>
</table>
 

知识整理：

1. finalize()方法是在JVM回收内存时执行的，但JVM并不保证在回收内存时一定会调用finalize()

2. JVM的垃圾回收机制，在内存充足的情况下，除非你显式调用System.gc()，否则它不会进行垃圾回收；在内存不足的情况下，垃圾回收将自动运行

---

### 内部类和匿名内部类 ###

---

![](https://i.imgur.com/sGNdkH9.png)

**内部类**分为成员内部类和局部内部类

* **成员内部类** 定义在外部类的成员位置，可以直接访问外部类的成员，成员内部类和外部类不存在继承关系
* **局部内部类** 定义在外部类的方法里面，可以直接访问外部类的成员，局部内部类在访问局部变量时，被访问的变量必须用 **final** 修饰（原因：如果变量不定义为final，外部类的方法执行完毕后，这个局部变量肯定被gc了。这时候内部类的某个方法还没执行完，却找不到他引用的外部变量了。如果定义为final，Java就会将这个变量复制一份作为成员变量置于内部类中。）

**匿名对象** 没有名字的对象，例如调用方法： new Person().show()，适用于一个对象只使用一次的场景，使用完毕就变成垃圾被回收，可以作为实际参数传递。

**匿名内部类** 在一个类中获取一个接口的实现类对象或者获取一个抽象类的子类对象
    

    class Outer
    {
    	public void method()
    	{
    		new Person()                   //person为抽象类
    		{
    			public void work()
    			{
    				System.out.println(“work”);
    			}
    		}.work();
    	}
    }
     
    new Outer().method();
     
    class Outer
    {
    	public void method()
    	{
    		Phone p = new Phone()            //Phone为接口
    		{
    			public void on()
    			{
    				System.out.println("on");
    			}
     
     
    			public void off()
    			{
    				System.out.println("off");
    			}
    		};
    		
    		p.on();
    		p.off();
    	}
    }
     
    new Outer().method();


---

### 反射机制

---

**反射机制** 在运行状态中，我们可以根据“ **类的部分已知的信息** ”来还原“ **类的全部的信息** ”。类的部分已知的信息可以是**类名** 或者 **类的对象**；类的全部信息包括类的**属性、方法、继承关系和注解**等等。

反射机制步骤：

1. 根据“类名”来获取对应类的Class对象。
2. 通过Class对象的函数接口，来读取“类的构造函数，成员变量”等信息。

获取Class对象的方法：

* Class.forName("类名字符串") （注意：类名字符串必须是全称，包名+类名）
* 类名.class
* 实例对象.getClass()
* "类名字符串".getClass()

Class的API说明：

分为四部分：**构造函数，成员方法，成员变量，类的其它信息**

参考博客：[https://www.cnblogs.com/skywang12345/p/3345205.html](https://www.cnblogs.com/skywang12345/p/3345205.html)

---

### Java Annotation认知

---
**Annotation架构图**

![](https://i.imgur.com/zMrprBW.jpg)

---

###comparable && comparator

---

* Comparable 可以认为是一个内比较器，至于具体和另一个实现了 Comparable接口 的类如何比较，则依赖 compareTo方法 的实现，compareTo方法 的返回值是 int
* Comparator 可以认为是是一个外比较器，Comparator接口 里面有一个 compare方法，方法有两个参数To1 和 To2，是泛型的表示方式，方法返回值是 int

总结：比较两个类的实例对象，如果类里边实现了Comparable接口的compareTo方法，可以直接调用实例对象的compareTo方法来比较两个对象的大小（**传入要比较的另外一个对象作为参数**）；
如果没有实现Comparable接口，但是还想比较这两个类的实例对象的大小，新建一个比较类来实现Comparator接口的compare方法，调用比较类的实例对象的compare方法来比较两个对象的大小（**传入要比较的两个对象作为参数**）。


[http://www.cnblogs.com/xrq730/p/4850140.html](http://www.cnblogs.com/xrq730/p/4850140.html)