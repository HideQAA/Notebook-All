## Java Bean ##

JavaBean是一个遵循特定写法的Java类，是一种Java语言编写的可重用组件，所有遵循“一定编程原则”的Java类都可以被称作JavaBean。它的方法命名，构造及行为必须符合特定的约定：

<font color=#0099ff>
1、这个类必须具有一个公共的(public)无参构造函数；

2、所有属性私有化（private）；

3、私有化的属性必须通过public类型的方法（getter和setter）暴露给其他程序，并且方法的命名也必须遵循一定的命名规范; 

4、这个类应是可序列化的。（比如可以实现Serializable 接口，用于实现bean的持久性）
</font>


JavaBean 在Java EE开发中，通常用于**封装数据**，对于遵循以上写法的JavaBean组件，其它程序可以通过反射技术实例化JavaBean对象（**内省机制**），并且通过反射那些遵循命名规范的方法，从而获知JavaBean的属性，进而调用其属性保存数据。


javaBean 在MVC设计模型中是model，又称**模型层**，在一般的程序中，我们称它为**数据层**，就是用来设置数据的属性和一些行为，然后我会提供获取属性和设置属性的get/set方法


---

什么是JavaBean、bean? 什么是POJO、PO、DTO、VO、BO ? 什么是EJB、EntityBean？
[https://blog.csdn.net/chenchunlin526/article/details/69939337](https://blog.csdn.net/chenchunlin526/article/details/69939337)


markdown编辑器语法——字体、字号与颜色：

[https://blog.csdn.net/testcs_dn/article/details/45719357/#comments](https://blog.csdn.net/testcs_dn/article/details/45719357/#comments)