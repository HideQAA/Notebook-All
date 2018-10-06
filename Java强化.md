# JAVA 强化 #
---
### Java集合类的使用场景 ###

    1) Collection
    一组"对立"的元素，通常这些元素都服从某种规则
     　　1.1) List必须保持元素特定的顺序
     　　1.2) Set不能有重复元素
     　　1.3) Queue保持一个队列(先进先出)的顺序
    2) Map
    一组成对的"键值对"对象

**1.迭代器接口** Interface Iterable，只一个方法: iterator()。它返回一个代表当前集合对象的泛型<T>迭代器，用于之后的遍历操作

**2.集合接口** Collection
 

- Set
   
   * **HashSet** 使用HASH算法来存储集合中的元素，因此具有良好的存取和查找性能
      * **LinkedHashSet** 使用链表维护元素的次序,在迭代访问Set里的全部元素时(遍历)将有很好的性能
   
   * **TreeSet** 是SortedSet接口的实现类，TreeSet可以确保集合元素处于排序状态 
   
   * **EnumSet** 是一个专门为枚举类设计的集合类,EnumSet的集合元素也是有序的

- List 

   * **ArrayList** 是基于数组实现的List类，它封装了一个动态的增长的、允许再分配的Object[]数组
   
   * **LinkedList** 实现List接口，能对它进行队列操作,可以根据索引来随机访问集合中的元素

   * **Vector** 提供的一个子类Stack，用于模拟"栈"这种数据结构

- Queue 用于模拟"队列"这种数据结构,队列不允许随机访问队列中的元素

   * **PriorityQueue** 按照队列元素的大小进行重新排序
   
   * **Deque** 接口代表一个"双端队列"，双端队列可以同时从两端来添加、删除元素


**3.映射接口** map

- Map
  
   * **HashMap** 两个key通过equals()方法比较返回true、两个key的hashCode值也必须相等
      * **LinkedHashMap** 使用双向链表来维护key-value对的次序
   
   * **TreeMap** TreeMap就是一个红黑树数据结构，可以保证所有的key-value对处于有序状态

   * **WeakHashMap** 如果WeakHashMap对象的key所引用的对象没有被其他强引用变量所引用，则这些key所引用的对象可能被垃圾回收，而后自动删除这些key所对应的key-value对
   
   * **EnumMap** 的所有key都必须是单个枚举类的枚举值


![](https://images0.cnblogs.com/blog/497634/201309/08171028-a5e372741b18431591bb577b1e1c95e6.jpg)



[https://www.cnblogs.com/LittleHann/p/3690187.html](https://www.cnblogs.com/LittleHann/p/3690187.html)

**重点：**[http://www.cnblogs.com/skywang12345/p/3323085.html](http://www.cnblogs.com/skywang12345/p/3323085.html)


源码的实质：java源码就是先实现了HashMap、TreeMap等集合，然后通过包装一个所有的value都为null的Map集合实现了Set集合类


**集合架构的接口和抽象类粗览：**

1. collection是接口，collections是工具类，提供了一系列静态方法实现对各种集合的搜索、排序、线程安全等操作

2. 工具类包括(Iterator迭代器、Enumeration枚举类、Arrays和Collections)，Arrays操作数组的工具类，Collections操作集合的工具类。

3. **Collection接口**的所有子类(直接子类和间接子类)都必须实现2种构造函数：**不带参数的构造函数** 和 **参数为Collection的构造函数**。带参数的构造函数，可以用来转换Collection的类型。

4. **Set接口**的API和Collection接口完全一样，**List接口** 相比于Collection有自己额外的API接口，主要有“**添加、删除、获取、修改指定位置的元素**”、“**获取List中的子队列**”等。

5. **AbstractCollection**是一个抽象类，它实现了Collection中除iterator()和size()之外的函数；**AbstractList抽象类** 中，实现了iterator()接口；**AbstractSet抽象类**也实现了Collection中除iterator()和size()之外的函数。

6. **Iterator接口**是集合的迭代器，提供的API接口包括：是否存在下一个元素、获取下一个元素、删除当前元素，Iterator遍历Collection时，是fail-fast机制的；**ListIterator接口**是一个继承于Iterator的接口，它是队列迭代器，它新增了添加、是否存在上一个元素、获取上一个元素等等API接口。

7. **Map 接口**提供三种collection 视图，允许以键集（**keySet()**）、值集（**values()** ）或键-值映射关系集（**entrySet()**）的形式查看某个映射的内容，还提供了“键-值对”、“根据键获取值”、“删除键”、“获取容量大小”等方法。////  Map 的实现类应该提供2个“标准的”构造方法：第一个，void（无参数）构造方法，用于创建空映射；第二个，带有单个 Map 类型参数的构造方法，用于创建一个与其参数具有相同键-值映射关系的新映射。

8. **Map.Entry接口**是Map中内部的一个接口，Map.Entry是键值对，Map通过 entrySet() 获取Map.Entry的键值对集合，从而通过该集合实现对键值对的操作。 

9. **AbstractMap抽象类**要实现不可修改的映射，编程人员只需扩展此类并提供 entrySet 方法的实现即可；要实现可修改的映射，编程人员必须另外重写此类的 put 方法，entrySet().iterator() 返回的迭代器也必须另外实现其 remove 方法。  

10. 插入有序 **SortedMap** 的所有元素都必须实现 Comparable 接口。///SortedMap 实现类都应该提供 4 个“标准”构造方法:void（无参数）构造方法，带有一个 Comparator 类型参数的构造方法，带有一个 Map 类型参数的构造方法，带有一个 SortedMap 类型参数的构造方法。

11. **NavigableMap接口**是一个可导航的键-值对集合，提供了获取“键”、“键-值对”、“键集”、“键-值对集”的相关方法。

12. **Dictionary**是JDK 1.0定义的键值对的接口，它也包括了操作键值对的基本函数。
 

<font color="red" size="3">
 **fail-fast** 机制是java集合中的一种错误机制。
</font>

**原理**：当多个线程对同一个集合进行操作的时候，某线程访问集合的过程中，该集合的内容被其他线程所改变(即其它线程通过add、remove、clear等方法，改变了modCount的值)；这时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。

**例如**：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。

**解决办法：**在多线程环境下使用fail-fast机制的集合，建议使用“java.util.concurrent包下的类”去取代“java.util包下的类”。如将 **ArrayList** 替换为 **CopyOnWriteArrayList**。

**解决办法原理：**CopyOnWriteArrayList不会抛ConcurrentModificationException，是因为所有改变其内容的操作（add、remove、clear等），都会copy一份现有数据，在现有数据上修改好，在把原有数据的引用改成指向修改后的数据。

---

### Java泛型 ###

**1.参数化类型** 将原来的具体类型参数化，类似于方法中的变量参数，可以分为类型形参（定义用到<E>）和类型实参（使用和调用的时候用到）


**2.泛型接口、泛型类和泛型方法** 泛型类型在逻辑上可以看成是多个不同的类型，实际上都是相同的基本类型


**3.类型通配符** 类型通配符一般是使用 ? 代替具体的类型实参。例如Box<?>在逻辑上是Box<Integer>、Box<Number>...等所有Box<具体类型实参>的父类。**类型通配符上限** 通过形如Box<? extends Number>形式定义，相对应的，**类型通配符下限** 为Box<? super Number>形式。

[https://www.cnblogs.com/lwbqqyumidi/p/3837629.html](https://www.cnblogs.com/lwbqqyumidi/p/3837629.html)

---

### IO ###

**IO模型** 使用Decorator模式，按功能划分Stream

[https://www.cnblogs.com/LittleHann/p/3678685.html](https://www.cnblogs.com/LittleHann/p/3678685.html)


**以字节为单位的输入流和输出流关联的框架图：**


![](https://images0.cnblogs.com/blog/497634/201310/20234245-b708d62c6397495db7915d8fee6616f7.jpg)


**以字符为单位的输入流和输出流关联的框架图：**


![](https://images0.cnblogs.com/blog/497634/201310/20234410-c986ccb259594865ae75f14f19e1179f.jpg)


**字节转换为字符流的框架图：**


![](https://images0.cnblogs.com/blog/497634/201310/20234430-bb419718ff01462c8d94fc2ac3e1aeb6.jpg)


**字节和字符的输入流对应关系：**

![](https://images0.cnblogs.com/blog/497634/201310/20234451-97f7312056a642ccb58ca02a2803dbb4.jpg)


**字节和字符的输出流对应关系：**

![](https://images0.cnblogs.com/blog/497634/201310/20234541-d488f6a75e524979acfe8a77ff14ec78.jpg)

**重点：**[https://www.cnblogs.com/skywang12345/p/io_01.html](https://www.cnblogs.com/skywang12345/p/io_01.html)



**IO框架粗览：**



---

### 异常框架 ###


![](https://images0.cnblogs.com/blog/497634/201402/111228085926220.jpg)

[https://www.cnblogs.com/skywang12345/p/3544168.html#a1](https://www.cnblogs.com/skywang12345/p/3544168.html#a1)

---

### 多线程 ###


Java线程简介：

[https://www.ibm.com/developerworks/cn/education/java/j-threads/j-threads.html](https://www.ibm.com/developerworks/cn/education/java/j-threads/j-threads.html)

线程池和工作队列：

[https://www.ibm.com/developerworks/cn/java/j-jtp0730/index.html](https://www.ibm.com/developerworks/cn/java/j-jtp0730/index.html)

---

###