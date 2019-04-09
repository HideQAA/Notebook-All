Spring
---



Spring Ioc
---

IOC 容器的本质目的就是为了管理 Bean，容纳我们开发的各种 bean，并且通过描述可以得到它。

控制反转是以一种通过描述（可以是 XML 或者注解）并通过第三方去产生或获取特定对象的方式，实现控制反转的是 IOC 容器，实现的方式是依赖注入。主动创建的模式，责任归于开发者；被动的模式下，责任归于 IOC 容器。

###1. Spring 容器设计： ###

主要基于 BeanFactory 和 ApplicationContext 两个接口，其中 ApplicationContext 是 BeanFactory 的子接口之一。BeanFactory 是 Spring Ioc 的最底层接口，而 ApplicationContext 是其高级接口之一，对 BeanFactory 的功能做了扩展，一般会使用 ApplicationContext 作为 Spring Ioc 容器。

**BeanFactory：**

* getBean 用于获取配置给 Spring IoC 容器的 bean。

* isSingleton 用于判断是否单例，如果为真，则该 bean 在容器中是作为一个唯一单例存在的。（默认）

* isPrototype 相反，如果为真，每获取一个 bean ，容器会生成一个新的实例。

* getAliases 获取别名的方法。

###2. Spring IoC 容器的初始化和DI： ###

**bean 的初始化：**

* 资源定位

* BeanDefinition 的载入

* BeanDefinition 的注册

使用 ApplicationContext 的实现类初始化 Spring IoC 容器，开发者可以通过 IoC 容器来获取资源。

###3. Spring Bean 的生命周期 ###



装配 Spring Bean
---
将 Bean 注入到 Spring IoC 容器中

###1. 依赖注入的三种方式： ###

* 构造器注入（参数多不方便）

* setter 注入（广泛使用）

* 接口注入

###2. 装配 bean： ###

* 在 XML 中显示配置（第三方类库时使用）

 * 装配简易值
 
 * 装配集合
 
 * 命名空间装配（先引入对应的 XSD 文件）：c、p、util

* 在 Java 的接口和类中配置（次优先使用，避免 XML 配置的泛滥）

* 隐式 bean 的发现机制和自动装配（优先使用）

 * 使用 @Component 装配 Bean （只能注解到类上）
 
 * 使用 @Bean 装配 Bean （能注解到方法上，将方法返回的对象作为 bean，可用于引入第三方包）
 
 * 使用 @Autowired 自动装配（可以配置在属性和方法，及构造方法的参数）
 
 * 自动装配的歧义性（@Primary 和 @Qualifier）：接口或者抽象类的实现类有多个，无法判断注入哪个实现类，解决办法一是添加优先级，二是按名称注入

* 装配混合使用： @ImportResource

### 3. 使用 Profile： ###

环境迁移，只有当规定的 profile 激活时，相应的 bean 才会被创建，没有指定 profile 的 bean 始终都会被创建。可以在 XML 和 Java配置。

**激活 profile：**（spring.profiles.active 确定激活项，spring.profiles.default 使用默认值）

* 测试代码中激活（@ActiveProfiles）

* 服务器上运行（配置 JVM 的启动项）

* Spring MVC（web.xml 中配置）


### 4. 加载属性文件： ###

开发中配置文件，往往就是属性文件，比如使用 properties 文件配置数据库文件。可以使用注解或者 XML 的方式加载属性文件：

* 注解：@PropertySource

* XML ：<context:property-placeholder>

### 5. 条件化装配 Bean： ###

在某个条件满足的情况下，创建 bean 

注解：@Conditional（实现了 Condition 接口的类），实现 Condition 接口中的 matches 方法，matches 方法中有两个参数，一个是 **ConditionContext** 返回 bean、环境变量、类加载器等 spring 的运行环境，另一个是 **AnnotatedTypeMetadata** 返回带有 @bean 注解方法上的其他注解。

### 6. Bean 的作用域： ###

默认下 spring 上下文中的所有 bean  都是以单例的形式创建的，但是对于易变的类型，这并不合适。所以定义了多种作用域（@scope）：

* 单例：整个应用只有一个 bean 实例

* 原型：每次注入或者通过上下文获取时，都会创建一个新的 bean 实例

* 会话：Web 应用中，为每个会话创建一个 bean 实例

* 请求：Web 应用中，为每个请求创建一个 bean 实例

### 7. Spring 表达式语言：#{bean id.属性或方法}、T(类).静态方法或常量} ###

* 调用 bean 的 ID 来引用 bean

* 调用指定对象的方法和访问对象的属性

* 进行运算

* 提供正则表达式进行匹配

* 集合配置


面向切面编程
---
AOP 通过动态代理模式，管控各个对象操作的切面环境，包括日志、数据库事务等操作，让我们可以在反射原有对象方法之前正常返回、异常返回后插入自己的逻辑代码的能力，有时候甚至取代原有的方法。

### 1. 术语： ###

* 切面：拦截器

* 通知：切面的方法

* 引入：向现有的类添加新方法和属性

* 切点：被切面拦截的方法

* 连接点：判断条件来指定哪些是切点

* 织入：生成代理对象的过程

spring 只能支持方法拦截的 AOP，以下两种方式实现 AOP 的拦截功能：

* XML 配置 AOP

* 使用 @AspectJ 注解驱动切面

* 使用 AspectJ 注入切面


### 2. 使用 @AspectJ 注解驱动切面 ###

* **选择切点**

* **创建切面：@Aspect、通知方法**

* **连接点：execution、@Pointcut**

* **配置 spring bean：启动自动代理、声明 bean**

* **环绕通知：@Around注解，有 ProceedingJoinPoint 参数**

* **给通知传递参数：&& args(参数1，参数2)**

*　**引入：DeclareParents**

### 3. 使用 XML 配置开发 Spring AOP ###

XML 配置 spring AOP 元素：

![](https://i.imgur.com/Vw8Ig7g.png)

### 4. 多个切面 ###

@Order(n)


Spring 和数据库编程
---
模板（不变的如事务管理、管理资源和处理异常）+回调（可变的如语句、绑定参数、结果集）

### 配置数据源 ###

### JDBC模板 ###

### MyBatis-Spring 项目 ###

* **配置数据源**

* **配置SqlSessionFactory**

* **配置SqlSessionTemplate（非必须）**

* **配置Mapper**

* **事务管理**


深入数据库事务管理
---

事务的创建、提交和回滚是通过 PlatformTransactionManager 接口完成的

**配置事务管理器：**

MyBatis 中使用最多的是 DataSourceTransactionManager

	<bean id="transactionManager" class="">
		<property name="" ref=""/>
	</bean>

**Java配置实现 Spring 数据库事务：**

配置类中实现接口 TransactionManagementConfigurer 的 annotationDrivenTransactionManager 方法，把返回的事务管理器作为程序中的事务管理器


**传播行为：**

方法之间调用事务策略的问题。

@Transactional 的**自调用**失效方法：调用自身类的方法
