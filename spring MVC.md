spring MVC
---
###1. spring MVC 组件和流程图： ###

![](https://i.imgur.com/eX2w5mP.png)

1. 拦截 http 请求

2. 根据配置，得到 URI 和处理器之间的映射关系

3. 给处理器加入拦截器，在处理器执行前后加入自己的代码，构成处理器执行链

4. 返回模型和视图

5. 把视图信息传递给视图解析器，如果是逻辑视图，视图解析器就会解析它，把模型渲染到视图中，最后响应用户的请求

6. 根据视图解析器得到对应路径，作为视图

###2. 入门实例: ###

**配置 web.xml 文件：**

* 配置 Spring IoC 配置文件路径（ContextLoaderListener 上下文）

* 配置监听器 ContextLoaderListener 用于初始化 IoC 容器

* 配置 DispatcherServlet（默认 DispatcherServlet 上下文）

* 配置 DispatcherServlet 拦截

### 3. spring MVC 初始化： ###

**初始化spring IoC 上下文：**

实现 ServletContextListener 接口的方法，要加载应用中的其他 bean，通常是驱动应用后端的中间件和数据层组件。

**初始化映射请求上下文：**

加载包含 Web 组件的 bean，如控制器、视图解析器以及处理器映射等。

### 4. spring MVC 开发流程： ###

**配置 @RequestMapping**

**控制器开发：**

* 获取请求参数：@RequestParam("")、@SessionAttribute("")

* 处理业务逻辑

* 绑定模型和视图


### 5. 高级组件开发： ###

**控制器接收各类请求参数：**

**拦截器：在进入控制器之前或者之后执行的方法，包括了三个方法：**

* preHandle：在控制器前的执行方法

* postHandle：在控制器之后的执行方法

* afterCompletion:在渲染视图后执行的方法

**上传文件：**