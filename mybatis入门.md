mybatis
---

### ORM:对象关系映射 ###

一个持久化类和一个表对应，类的每个实例对应表中的一条记录，类的每个属性对应表的每个字段(domain类)。可以通过操作对象实现对数据库的记录的操作，跳过了 SQL 语句。

快速入门
---

**1. 导入jar包**

* mybatis包： mybatis-3.1.1.jar

* mysql驱动包： mysql-connector-java-5.1.7-bin.jar


**2. 创建数据库和表**

	1 create database mybatis;
	2 use mybatis;
	3 CREATE TABLE users(id INT PRIMARY KEY AUTO_INCREMENT, NAME VARCHAR(20), age INT);
	4 INSERT INTO users(NAME, age) VALUES('张三', 23);
	5 INSERT INTO users(NAME, age) VALUES('李四', 23);

**3. 添加Mybatis的配置文件conf.xml**

	 1 <?xml version="1.0" encoding="UTF-8"?>
	 2 <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
	 3 <configuration>
	 4     <environments default="development">
	 5         <environment id="development">
	 6             <transactionManager type="JDBC" />
	 7             <!-- 配置数据库连接信息 -->
	 8             <dataSource type="POOLED">
	 9                 <property name="driver" value="com.mysql.jdbc.Driver" />
	10                 <property name="url" value="jdbc:mysql://localhost:3306/mybatis" />
	11                 <property name="username" value="root" />
	12                 <property name="password" value="123" />
	13             </dataSource>
	14         </environment>
	15     </environments>
	16     
	17 </configuration>

**4. 定义表所对应的实体类**

	package me.gacl.domain;
	
	/**
	 * @author gacl
	 * users表所对应的实体类
	 */
	public class User {
	
	    //实体类的属性和表的字段名称一一对应
	    private int id;
	    private String name;
	    private int age;
	
	    public int getId() {
	        return id;
	    }
	
	    public void setId(int id) {
	        this.id = id;
	    }
	
	    public String getName() {
	        return name;
	    }
	
	    public void setName(String name) {
	        this.name = name;
	    }
	
	    public int getAge() {
	        return age;
	    }
	
	    public void setAge(int age) {
	        this.age = age;
	    }
	
	    @Override
	    public String toString() {
	        return "User [id=" + id + ", name=" + name + ", age=" + age + "]";
	    }
	}

**5. 定义操作users表的sql映射文件userMapper.xml**

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	<!-- 为这个mapper指定一个唯一的namespace，namespace的值习惯上设置成包名+sql映射文件名，这样就能够保证namespace的值是唯一的
	例如namespace="me.gacl.mapping.userMapper"就是me.gacl.mapping(包名)+userMapper(userMapper.xml文件去除后缀)
	 -->
	<mapper namespace="me.gacl.mapping.userMapper">
	    <!-- 在select标签中编写查询的SQL语句， 设置select标签的id属性为getUser，id属性值必须是唯一的，不能够重复
	    使用parameterType属性指明查询时使用的参数类型，resultType属性指明查询返回的结果集类型
	    resultType="me.gacl.domain.User"就表示将查询结果封装成一个User类的对象返回
	    User类就是users表所对应的实体类
	    -->
	    <!-- 
	        根据id查询得到一个user对象
	     -->
	    <select id="getUser" parameterType="int" 
	        resultType="me.gacl.domain.User">
	        select * from users where id=#{id}
	    </select>
	</mapper>

**6. 在conf.xml文件中注册userMapper.xml文件**

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC" />
	            <!-- 配置数据库连接信息 -->
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver" />
	                <property name="url" value="jdbc:mysql://localhost:3306/mybatis" />
	                <property name="username" value="root" />
	                <property name="password" value="XDP" />
	            </dataSource>
	        </environment>
	    </environments>
	    
	    <mappers>
	        <!-- 注册userMapper.xml文件， 
	        userMapper.xml位于me.gacl.mapping这个包下，所以resource写成me/gacl/mapping/userMapper.xml-->
	        <mapper resource="me/gacl/mapping/userMapper.xml"/>
	    </mappers>
	    
	</configuration>

**7. 测试**

	package me.gacl.test;
	
	import java.io.IOException;
	import java.io.InputStream;
	import java.io.Reader;
	import me.gacl.domain.User;
	import org.apache.ibatis.io.Resources;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	
	public class Test1 {
	
	    public static void main(String[] args) throws IOException {
	        //mybatis的配置文件
	        String resource = "conf.xml";
	        //使用类加载器加载mybatis的配置文件（它也加载关联的映射文件）
	        InputStream is = Test1.class.getClassLoader().getResourceAsStream(resource);
	        //构建sqlSession的工厂
	        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(is);
	        //使用MyBatis提供的Resources类加载mybatis的配置文件（它也加载关联的映射文件）
	        //Reader reader = Resources.getResourceAsReader(resource); 
	        //构建sqlSession的工厂
	        //SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(reader);
	        //创建能执行映射文件中sql的sqlSession
	        SqlSession session = sessionFactory.openSession();
	        /**
	         * 映射sql的标识字符串，
	         * me.gacl.mapping.userMapper是userMapper.xml文件中mapper标签的namespace属性的值，
	         * getUser是select标签的id属性值，通过select标签的id属性值就可以找到要执行的SQL
	         */
	        String statement = "me.gacl.mapping.userMapper.getUser";//映射sql的标识字符串
	        //执行查询返回一个唯一user对象的sql
	        User user = session.selectOne(statement, 1);
	        System.out.println(user);
	    }
	}

**8. 基于注解对表执行CRUD操作**

	package me.gacl.mapping;
	
	import java.util.List;
	import me.gacl.domain.User;
	import org.apache.ibatis.annotations.Delete;
	import org.apache.ibatis.annotations.Insert;
	import org.apache.ibatis.annotations.Select;
	import org.apache.ibatis.annotations.Update;
	
	/**
	 * @author gacl
	 * 定义sql映射的接口，使用注解指明方法要执行的SQL
	 */
	public interface UserMapperI {
	
	    //使用@Insert注解指明add方法要执行的SQL
	    @Insert("insert into users(name, age) values(#{name}, #{age})")
	    public int add(User user);
	    
	    //使用@Delete注解指明deleteById方法要执行的SQL
	    @Delete("delete from users where id=#{id}")
	    public int deleteById(int id);
	    
	    //使用@Update注解指明update方法要执行的SQL
	    @Update("update users set name=#{name},age=#{age} where id=#{id}")
	    public int update(User user);
	    
	    //使用@Select注解指明getById方法要执行的SQL
	    @Select("select * from users where id=#{id}")
	    public User getById(int id);
	    
	    //使用@Select注解指明getAll方法要执行的SQL
	    @Select("select * from users")
	    public List<User> getAll();
	}



核心组件
---
###1. 构造器（SqlSessionFactoryBuilder） ###

采用构造器模式，根据配置文件或代码，来生成 SqlSessionFactory

生命周期：存在于创建 SqlSessionFactory 的方法中

###2. 工厂接口（SqlSessionFactory） ###

DefaultSqlSession（在单线程中） 和 SqlSessionManager（在多线程中） 是 SqlSessionFactory 的两个实现类，来生成 SqlSession

生命周期：相当于数据库连接池，等同于 Mybatis 的应用周期，以单例存在

###3. 会话（SqlSession） ###

类似于 JDBC 中的 Connection 对象，代表一个连接资源的开启，作用有三个：

* 获取 Mapper 接口

* 发送 SQL 给数据库

* 控制数据库事务

生命周期：相当于一个数据库连接，存活在一个业务请求中，请求结束后需要关闭

###4. 映射器 ###

mybatis中最重要，最复杂的组件，由一个接口和对应的XML文件（或注解）组成，主要作用就是将 SQL 查询到的结果映射为一个 POJO ，或将 POJO 的数据插入到数据库中。可以配置以下的内容：

* 描述映射规则

* 提供 SQL 语句，配置 SQL 参数类型、返回类型、缓存刷新等信息

* 配置缓存

* 提供动态 SQL

生命周期：代表一个请求中的一个业务处理步骤，周期小于等于 SqlSession 的周期


文件目录
---
* 映射器接口

* 映射器 XML 文件

* POJO 对象

* MyBatis 配置文件

* 日志配置文件

* SqlSessionFactoryUtils 工具类，用于创建 SqlSessionFactory 和获取 SqlSession 对象 


MyBatis配置
---

配置的顺序不能颠倒

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>		配置
		<properties/>		属性	
		<settings/>			设置
		<typeAliases/>		类型命名
		<typeHandlers/>		类型处理器
		<objectFactory/>	对象工厂
		<plugins/>			插件
		<environments> 		配置环境
			<environment>	环境变量
				<transactionManager/>	事务管理器
				<dataSource/>			数据源
			</environment>
		</environments>
		<databaseIdProvider/>		数据库厂商标示
	  	<mappers/>			映射器
	</configuration>

###1. properties： ###

给系统配置运行参数，放在XML文件，或者 properties 文件中，优点在于参数便于修改，不用重新编译代码。使用 properties 的三种方式：

* property 子元素

* properties 文件

		<properties resource="jdbc.properties"/>

* 程序代码传递（需加密的时候用）

###2. typeAliases： ###

由于类的全限定名很长，大量使用时，可以定义别名。可以通过 Configuration 获取 TypeAliasRegistry 类对象，使用 TypeAliasRegistry 的 registerAlias 方法注册别名；也可以采用配置文件或者扫描方式。分为系统定义别名和自定义别名：

* **系统定义别名：**

int 定义为 _int ， long 定义为 _long

Integer 定义为 int ， Long 定义为 long

_int 数组定义为 _int[] 

* **自定义别名：**

配置文件定义：

	<typeAliases>
		<typeAlias alias="role" type="com.learn.ssm.pojo.Role"/>
		<typeAlias alias="user" type="com.learn.ssm.pojo.User"/>
	</typeAliases>

扫描别名方式：扫描包里边的类，将其第一个字母变为小写作为其别名

	<typeAliases>
		<package name="com.learn.ssm.pojo"/>
	</typeAliases>

###3. typeHandler： ###

jdbcType 和 javaType 之间的相互转换，分为系统定义和自定义：

* **系统定义：**

在 MyBatis 中 typeHandler 都要实现 org.apache.ibatis.type.TypeHandler 这个接口，或者继承 BaseTypeHandler 抽象类

* **自定义：**

自定义一个 MyTypeHandler 实现接口 TypeHandler。

配置 MyTypeHandler:

	<typeHandlers>
		<typeHandler jdbcType="VARCHAR" javaType="string" handler="com.learn.ssm.typehandler.MyTypeHandler"/>
	</typeHandlers>		

###4. environments： ###

主要作用是配置数据库的信息

* **transactionManager：**

		<transactionManager type="JDBC"/>
		<transactionManager type="MANAGED"/>
 Transaction 接口，主要工作就是提交、回滚和关闭数据库事务。有两个实现类，JdbcTransaction 和 ManagedTransaction，分别对应上面的两种配置方式。
 * JDBC：使用 JdbcTransactionFactory 生成的 JdbcTransaction 对象实现。以 JDBC 的方式对数据库的提交和回滚进行操作。
 
 * MANAGED：使用 ManagedTransactionFactory 生成的 ManagedTransaction 对象实现。其提交和回滚方法不用任何操作，而是把事务交给容器处理，默认情况下它会关闭连接。


* **dataSource：**

		<dataSource type="UNPOOLED">
		<dataSource type="POOLED">
		<dataSource type="JNDI">
 通过 pooledDataSourceFactory、UnpooledDataSourceFactory 和 JndiDataSourceFactory 三个工厂类来提供，生成一个实现了 DataSource 接口的数据库连接对象。
 * UNPOOLED：采用非数据库池的管理方式，每次请求都会打开一个数据库连接

 * POOLED：采用数据库池的方式管理 Connection 对象
 
 * JNDI：在容器中使用，容器可以集中或者在外部配置数据源，然后放置一个 JNDI 上下文的引用

###5. mappers： ###

根据实际情况引入映射器：

	<mappers>
		<mapper resource="文件路径">
	</mappers>
	
	<mappers>
		<mapper class="类路径">
	</mappers>
	
	<mappers>
		<package name="包名">
	</mappers>


映射器
--- 
由一个接口加上 XML 文件组成。在映射器中可以配置参数、各类的 sql 语句、存储过程、缓存和级联等复杂内容。通过简易的映射规则映射到指定的 POJO 或者其他对象上，映射器能有效消除 JDBC 底层的代码。

映射器的配置元素：

![](https://i.imgur.com/9SR8PXv.png)

### 1. select元素 ###

	#{传递进去的参数}

* 自动映射和驼峰映射

建立在 SQL 列名和 POJO 属性名的映射关系上。

setting 元素中的可以配置的选项 autoMappingBehavior 和 mapUnderscoreToCamelCase

* 传递多个参数

 * 使用 map 接口传递
 
 * 使用注解 @Param 传递 
 
 * 通过 Java bean 传递

* resultMap 映射结果集

		<resultMap id="roleMap" type="role">
			<id property="id" column="id"/>
			<result property="roleName" column="role_name"/>
			<result property="note" column="note"/>
		</resultMap>
property 代表 POJO 的属性名称，column 代表 SQL 的列名。把 POJO 的属性和 SQL 的 列名做对应。

* 分页参数 RowBounds

### 2. insert元素 ###

MyBatis 在执行完一条 insert 语句后，会返回一个整数表示其影响的记录数

* 主键回填

		useGeneratedKeys="true"  keyProperty="id"
useGeneratedKeys 表示返回主键，keyProperty 表示用哪个 POJO 的属性匹配这个主键
* 自定义主键

		<selectKey keyProperty="id" resultType="long" order="BEFORE">
			select if (max(id)=null, 1, max(id)+3) from t_role
		</selectKey>
order 指将于当前定义的 SQL 前执行

### 3. update元素和delete元素 ###

规则如上

### 4. sql元素 ###

反复出现的 sql 语句，可以通过 sql 元素来定义，方便后面的引用

	<sql id="roleCols">
		id, role_name, note
	</sql>

下面的引用：

<include refid="roleCols"/>

### 5. resultMap元素 ###

定义结果集的映射关系，也就是 SQL 到 JavaBean 的映射关系定义

	<resultMap>
		<constructor>
			<idArg/>
			<arg/>
		</constructor>
		<id/>
		<result/>
		<association/>
		<collection/>
		<discriminator>
			<case/>
		</discriminator>
	</resultMap>

* **constructor:** 用来配置有参的构造方法，这样 MyBatis 就会使用对应的构造方法来构造 POJO。

* **id：** 表示哪个列是主键

* **result：** 配置 POJO 到 SQL 列名的映射关系

result 和 id 的属性如下：

![](https://i.imgur.com/CC89sMq.png)

* **association：** 一对一级联

* **collection：** 一对多级联

* **discriminator：** 鉴别器

case用于进行区分

###6. 存储结果集 ###

可以使用 resultType 和 resultMap 中的一种。

* 使用 map 存储

		resultType="map"
可读性下降

* 使用 POJO 存储

先配置好 resultMap 

	resultMap="配置好的id"

### 7. 级联 ###

* **鉴别器：** 根据某些条件，决定采用具体实现类级联的方案

* **一对一：** 如学生和学生证

* **一对多：** 如老师和学生

* **多对多：** 一般拆分成两个一对多

**N+1 问题：**假设有N 个关联关系完成了级联，再加入一个关联关系，就变成了 N+1 个级联，所有的级联 SQL 都会被执行，有很多不是我们关心的数据被取出，这样会造成很大的资源浪费。解决办法是延迟加载。

**延迟加载：**一次性把常用的级联数据通过 SQL 直接查询出来，对于那些不常用的级联数据不要取出，而是等待要用时再取出，这些不常用的数据可以采用延迟加载。在 Settings配置中存在两个元素可以配置级联：

![](https://i.imgur.com/IdGhrzO.png)

全局性配置：

	<settings>
		<setting name="lazyLiadingEnabled" value="true">   //是否开启加载
		<setting name="aggressiveLazyLoading" value="true">  //是否开启层级加载
	</settings>

自定义性配置：

association 和collection 的fetchType 属性：**eager**（获得当前POJO立即加载对应数据）；**lazy**（获得当前POJO后延迟加载对应数据）。

### 8. 缓存 ###

缓存一般放在可高速读写的存储器上，一般只会把那些常用且命中率高的数据缓存起来。

**一级缓存和二级缓存：**

* 一级缓存是在 SqlSession 的缓存，没有任何配置的情况下，MyBatis 会开启一级缓存。一级缓存不需要 POJO 对象实现序列化。对于不同的 SqlSession 对象是不能共享的

* 二级缓存是在 SqlSessionFactory 的缓存。需要 POJO 对象实现序列化，在测试代码中需要把 sqlSession commit 到一级缓存，在映射文件 XML 中加入如下代码

		<cache/>

**配置缓存项：**

![](https://i.imgur.com/MyLKux1.png)

假设存在一个 redis 的缓存实现类 com.ssm.cache.RedisCache ，配置如下：

	<cache type="com.ssm.cache.RedisCache">
		<property name="host" value="localhost"/>
	</cache>

其他的映射器使用同样的配置：

	<cache-ref namespace="com.ssm.mapper.RoleMapper"/>


### 9. 存储过程 ###

是数据库预先编译好，放在数据库内存中的一个程序片段，可以完成特定的功能。类似于 Java 语言中的方法，可以接收参数，输出参数，返回单个或多个结果集。有性能高、可重复使用的特性。定义了三种类型的参数：

* 输入参数：外界给的存储过程参数

* 输出参数：存储过程经过计算返回给程序的结果参数

* 输入输出参数：一开始作为参数传递给存储过程，存储过程修改后返回的参数

		<select id="" parameterType="" statementType="CALLABLE" >
		{call count_role(
		#{roleName,mode=IN,jdbcType=VARCHAR},
		#{total,mode=OUT,jdbcType=INTEGER},
		)}
		</select>

**游标**


**存储过程参考博客：** [https://www.cnblogs.com/chenpi/p/5136483.html](https://www.cnblogs.com/chenpi/p/5136483.html)


动态 SQL
---
MyBatis 提供对 SQL 语句动态的组装能力，大量的判断可以在 MyBatis 的映射XML 里面配置，以实现如需要条件查询等功能。

动态 SQL 元素如下：

![](https://i.imgur.com/596ZZF9.png) 






















MyBatis 底层原理
---



插件
---