Java Web入门
---
1. Tomcat

* Http协议

* Servlet

* Response

* Request

* Cookie

* Session

* JSP

* JavaBean

* JDBC

* Filter

* Listener

**入门：**

* 静态web资源（如html 页面）：指web页面中供人们浏览的数据始终是不变

![](https://i.imgur.com/BUfHQXH.png)

**JS是用来实现静态WEB客户端动态效果的手段**

* 动态web资源（JSP/Servlet、ASP、PHP等）：指web页面中供人们浏览的数据是由程序产生的，不同时间点访问web页面看到的内容各不相同

![](https://i.imgur.com/0ftUvcm.png)

所有请求都先经过一个WEB Server Plugin（**服务器插件**）来处理，此插件用于区分是请求的是静态资源还是动态资源。 如果发现客户端请求的是动态资源，则先将请求转交给WEB Container(**WEB容器**)，在WEB Container中连接数据库，从数据库中取出数据等一系列操作后动态拼凑页面的展示内容，拼凑页面的展示内容后，把所有的展示内容交给WEB服务器，之后通过WEB服务器将内容发送回客户端浏览器进行解析执行。

一个 **web应用** 由多个静态web资源和动态web资源组成，如:html、css、js文件，Jsp文件、java程序、支持jar包、配置文件等等。Web应用开发好后，若想供外界访问，需要把web应用所在目录交给 **web服务器管理**，这个过程称之为虚似目录的映射。

---

Tomcat
---
**Tomcat体系结构：**

![](https://i.imgur.com/hda5tI1.png)

**server.xml文件：**

	<?xml version='1.0' encoding='utf-8'?>
	<Server port="8005" shutdown="SHUTDOWN">
	  <Service name="Catalina">
	    <Connector port="8080" protocol="HTTP/1.1"
	               connectionTimeout="20000"
	               redirectPort="8443" />
	    <Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol"
	               maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
	               clientAuth="false" sslProtocol="TLS" 
	               keystoreFile="conf/.keystore" keystorePass="123456"/>
	    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
	    <Engine name="Catalina" defaultHost="localhost">
	
	      <Host name="localhost"  appBase="webapps"
	            unpackWARs="true" autoDeploy="true">
	        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
	               prefix="localhost_access_log." suffix=".txt"
	               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
	      </Host>
	      <Host name="www.gacl.cn" appBase="F:\JavaWebApps">
	        <Context path="" docBase="F:\JavaWebApps\JavaWebDemo1"/>
	      </Host>
	
	    </Engine>
	  </Service>
	</Server>


Tomcat启动的时候首先会启动一个Server，Server里面就会启动Service，Service里面就会启动多个"Connector(连接器)"，每一个连接器都在等待客户机的连接，当有用户使用浏览器去访问服务器上面的web资源时，首先是连接到Connector(连接器)，Connector(连接器)是不处理用户的请求的，而是将用户的请求交给一个Engine(引擎)去处理，Engine(引擎)接收到请求后就会解析用户想要访问的Host，然后将请求交给相应的Host，Host收到请求后就会解析出用户想要访问这个Host下面的哪一个Web应用，一个web应用对应一个Context。

---

Http协议
---
**请求：**

![](https://i.imgur.com/LRv06mj.png)

如果请求方式为GET方式，则可以在请求的URL地址后以 **?** 的形式带上交给服务器的数据，多个数据之间以&进行分隔。其数据容量通常不能超过1K。

**HTTP请求中的常用消息头：**

* accept:浏览器通过这个头告诉服务器，它所支持的数据类型
* Accept-Charset: 浏览器通过这个头告诉服务器，它支持哪种字符集
* Accept-Encoding：浏览器通过这个头告诉服务器，支持的压缩格式
* Accept-Language：浏览器通过这个头告诉服务器，它的语言环境
* Host：浏览器通过这个头告诉服务器，想访问哪台主机
* If-Modified-Since: 浏览器通过这个头告诉服务器，缓存数据的时间
* Referer：浏览器通过这个头告诉服务器，客户机是哪个页面来的，防盗链
* Connection：浏览器通过这个头告诉服务器，请求完后是断开链接还是持有链接



**响应：**

![](https://i.imgur.com/6AwwZiv.png)

**HTTP响应中的常用响应头：**

* Location: 服务器通过这个头，来告诉浏览器跳到哪里
* Server：服务器通过这个头，告诉浏览器服务器的型号
* Content-Encoding：服务器通过这个头，告诉浏览器，数据的压缩格式
* Content-Length: 服务器通过这个头，告诉浏览器回送数据的长度
* Content-Language: 服务器通过这个头，告诉浏览器语言环境
* Content-Type：服务器通过这个头，告诉浏览器回送数据的类型
* Refresh：服务器通过这个头，告诉浏览器定时刷新
* Content-Disposition: 服务器通过这个头，告诉浏览器以下载方式打开数据
* Transfer-Encoding：服务器通过这个头，告诉浏览器数据是以分块方式回送的
* Expires: -1  控制浏览器不要缓存

**服务端设置响应头来控制客户端浏览器的行为：**

* 设置Location响应头，实现请求重定向：

		response.setStatus(302);
		response.setHeader("Location", "/JavaWeb_HttpProtocol_Study_20140528/1.jsp");

* 设置Content-Encoding响应头，告诉浏览器数据的压缩格式：

		response.setHeader("Content-Encoding", "gzip");
		response.setHeader("Content-Length",g.length +"");

* 设置content-type响应头，指定回送数据类型

		response.setHeader("content-type", "image/jpeg");

* 设置refresh响应头，让浏览器定时刷新

		response.setHeader("refresh", "3");
		response.setHeader("refresh", "3;url='http://www.baidu.com'");

* 设置content-disposition响应头，让浏览器下载文件

		response.setHeader("content-disposition", "attachment;filename=xxx.jpg");

---

servlet
---
**实现了servlet接口的java程序，并部署到web服务器中**

####Servlet调用示意图：####

![](https://i.imgur.com/viUrXMV.png)

**service()方法** 两个参数分别是 **Request对象** 和 **Response对象**，方法体中读取请求信息，写入响应信息。

####Web project目录结构：####

![](https://i.imgur.com/2SAtoMQ.png)

#### 在客户端缓存servlet输出： ####

	response.setDateHeader("expires",System.currentTimeMillis() + 24 * 3600 * 1000);
	response.getOutputStream().write(data.getBytes());

####ServletConfig对象（只适用于当前的servlet对象）：####

在Servlet的配置文件web.xml中，可以使用一个或多个<init-param>标签为servlet配置一些初始化参数。

     <init-param>
         <param-name>name</param-name>
        <param-value>gacl</param-value>
     </init-param>

当servlet配置了初始化参数后，web容器在创建servlet实例对象时，会自动将这些初始化参数封装到ServletConfig对象中，并在调用servlet的init方法时，将ServletConfig对象传递给servlet。进而，**我们通过ServletConfig对象就可以得到当前servlet的初始化参数信息。**

	public void init(ServletConfig config) throws ServletException {
	         this.config = config;
	    }

	String paramVal = this.config.getInitParameter("name");//获取指定的初始化参数
    response.getWriter().print(paramVal);

#### ServletContext对象：####

WEB容器在启动时，它会为每个WEB应用程序都创建一个对应的ServletContext对象，它代表当前web应用。

开发人员在编写servlet时，可以通过**ServletConfig.getServletContext方法**获得ServletContext对象。

**一个WEB应用中的所有Servlet共享同一个ServletContext对象，因此Servlet对象之间可以通过ServletContext对象来实现通讯。**

#### ServletContext的应用： ####

**1. 多个Servlet通过ServletContext对象实现数据共享**

demo1:

	String data = "xdp_gacl";
	ServletContext context = this.getServletConfig().getServletContext();
	context.setAttribute("data", data);   //在demo1中将数据data存储到ServletContext对象中

demo2:

	ServletContext context = this.getServletContext();
    String data = (String) context.getAttribute("data");//demo2中从ServletContext对象中取出数据
    response.getWriter().print("data="+data);


**2. 获取WEB应用的初始化参数（对所有的servlet都适用）**

在web.xml文件中使用<context-param>标签配置WEB应用的初始化参数

	 <context-param>
        <param-name>url</param-name>
         <param-value>jdbc:mysql://localhost:3306/test</param-value>
     </context-param>

获取Web应用的初始化参数

		 ServletContext context = this.getServletContext();
         //获取整个web站点的初始化参数
         String contextInitParam = context.getInitParameter("url");
         response.getWriter().print(contextInitParam);

**3. 用servletContext实现请求转发**

	ServletContext context = this.getServletContext();//获取ServletContext对象
    RequestDispatcher rd = context.getRequestDispatcher("/servlet/ServletContextDemo5");//获取请求转发对象(RequestDispatcher)
    rd.forward(request, response);//调用forward方法实现请求转发

**4. 利用ServletContext对象读取资源文件**

获取绝对路径：

	String path = this.getServletContext().getRealPath("/WEB-INF/classes/db/config/db3.properties");
    InputStream in = new FileInputStream(path);

获取相对路径：

	InputStream in = this.getServletContext().getResourceAsStream("/WEB-INF/classes/gacl/servlet/study/db4.properties");

**5. 利用类加载器读取资源文件**

不适合装载大文件，否则会导致jvm内存溢出

	//获取到装载当前类的类加载器
	ClassLoader loader = ServletContextDemo.class.getClassLoader();
	//用类加载器读取src目录下的gacl.servlet.study包中的db4.properties配置文件
	InputStream in = loader.getResourceAsStream("gacl/servlet/study/db4.properties");


#### 开发注意细节： ####

**1. Servlet访问URL映射配置**

	   <servlet>
	    <servlet-name>ServletDemo1</servlet-name>
	    <servlet-class>gacl.servlet.study.ServletDemo1</servlet-class>
	   </servlet>
	 
	   <servlet-mapping>
	     <servlet-name>ServletDemo1</servlet-name>
	     <url-pattern>/servlet/ServletDemo1</url-pattern>
	   </servlet-mapping>

**2. url-pattern的配置**

  *是通配符

**3. Servlet的生命周期**

针对客户端的多次Servlet请求，通常情况下，服务器只会创建一个Servlet实例对象。

在Servlet的整个生命周期内，Servlet的init方法只被调用一次。而对一个Servlet的每次访问请求都导致Servlet引擎调用一次servlet的service方法。对于每次访问请求，Servlet引擎都会创建一个新的HttpServletRequest请求对象和一个新的HttpServletResponse响应对象，然后将这两个对象作为参数传递给它调用的Servlet的service()方法，service方法再根据请求方式分别调用doXXX方法。

    <servlet>
        <servlet-name>invoker</servlet-name>
        <servlet-class>
            org.apache.catalina.servlets.InvokerServlet
        </servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

在<servlet>元素中配置了一个<load-on-startup>元素，WEB应用程序在启动时，就会装载并创建Servlet的实例对象、以及调用Servlet实例对象的init()方法。

**4. 缺省Servlet**

	   <servlet>
	    <servlet-name>ServletDemo2</servlet-name>
	     <servlet-class>gacl.servlet.study.ServletDemo2</servlet-class>
	     <load-on-startup>1</load-on-startup>
	   </servlet>
	   
	   <!-- 将ServletDemo2配置成缺省Servlet -->
	   <servlet-mapping>
	     <servlet-name>ServletDemo2</servlet-name>
	     <url-pattern>/</url-pattern>
	   </servlet-mapping>

将某个Servlet的映射路径设为一个正斜杠（/），这个Servlet就成为当前Web应用程序的缺省Servlet，可以用于处理所有其他Servlet都不处理的访问请求。

**5. Servlet的线程安全问题**

多个客户端并发访问同一个Servlet时，web服务器会为每一个客户端的访问请求创建一个线程，并在这个线程上调用Servlet的service方法，因此service方法内如果访问了同一个资源的话，就有可能引发线程安全问题。

解决办法：

<font color='red'>错误的：</font> 在 doGet方法体内加上 synchronized (this)，线程每次访问先获取servlet对象锁，**但是在servlet中千万不能使用这种方法，因为并发量太大。**

<font color='red'>过时的：</font> 让Servlet去实现一个SingleThreadModel接口，其采用的方式是产生多个Servlet实例对象，并发的每个线程分别调用一个独立的Servlet实例对象。

---

response
---
**Web服务器收到客户端的http请求，会针对每一次请求，分别创建一个用于代表请求的request对象、和代表响应的response对象。我们要获取客户机提交过来的数据，只需要找request对象，要向客户机输出数据，只需要找response对象。**

**HttpServletResponse对象代表服务器的响应，其中封装了向客户端发送数据、发送响应头，发送响应状态码的方法。**

**发送数据：（字节流和字符流）**

![](https://i.imgur.com/ei4QEut.png)

**发送响应头：**

![](https://i.imgur.com/OZVcBUQ.png)

![](https://i.imgur.com/GBYXjQ7.png)

**发送状态码：**

![](https://i.imgur.com/vWEH990.png)

#### HttpServletResponse对象常见应用： ####

**1. 使用OutputStream流向客户端浏览器输出中文数据**

在服务器端，数据是以哪个码表输出的，那么就要控制客户端浏览器以相应的码表打开，可以通过设置响应头控制浏览器的行为，例如：response.setHeader("content-type", "text/html;charset=UTF-8")

	String data = "中国";
	OutputStream outputStream = response.getOutputStream();
	response.setHeader("content-type", "text/html;charset=UTF-8");
	byte[] dataByteArr = data.getBytes("UTF-8"); //data.getBytes()将字符转换成字节数组，以UTF-8编码进行转换
	outputStream.write(dataByteArr);

**2. 使用PrintWriter流向客户端浏览器输出中文数据**

	 response.setCharacterEncoding("UTF-8");//设置将字符以"UTF-8"编码输出到客户端浏览器
	 /**
	 * PrintWriter out = response.getWriter();这句代码必须放在response.setCharacterEncoding("UTF-8");之后
	 * 否则response.setCharacterEncoding("UTF-8")这行代码的设置将无效，浏览器显示的时候还是乱码
	 */
	 PrintWriter out = response.getWriter();//获取PrintWriter输出流

然后再使用response.setHeader("content-type", "text/html;charset=字符编码")；设置响应头，控制浏览器以指定的字符编码编码进行**显示**。

	response.getWriter().write("<meta http-equiv='content-type' content='text/html;charset=UTF-8'/>");
	等同于response.setHeader("content-type", "text/html;charset=UTF-8");

**当需要向浏览器输出字符数据时，使用PrintWriter比较方便，省去了将字符转换成字节数组那一步**

**3. 使用OutputStream或者PrintWriter向客户端浏览器输出数字**

	response.setHeader("content-type", "text/html;charset=UTF-8");
    OutputStream outputStream = response.getOutputStream();
    outputStream.write("使用OutputStream流输出数字1：".getBytes("UTF-8"));
    //outputStream.write(1);
    outputStream.write((1+"").getBytes()); //正常输出1

<font color='red'>在开发过程中，如果希望服务器输出什么浏览器就能看到什么，那么在服务器端都要以字符串的形式进行输出。</font>

    response.setHeader("content-type", "text/html;charset=UTF-8");
    response.setCharacterEncoding("UTF-8");
    PrintWriter out = response.getWriter();//获取PrintWriter输出流
    out.write("使用PrintWriter流输出数字1：");
    out.write(1+"");

**4. 文件下载**



　　1.获取要下载的文件的绝对路径

　　2.获取要下载的文件名

　　3.设置content-disposition响应头控制浏览器以下载的形式打开文件

　　4.获取要下载的文件输入流

　　5.创建数据缓冲区

　　6.通过response对象获取OutputStream流

　　7.将FileInputStream流写入到buffer缓冲区

　　8.使用OutputStream将缓冲区的数据输出到客户端浏览器

	String realPath = this.getServletContext().getRealPath("/download/1.JPG");
	String fileName = realPath.substring(realPath.lastIndexOf("\\")+1);
	response.setHeader("content-disposition", "attachment;filename="+fileName);
	InputStream in = new FileInputStream(realPath);
	int len = 0;
	byte[] buffer = new byte[1024];
	OutputStream out = response.getOutputStream();
	while ((len = in.read(buffer)) > 0) {
		out.write(buffer,0,len);
	}
	in.close();

<font color='red'>在编写下载文件功能时，要使用OutputStream流，避免使用PrintWriter流，因为OutputStream流是字节流，可以处理任意类型的数据，而PrintWriter流是字符流，只能处理字符数据，如果用字符流处理字节数据，会导致数据丢失。</font>

**5. 生成验证码**

**6. 设置响应头控制浏览器的行为**

禁止缓存当前文档内容：

	 response.setDateHeader("expries", -1);
	 response.setHeader("Cache-Control", "no-cache");
	 response.setHeader("Pragma", "no-cache");

定时刷新网页：

	response.setHeader("refresh", "5");

实现请求重定向：

	response.sendRedirect(String location)；

#### web工程中URL地址的推荐写法： ####

建议最好以"/"开头，也就是使用绝对路径的方式，<font color='red'>如果"/"是给服务器用的，则代表当前的web工程，如果"/"是给浏览器用的，则代表webapps目录。</font>

	this.getServletContext().getRequestDispatcher("/index.jsp").forward(request, response);   
	//请求转发在服务器内部， / 代表当前web工程
	response.sendRedirect(request.getContextPath()+"/index.jsp");
	//重定向在浏览器端， / 代表webapps目录

---

request
---
客户端通过HTTP协议访问服务器时，HTTP请求头中的所有信息都封装在这个对象中，通过这个对象提供的方法，可以获得客户端请求的所有信息。

#### Request常用方法： ####

**1. 获得客户机信息**

         String requestUrl = request.getRequestURL().toString();//得到请求的URL地址
         String requestUri = request.getRequestURI();//得到请求的资源
         String queryString = request.getQueryString();//得到请求的URL地址中附带的参数
         String remoteAddr = request.getRemoteAddr();//得到来访者的IP地址
         String remoteHost = request.getRemoteHost();//得到客户机的完整主机名
         int remotePort = request.getRemotePort();//得到客户机的网络端口号
         String remoteUser = request.getRemoteUser();
         String method = request.getMethod();//得到请求URL地址时使用的方法
         String pathInfo = request.getPathInfo();
         String localAddr = request.getLocalAddr();//获取WEB服务器的IP地址
         String localName = request.getLocalName();//获取WEB服务器的主机名

**2. 获得客户机请求头**

	　　getHeader(string name)方法：String 
	　　getHeaders(String name)方法：Enumeration 
	　　getHeaderNames()方法：获取所有请求头名称

**3. 获得客户机请求参数**

	getParameter(String)方法：String
	getParameterValues(String name)方法：String[]
	getParameterMap()方法：Map<String,String[]>

#### doPost和doGet: ####

使用表单提交数据到服务器的时候有两张方式可共选择，一个是post一个是get，可在<form>中的method属性中指定提交的方式。如： 

	<form action="inputForm"method="get">

区别：

* 通过get方式提交的数据有大小的限制，通常在1024字节左右。也就是说如果提交的数据很大，用get方法就可需要小心；而post方式没有数据大小的限制，理论上传送多少数据都可以。

* 通过get传递数据，实际上是将传递的数据按照'key，value'的方式跟在URL的后面来达到传送的目的的；而post传递数据是通过http请求的附件进行的，在URL中并没有明文显示。

* 通过Get方式提交的数据安全性不高，而Post方式的更加安全。

<font color='red'>可以post方式提交，然后在doGet方式写逻辑代码，不过要在doPost方法中调用doGet方法，同样get方式也是一样的道理。</font>

#### 解决中文乱码问题： ####

**1. post方式提交中文数据乱码**

在客户端和服务器之间设置一个统一的编码，之后就按照此编码进行数据的传输和接收

	request.setCharacterEncoding("UTF-8");
	String userName = request.getParameter("userName");
	System.out.println("userName："+userName);

**2. get方式提交中文数据乱码**

	String name = request.getParameter("name");
	name =new String(name.getBytes("ISO8859-1"), "UTF-8") ;
	//获取request对象以ISO8859-1字符编码接收到的原始数据的字节数组，然后通过字节数组以指定的编码构建字符串，解决乱码问题
	System.out.println("name："+name);


#### Request对象实现请求转发: ####

一个web资源收到客户端请求后，通知服务器去调用另外一个web资源进行处理。

在Servlet中实现请求转发的两种方式：

* 通过ServletContext的getRequestDispatcher(String path)方法，该方法返回一个RequestDispatcher对象，调用这个对象的forward方法可以实现请求转发

		 RequestDispatcher reqDispatcher =this.getServletContext().getRequestDispatcher("/test.jsp");
		 reqDispatcher.forward(request, response);

* 通过request对象提供的getRequestDispatche(String path)方法，该方法返回一个RequestDispatcher对象，调用这个对象的forward方法可以实现请求转发

		request.getRequestDispatcher("/test.jsp").forward(request, response);

#### request域对象： ####

**域对象：当成map集合，存放的是在作用的时间范围内共享的数据。常用方法都是：..Attribute()**

---

cookie
---
**会话技术：**用户开一个浏览器，点击多个超链接，访问服务器多个web资源，然后关闭浏览器

* 客户端：Cookie服务器生成，response写回浏览器，下一次访问，浏览器携带不同的cookie，服务器接收。

* 服务器端：Session，服务器在运行时可以为每一个用户的浏览器创建一个其独享的session对象。用户在访问服务器的web资源时，可以把各自的数据放在各自的session中；访问服务器中的其它web资源时，其它web资源再从用户各自的session中取出数据为用户服务。

**作用：**保存之前的状态（数据）

#### Cookie的API： ####

* Cookie(String name, String value)：实例化Cookie对象，传入cooke名称和cookie的值

* response.addCookie(Cookie c)：写回浏览器

* request.getCookie()：获取客户端提交的Cookie

* public String getName()：取得Cookie的名字

* public String getValue()：取得Cookie的值

* **public void setMaxAge(int expiry)**：设置Cookie的有效期，如果在服务器端没有调用setMaxAge方法设置cookie的有效期，那么cookie的有效期只在一次会话过程中有效，将最大时效设为0则是命令浏览器删除该cookie

* public void setPath(String uri)：设置cookie的有效路径，比如把cookie的有效路径设置为"/xdp"，那么浏览器访问"xdp"目录下的web资源时，都会带上cookie


#### 使用cookie记录用户上一次访问的时间 ####

		Cookie[] cookies = request.getCookies();

        if (cookies!=null) {
             out.write("您上次访问的时间是：");
             for (int i = 0; i < cookies.length; i++) {
                Cookie cookie = cookies[i];
                 if (cookie.getName().equals("lastAccessTime")) {
                    Long lastAccessTime =Long.parseLong(cookie.getValue());
                    Date date = new Date(lastAccessTime);
                    out.write(date.toLocaleString());
               }
             }
         }else {
             out.write("这是您第一次访问本站！");
         }


		Cookie cookie = new Cookie("lastAccessTime", System.currentTimeMillis()+"");
		cookie.setMaxAge(24*60*60);
		response.addCookie(cookie);

#### Cookie注意细节： ####

* 一个Cookie只能标识一种信息，它至少含有一个标识该信息的名称（NAME）和设置值（VALUE）

* 一个WEB站点可以给一个WEB浏览器发送多个Cookie，一个WEB浏览器也可以存储多个WEB站点提供的Cookie

* 浏览器一般只允许存放300个Cookie，每个站点最多存放20个Cookie，每个Cookie的大小限制为4KB

---

Session
---
服务器可以为每个用户浏览器创建一个会话对象，在需要保存用户数据时，服务器程序可以把用户数据写到用户浏览器独占的session中，当用户使用浏览器访问其它程序时，其它程序可以从用户的session中取出该用户的数据，为用户服务。（**一个浏览器对应一个session对象**）

#### session和cookie的区别: ####

* Cookie是把用户的数据写给用户的浏览器
* Session是把用户的数据写到用户独占的session中

#### session实现原理： ####

**服务器创建session出来后，会把session的id号，以cookie的形式回写给客户机，只要客户机的浏览器不关，再去访问服务器时，都会带着session的id号去，服务器发现客户机浏览器带session id过来了，就会使用内存中与之对应的session为之服务。**

		HttpSession session = request.getSession();
		session.setAttribute("data", "bupt");
		String sessionId = session.getId();
        if (session.isNew()) {
             response.getWriter().print("session创建成功，session的id是："+sessionId);
        }else {
             response.getWriter().print("服务器已经存在该session了，session的id是："+sessionId);
         }

#### session对象的创建和销毁时机： ####

**session对象的创建时机：**

在程序中第一次调用request.getSession()方法时就会创建一个新的Session，可以用isNew()方法来判断Session是不是新创建的

**session对象的销毁时机：**

* 手动调用session.invalidate方法

		 HttpSession session = request.getSession();
		 //手工调用session.invalidate方法，摧毁session
		 session.invalidate();

* web.xml中配置

		<session-config>
	        <session-timeout>15</session-timeout>
	   	</session-config>

#### 防止表单重复提交： ####

在服务器端生成一个唯一的随机标识号，专业术语称为Token(令牌)，同时在当前用户的Session域中保存这个Token。然后将Token发送到客户端的Form表单中，在Form表单中使用隐藏域来存储这个Token，表单提交的时候连同这个Token一起提交到服务器端，然后在服务器端判断客户端提交上来的Token与服务器端生成的Token是否一致，如果不一致，那就是重复提交了，此时服务器端就可以不处理重复提交的表单。如果相同则处理表单提交，处理完后清除当前用户的Session域中存储的标识号。

在FormServlet中，生成Token(令牌)和跳转到form.jsp页面：

	String token = TokenProccessor.getInstance().makeToken();//创建令牌
	System.out.println("在FormServlet中生成的token："+token);
	request.getSession().setAttribute("token", token);  //在服务器使用session保存token(令牌)
	request.getRequestDispatcher("/form.jsp").forward(request, response);//跳转到form.jsp页面

在form.jsp中使用隐藏域来存储Token(令牌)：

	<input type="hidden" name="token" value="${token}"/> 
	<%--使用隐藏域存储生成的token--%>
	<%--使用EL表达式取出存储在session中的token--%>

DoFormServlet处理表单提交：

	boolean b = isRepeatSubmit(request);//判断用户是否是重复提交
	if(b==true){
	    System.out.println("请不要重复提交");
	    return;
	}
	request.getSession().removeAttribute("token");//移除session中的token
	System.out.println("处理用户提交请求！！");

         private boolean isRepeatSubmit(HttpServletRequest request) {
             String client_token = request.getParameter("token");
             //1、如果用户提交的表单数据中没有token，则用户是重复提交了表单
             if(client_token==null){
                 return true;
             }
             //取出存储在Session中的token
             String server_token = (String) request.getSession().getAttribute("token");
             //2、如果当前用户的Session中不存在Token(令牌)，则用户是重复提交了表单
             if(server_token==null){
                 return true;
             }
             //3、存储在Session中的Token(令牌)与表单提交的Token(令牌)不同，则用户是重复提交了表单
             if(!client_token.equals(server_token)){
                 return true;
             }
             
             return false;
         }

---

JSP
---
在html代码中嵌套Java代码，一种用于开发动态web资源的技术。

HttpJspBase类是继承HttpServlet的，所以HttpJspBase类是一个Servlet，而index_jsp又是继承HttpJspBase类的，所以index_jsp类也是一个Servlet，所以当浏览器访问服务器上的index.jsp页面时，其实就是在访问index_jsp这个Servlet，index_jsp这个Servlet使用_jspService这个方法处理请求。


#### JSP运行原理： ####

每个JSP 页面在第一次被访问时，WEB容器都会把请求交给JSP引擎（即一个Java程序）去处理。JSP引擎先将JSP翻译成一个_jspServlet(实质上也是一个servlet) ，然后按照servlet的调用方式进行调用。

由于JSP第一次访问时会翻译成servlet，所以第一次访问通常会比较慢，但第二次访问，JSP引擎如果发现JSP没有变化，就不再翻译，而是直接调用，所以程序的执行效率不会受到影响。

JSP引擎在调用JSP对应的_jspServlet时，会传递或创建9个与web开发相关的对象供_jspServlet使用。JSP技术的设计者为便于开发人员在编写JSP页面时获得这些web对象的引用，特意定义了9个相应的变量，**开发人员在JSP页面中通过这些变量就可以快速获得这9大对象的引用**。

在JSP中编写的java代码和html代码都会被翻译到_jspService方法中去，Java代码会原封不动的翻译成Java代码，例如：<%out.print("Hello Jsp");%>直接翻译成out.print("Hello Jsp");html排版标签都是以out.write("<html标签>\r\n");的形式输出到浏览器。

#### JSP的基础语法： ####

**1. JSP模版元素：**

JSP页面中的HTML内容称之为JSP模版元素，定义了页面的结构和外观。

**2. JSP表达式：**

	<%= 变量或表达式 %>    

用于将程序数据输出到客户端

**3. JSP脚本片断：**

	　　　　<% 
	        　　　　多行java代码            
	　　　　%>

用于在JSP页面中编写多行Java代码，**可以定义变量、编写语句，但不能定义方法**。单个脚本片断中的Java语句可以是不完整的，但是多个脚本片断组合后的结果必须是完整的Java语句。

**4. JSP声明：**

	　　　　<%！ 
	    　　　　java代码
	　　　　%>

JSP声明可用于定义JSP页面转换成的Servlet程序的**静态代码块、成员变量和方法**。

<font color='red'>JSP页面中编写的所有代码，默认会翻译到servlet的service方法中， 而Jsp声明中的java代码被翻译到_jspService方法的外面。</font>

**5. JSP注释：**

	 <!--这个注释可以看见-->          //html风格的注释
	 
	 <%
	     //JAVA中的单行注释
	 
	     /*
	         JAVA中的多行注释
	     */
	 %>
	 
	 <%--JSP自己的注释--%>

HTML的注释在浏览器中查看源文件的时候是可以看得到的。

#### JSP指令： ####

JSP指令（directive）是为JSP引擎而设计的，不直接产生任何可见输出，只是告诉引擎如何处理JSP页面中的其余部分。

JSP指令的基本语法格式：
		
	<%@ 指令 属性名="值" %>

**1. Page指令：**

page指令用于定义JSP页面的各种属性，通常是放在整个JSP页面的起始位置。

* import属性：导入所需要的包

* errorPage属性：出现异常的跳转路径，必须使用相对路径，有三个子属性可以在web.xml文件中为整个Web应用程序设置错误处理页面。

		  <error-code>子元素指定错误的状态码，例如：<error-code>404</error-code>
		  <exception-type>子元素指定异常类的完全限定名，例如：<exception-type>java.lang.ArithmeticException</exception-type>
		  <location> 子元素指定以“/”开头的错误处理页面的路径，例如：<location>/ErrorPage/404Error.jsp</location>


* isErrorPage属性：显式声明页面为错误页面

将error.jsp页面显式声明为错误处理页面后，Jsp引擎在将jsp页面翻译成Servlet的时候，在Servlet的 _jspService方法中会声明一个exception对象，然后将运行jsp出错的异常信息存储到exception对象中。

可以在error.jsp页面中使用exception对象，这样就可以在Jsp页面中拿到出错的异常信息。如果没有设置isErrorPage="true"，那么在jsp页面中是无法使用exception对象的。

	<%=exception.getMessage()%>

**2. include指令：**

include指令用于引入其它JSP页面，如果使用include指令引入了其它JSP页面，那么JSP引擎将把这两个JSP翻译成一个servlet。所以include指令引入通常也称之为静态引入。

	<%@ include file="relativeURL"%>


#### JSP九个内置对象： ####

JSP引擎在调用 JSP 对应的 _jspServlet 时，会传递或创建9个与web开发相关的对象供 _jspServlet 使用。JSP技术的设计者为便于开发人员在编写JSP页面时获得这些web对象的引用，特意定义了9个相应的变量，开发人员在JSP页面中通过这些变量就可以快速获得这9大对象的引用。

* pageContext
* request
* response
* session
* application
* config
* out
* page
* exception

**out：**

相当于带缓存的PrintWriter，缓冲默认大小是8kb，可以在page指令中设置缓冲区大小：buffer="1kb"

![](https://i.imgur.com/kPgzZUX.png)

**exception：**

只有指定当前页面为错误错里页面后才可以去使用它

**pageContext:**

* 获取其他**内置对象**（例如当我们要在service方法之外去调用session对象或者request对象等）：get***()

* 查找各个**域对象**中的属性：findAttribute()，按照查找顺序"page→request→session→application"在这四个对象中去查找，只要找到了就返回属性值，如果四个对象都没有找到要查找的属性，则返回一个null。

* 可以从四个**域对象**中获取数据：pageContext.getAttribute("name",域范围常量)

<font color='red'>在那个域中保存数据，必须在哪个域中取数据</font>

#### JSP域对象： ####

JSP有四种域对象：

* 当前页：一个属性只能在一个页面中取得，跳转到其他页面无法取得（**转发和重定向都不行**）

* 一次服务器请求：一个页面中设置的属性，只要经过了服务器跳转，则跳转之后的页面可以继续取得（**转发可以，重定向不可以**），像新闻数据，属于用户看完就没用的

* 一次会话：一个用户设置的内容，只要是与此用户相关的页面都可以访问（**转发和重定向都可以**），像购物数据，用户需要看到自己购物信息，并且等一会儿，还要用这个购物数据结帐

* 上下文：在整个服务器上设置的属性，所有人都可以访问（**全局**），像聊天数据

**如果在服务器上设置了过多的application属性，则会影响到服务器的性能**


#### JSP标签介绍： ####

* **<jsp:include>标签**  

		<jsp:include page="relativeURL | <%=expression%>" flush="true|false" />

page属性用于指定被引入资源的相对路径，它也可以通过执行一个表达式来获得；flush属性指定在插入其他资源的输出内容时，是否先将当前JSP页面的已输出的内容刷新到客户端。 

**include标签和include指令的区别：**

<jsp:include>标签是动态引入， <jsp:include>标签涉及到的2个JSP页面会被翻译成2个servlet，这2个servlet的内容在执行时进行合并。（同时声明一个变量可以） 
　　

而include指令是静态引入，涉及到的2个JSP页面会被翻译成一个servlet，其内容是在源文件级别进行合并。（同时声明一个变量不可以）

**JSP规范建议使用.jspf**（JSP fragments）作为静态引入文件的扩展名。只有用"@include"指令的时候，jspf文件的内容才会被解析并执行其中的jsp指令和tag；而使用"jsp:include"和JSTL的"c:import"时，jspf文件被当作纯文本文件处理了。

* **<jsp:forward>标签**  

		<jsp:forward page="relativeURL | <%=expression%>" /> 

page属性用于指定请求转发到的资源的相对路径，它也可以通过执行一个表达式来获得

* **<jsp:param>标签**

当使用<jsp:include>和<jsp:forward>标签引入或将请求转发给其它资源时，可以使用<jsp:param>标签向这个资源传递参数。

		语法1：
	    <jsp:include page="relativeURL | <%=expression%>">
	        <jsp:param name="parameterName" value="parameterValue|<%= expression %>" />
	    </jsp:include>
	　　语法2：
	    <jsp:forward page="relativeURL | <%=expression%>">
	        <jsp:param name="parameterName" value="parameterValue|<%= expression %>" />
	    </jsp:include>

<jsp:param>标签的name属性用于指定参数名，value属性用于指定参数值。在<jsp:include>和<jsp:forward>标签中可以使用多个<jsp:param>标签来传递多个参数。

---

JavaBean
---
JavaBean是一个遵循特定写法的Java类:

* 这个Java类必须具有一个无参的构造函数

* 属性必须私有化

* 私有化的属性必须通过public类型的方法暴露给其它程序，并且方法的命名也必须遵守一定的命名规范

在J2EE开发中，通常用于封装数据，其它程序可以通过反射技术实例化JavaBean对象，并且通过反射那些遵守命名规范的方法，从而获知JavaBean的属性，进而调用其属性保存数据。

JavaBean的属性可以是任意类型，并且一个JavaBean可以有多个属性，都需要具有相应的setter、 getter方法，命名规则为：get/set+属性名（第一个字母要大写）

#### JSP中使用Javabean ####

* **<jsp:useBean>标签**

用于在指定的域范围内查找指定名称的JavaBean对象，如果存在则直接返回该JavaBean对象的引用，如果不存在则实例化一个新的JavaBean对象并将它以指定的名称存储到指定的域范围中。

	<jsp:useBean id="beanName" class="package.class" scope="page|request|session|application"/>
"id"属性用于指定JavaBean实例对象的引用名称和其存储在域范围中的名称；

"class"属性用于指定JavaBean的完整类名（即必须带有包名）；

"scope"属性用于指定JavaBean实例对象所存储的域范围，其取值只能是page、request、session和application等四个值中的一个，其默认值是page。

* **<jsp:setProperty>标签**

用于设置和访问JavaBean对象的属性（**既可以直接设置，也可以接收用户输入的参数值**）

	　　语法格式一：
	　　　　<jsp:setProperty name="beanName" property="propertyName" value="string字符串"/>
	
	　　语法格式二：
	　　　　<jsp:setProperty name="beanName" property="propertyName" value="<%= expression %>" />
	
	　　语法格式三：
	　　　　<jsp:setProperty name="beanName" property="propertyName" param="parameterName"/>
	
	　　语法格式四：
	　　　　<jsp:setProperty name="beanName" property= "*" />
name属性用于指定JavaBean对象的名称；

property属性用于指定JavaBean实例对象的属性名；

value属性用于指定JavaBean对象的某个属性的值，value的值可以是字符串，也可以是表达式；

param属性用于将JavaBean实例对象的某个属性值设置为一个请求参数值（**接收用户输入的参数**）。

* **<jsp:getProperty>标签**

用于读取JavaBean对象的属性

	<jsp:getProperty name="beanInstanceName" property="PropertyName" />

name属性用于指定JavaBean实例对象的名称，其值应与<jsp:useBean>标签的id属性值相同；

property属性用于指定JavaBean实例对象的属性名。

---

JDBC
---

Java中通过加载具体的驱动，操作数据库的一套接口。开发 JDBC 应用主要由 java.sql 和 javax.sql 两个包组成，并导入相应的数据库驱动。

![](https://i.imgur.com/xZ6EPbE.png)

### 编写JDBC程序： ###

**搭建实验环境：**  

* 在mysql中创建一个库，并创建表和插入表的数据

* 新建一个Java工程，并导入数据驱动

![](https://i.imgur.com/Ky84AS6.png)
  
* 编写程序

	     public static void main(String[] args) throws Exception {
	         //要连接的数据库URL
	         String url = "jdbc:mysql://localhost:3306/jdbcStudy";
	         //连接的数据库时使用的用户名
	         String username = "root";
	         //连接的数据库时使用的密码
	         String password = "XDP";
	         
	         //1.加载驱动
	         //DriverManager.registerDriver(new com.mysql.jdbc.Driver());不推荐使用这种方式来加载驱动
	         Class.forName("com.mysql.jdbc.Driver");//推荐使用这种方式来加载驱动
	         //2.获取与数据库的链接
	         Connection conn = DriverManager.getConnection(url, username, password);
	         
	         //3.获取用于向数据库发送sql语句的statement
	         Statement st = conn.createStatement();
	         
	         String sql = "select id,name,password,email,birthday from users";
	         //4.向数据库发sql,并获取代表结果集的resultset
	         ResultSet rs = st.executeQuery(sql);
	         
	         //5.取出结果集的数据
	         while(rs.next()){
	             System.out.println("id=" + rs.getObject("id"));
	             System.out.println("name=" + rs.getObject("name"));
	             System.out.println("password=" + rs.getObject("password"));
	             System.out.println("email=" + rs.getObject("email"));
	             System.out.println("birthday=" + rs.getObject("birthday"));
	         }
	         
	         //6.关闭链接，释放资源
	         rs.close();
	         st.close();
	         conn.close();
	     }

**DriverManager类：**

dbc程序中的DriverManager用于加载驱动，并创建与数据库的链接，有以下的方法：

* DriverManager.registerDriver(new Driver())
* DriverManager.getConnection(url, user, password)

在实际开发中并不推荐采用registerDriver方法注册驱动：如果采用此种方式，会导致驱动程序注册两次，也就是在内存中会有两个Driver对象；程序依赖mysql的api，脱离mysql的jar包，程序将无法编译，将来程序切换底层数据库将会非常麻烦。

推荐加载驱动方式：Class.forName("com.mysql.jdbc.Driver");

**数据库URL：**

![](https://i.imgur.com/NUiaGYC.png)

* Oracle写法：jdbc:oracle:thin:@localhost:1521:sid
* SqlServer写法：jdbc:microsoft:sqlserver://localhost:1433; DatabaseName=sid
* MySql写法：jdbc:mysql://localhost:3306/sid


**Connection类：**

Jdbc程序中的Connection，它用于代表数据库的链接，客户端与数据库所有交互都是通过connection对象完成的，常用的方法有：

* createStatement()：创建向数据库发送sql的statement对象。
* prepareStatement(sql) ：创建向数据库发送预编译sql的PrepareSatement对象。
* prepareCall(sql)：创建执行存储过程的callableStatement对象。
* setAutoCommit(boolean autoCommit)：设置事务是否自动提交。（开启事务）
* commit() ：在链接上提交事务。
* rollback() ：在此链接上回滚事务。

**Statement类：**

Jdbc程序中的Statement对象用于向数据库发送SQL语句，常用的方法有：

* executeQuery(String sql) ：用于向数据发送查询语句（**返回代表查询结果的ResultSet对象**）
* executeUpdate(String sql)：用于向数据库发送insert、update或delete语句（**返回一个整数影响行数**）
* execute(String sql)：用于向数据库发送任意sql语句
* addBatch(String sql) ：把多条sql语句放到一个批处理中
* executeBatch()：向数据库发送一批sql语句执行

**PreperedStatement**是Statement的子类，可以避免SQL注入的问题，Statement会使数据库频繁编译SQL，可能造成数据库缓冲区溢出。PreparedStatement可对SQL进行预编译，从而提高数据库的执行效率。并且PreperedStatement对于sql中的参数，允许使用占位符的形式进行替换，简化sql语句的编写。可以 **st.setString(int 第几个占位符，实参) 来设置参数**

**ResultSet类：**

Jdbc程序中的ResultSet用于代表Sql语句的执行结果，该对象提供的都是用于获取数据的get方法：

获取任意类型的数据：

* getObject(int index)
* getObject(string columnName)

获取指定类型的数据：

* getString(int index)
* getString(String columnName)

ResultSet还提供了对结果集进行滚动的方法：

* next()：移动到下一行
* Previous()：移动到前一行
* absolute(int row)：移动到指定行
* beforeFirst()：移动resultSet的最前面
* afterLast() ：移动到resultSet的最后面

**释放资源：**

jdbc程序运行完后，切记要释放程序在运行过程中，创建的那些与数据库进行交互的对象（通常是ResultSet, Statement和Connection对象），用完后必须马上释放。


### JDBC处理大数据： ###

大数据也称为LOB（Large Objects），分为 **clob** 和 **blob** ，clob用于存储大文本，blob用于存储二进制数据（图像、声音等等）。对MySQL而言只有blob，而没有clob，mysql存储大文本采用的是Text。

对于MySQL中的Text类型，可调用如下方法设置：

	PreparedStatement.setCharacterStream(index, reader, length);//注意length长度须设置，并且设置为int型

对于MySQL中的Text类型，可调用如下方法获取：

	1 reader = resultSet. getCharacterStream(String columnLabel);
	2 string s = resultSet.getString(String columnLabel);

对于MySQL中的BLOB类型，可调用如下方法设置：

	PreparedStatement. setBinaryStream(i, inputStream, length);

对于MySQL中的BLOB类型，可调用如下方法获取：

	1 InputStream in  = resultSet.getBinaryStream(String columnLabel);
	2 InputStream in  = resultSet.getBlob(String columnLabel).getBinaryStream(); 

### 使用JDBC进行批处理： ###

向数据库发送一批SQL语句执行，JDBC实现批处理有两种方式：statement和preparedstatement

* 使用Statement完成批处理：

  * 使用Statement对象**添加**要批量执行SQL语句，如下：

			 Statement.addBatch(sql1);
			 Statement.addBatch(sql2);
			 Statement.addBatch(sql3);

  * **执行**批处理SQL语句：Statement.executeBatch();
  
  * **清除**批处理命令：Statement.clearBatch();

             conn = JdbcUtils.getConnection();
             String sql1 = "insert into testbatch(id,name) values(1,'aaa')";
             String sql2 = "insert into testbatch(id,name) values(2,'bbb')";
             String sql3 = "insert into testbatch(id,name) values(3,'CCC')";
             String sql4 = "insert into testbatch(id,name) values(4,'DDD')";
             String sql5 = "update testbatch set name='gacl' where id=1";
             String sql6 = "insert into testbatch(id,name) values(5,'FFF')";
             String sql7 = "delete from testbatch where id=2";
             st = conn.createStatement();
             //添加要批量执行的SQL
             st.addBatch(sql1);
             st.addBatch(sql2);
             st.addBatch(sql3);
             st.addBatch(sql4);
             st.addBatch(sql5);
             st.addBatch(sql6);
             st.addBatch(sql7);
             //执行批处理SQL语句
             st.executeBatch();
             //清除批处理命令
             st.clearBatch();

可以向数据库发送多条不同的 SQL 语句，但是仅参数不同时，需要重复写上很多条 SQL 语句。

* 使用PreparedStatement完成批处理：

 			conn = JdbcUtils.getConnection();
            String sql = "insert into testbatch(id,name) values(?,?)";
            st = conn.prepareStatement(sql);
            for(int i=1;i<1000008;i++){  //i=1000  2000
                st.setInt(1, i);
                st.setString(2, "aa" + i);
                st.addBatch();
                if(i%1000==0){
                     st.executeBatch();
                     st.clearBatch();
				}
			}
			st.executeBatch();

只能应用在SQL语句相同，但参数不同的批处理中。因此此种形式的批处理经常用于在同一个表中批量插入数据，或批量更新表的数据。

### 事务： ###

* Connection.setAutoCommit(false);//开启事务(start transaction)

* Connection.rollback();//回滚事务(rollback)

* Connection.commit();//提交事务(commit)

模拟转账成功时的业务场景：

              conn = JdbcUtils.getConnection();
              conn.setAutoCommit(false);//通知数据库开启事务(start transaction)
              String sql1 = "update account set money=money-100 where name='A'";
              st = conn.prepareStatement(sql1);
              st.executeUpdate();
              String sql2 = "update account set money=money+100 where name='B'";
              st = conn.prepareStatement(sql2);
              st.executeUpdate();
              conn.commit();//上面的两条SQL执行Update语句成功之后就通知数据库提交事务(commit)
              System.out.println("成功！！！");  //log4j

模拟转账过程中出现异常导致有一部分SQL执行失败时手动通知数据库回滚事务：

         try{
             conn = JdbcUtils.getConnection();
             conn.setAutoCommit(false);//通知数据库开启事务(start transaction)
             String sql1 = "update account set money=money-100 where name='A'";
             st = conn.prepareStatement(sql1);
             st.executeUpdate();
             //用这句代码模拟执行完SQL1之后程序出现了异常而导致后面的SQL无法正常执行，事务也无法正常提交
             int x = 1/0;
             String sql2 = "update account set money=money+100 where name='B'";
             st = conn.prepareStatement(sql2);
             st.executeUpdate();
             conn.commit();//上面的两条SQL执行Update语句成功之后就通知数据库提交事务(commit)
             System.out.println("成功！！！");
         }catch (Exception e) {
             try {
                 //捕获到异常之后手动通知数据库执行回滚事务的操作
                 conn.rollback();
             } catch (SQLException e1) {
                 e1.printStackTrace();
             }
             e.printStackTrace();
         }finally{
             JdbcUtils.release(conn, st, rs);
         }


**设置事务回滚点：**

	　　Savepoint sp = conn.setSavepoint();
	　　Conn.rollback(sp);
	　　Conn.commit();//回滚后必须通知数据库提交事务

回滚到设置的事务回滚点，回滚后必须得通知数据库提交事务。

**事务的四大特性：ACID**

原子性、一致性、隔离性、持久性

**事务的隔离级别：**

不考虑隔离性会出现的问题：

* 脏读：一个事务读取了另外一个事务未提交的数据

* 不可重复读：一个事务内读取表中的某一行数据，多次读取结果不同（针对 Update 操作）

* 幻读：在一个事务内读取到了别的事务插入的数据，导致前后读取不一致（针对 Insert 操作）

四种隔离级别：

* Serializable(串行化)：可避免脏读、不可重复读、幻读情况的发生。

* Repeatable read(可重复读)：可避免脏读、不可重复读情况的发生。

* Read committed(读已提交)：可避免脏读情况发生。

* Read uncommitted(读未提交)：最低级别，以上情况均无法保证。


mysql数据库设置事务隔离级别：set transaction isolation level 隔离级别名


mysql数据库查询当前事务隔离级别：select @@tx_isolation


java 中控制隔离级别：Connection 的 api:setTransactionIsolation(int level) 常量1，2，4，8


### 数据库连接池： ###

**直接获取数据库连接**：数据库创建连接通常需要消耗相对较大的资源，创建时间也较长，极大的浪费数据库的资源，极易造成数据库服务器内存溢出、拓机。

![](https://i.imgur.com/hEmf0TB.png)

**通过数据库连接池获取连接**：数据库连接池负责分配,管理和释放数据库连接,它允许应用程序重复使用一个现有的数据库连接,而不是重新建立一个。

![](https://i.imgur.com/1vz3Y7h.png)

数据库连接池在初始化时将创建一定数量的数据库连接放到连接池中，当应用程序向连接池请求的连接数超过最大连接数量时,这些请求将被加入到等待队列中。

**编写数据库连接池：**

编写连接池需实现java.sql.DataSource接口，DataSource接口中定义了两个重载的getConnection方法：

* Connection getConnection()
* Connection getConnection(String username, String password)

**步骤：**

* 在DataSource构造函数中批量创建与数据库的连接，并把创建的连接加入LinkedList对象中；

* 实现getConnection方法，让getConnection方法每次调用时，从LinkedList中取一个Connection返回给用户；

* 当用户使用完Connection，调用Connection.close()方法时，Collection对象应保证将自己返回到LinkedList中,而不要把conn还给数据库。

 proxyConn = (Connection) Proxy.newProxyInstance(this.getClass()
             .getClassLoader(), conn.getClass().getInterfaces(),
             new InvocationHandler() {
         //此处为内部类，当close方法被调用时将conn还回池中,其它方法直接执行
             public Object invoke(Object proxy, Method method,
                       Object[] args) throws Throwable {
                 if (method.getName().equals("close")) {
                     pool.addLast(conn);
                     return null;
             }
             return method.invoke(conn, args);
         }
     });

### 开源数据库连接池： ###

* DBCP 数据库连接池

  * **导入相关jar包：**Commons-dbcp.jar：连接池的实现，Commons-pool.jar：连接池实现的依赖库
  
  * **在类目录下加入dbcp的配置文件：dbcpconfig.properties**

			#连接设置
			driverClassName=com.mysql.jdbc.Driver
			url=jdbc:mysql://localhost:3306/jdbcstudy
			username=root
			password=XDP
			
			#<!-- 初始化连接 -->
			initialSize=10
			
			#最大连接数量
			maxActive=50
			
			#<!-- 最大空闲连接 -->
			maxIdle=20
			
			#<!-- 最小空闲连接 -->
			minIdle=5
			
			#<!-- 超时等待时间以毫秒为单位 6000毫秒/1000等于60秒 -->
			maxWait=60000
			
			
			#JDBC驱动建立连接时附带的连接属性属性的格式必须为这样：[属性名=property;] 
			#注意："user" 与 "password" 两个属性会被明确地传递，因此这里不需要包含他们。
			connectionProperties=useUnicode=true;characterEncoding=UTF8
			
			#指定由连接池所创建的连接的自动提交（auto-commit）状态。
			defaultAutoCommit=true
			
			#driver default 指定由连接池所创建的连接的只读（read-only）状态。
			#如果没有设置该值，则“setReadOnly”方法将不被调用。（某些驱动并不支持只读模式，如：Informix）
			defaultReadOnly=
			
			#driver default 指定由连接池所创建的连接的事务级别（TransactionIsolation）。
			#可用值为下列之一：（详情可见javadoc。）NONE,READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
			defaultTransactionIsolation=READ_UNCOMMITTED

  * **在获取数据库连接的工具类(如jdbcUtils)的静态代码块中创建池**
  
		     private static DataSource ds = null;
		     //在静态代码块中创建数据库连接池
		     static{
		         try{
		             //加载dbcpconfig.properties配置文件
		             InputStream in = JdbcUtils_DBCP.class.getClassLoader().getResourceAsStream("dbcpconfig.properties");
		             Properties prop = new Properties();
		             prop.load(in);
		             //创建数据源
		             ds = BasicDataSourceFactory.createDataSource(prop);
		         }catch (Exception e) {
		             throw new ExceptionInInitializerError(e);
		         }
		     }

* C3P0 数据库连接池（有自动回收空闲连接功能）

 * **导入相关jar包：**c3p0-0.9.2-pre1.jar、mchange-commons-0.2.jar，如果操作的是Oracle数据库，那么还需要导入c3p0-oracle-thin-extras-0.9.2-pre1.jar
 
 * **在类目录下加入C3P0的配置文件：c3p0-config.xml**
     
			 <!--
		     C3P0的缺省(默认)配置，
		     如果在代码中“ComboPooledDataSource ds = new ComboPooledDataSource();”这样写就表示使用的是C3P0的缺省(默认)配置信息来创建数据源
		     -->
			<default-config>
		         <property name="driverClass">com.mysql.jdbc.Driver</property>
		         <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy</property>
		         <property name="user">root</property>
		         <property name="password">XDP</property>
		         
		         <property name="acquireIncrement">5</property>
		         <property name="initialPoolSize">10</property>
		         <property name="minPoolSize">5</property>
		         <property name="maxPoolSize">20</property>
		     </default-config>

		     <!--
		     C3P0的命名配置，
		     如果在代码中“ComboPooledDataSource ds = new ComboPooledDataSource("MySQL");”这样写就表示使用的是name是MySQL的配置信息来创建数据源
		     -->
		     <named-config name="MySQL">
		         <property name="driverClass">com.mysql.jdbc.Driver</property>
		         <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy</property>
		         <property name="user">root</property>
		         <property name="password">XDP</property>
		         
		         <property name="acquireIncrement">5</property>
		         <property name="initialPoolSize">10</property>
		         <property name="minPoolSize">5</property>
		         <property name="maxPoolSize">20</property>
		     </named-config>

 * **在获取数据库连接的工具类(如jdbcUtils)的静态代码块中创建池**

		     private static ComboPooledDataSource ds = null;
		     //在静态代码块中创建数据库连接池
		     static{
		         try{
		             //通过代码创建C3P0数据库连接池
		             /*ds = new ComboPooledDataSource();
		             ds.setDriverClass("com.mysql.jdbc.Driver");
		             ds.setJdbcUrl("jdbc:mysql://localhost:3306/jdbcstudy");
		             ds.setUser("root");
		             ds.setPassword("XDP");
		             ds.setInitialPoolSize(10);
		             ds.setMinPoolSize(5);
		             ds.setMaxPoolSize(20);*/
		             
		             //通过读取C3P0的xml配置文件创建数据源，C3P0的xml配置文件c3p0-config.xml必须放在src目录下
		             //ds = new ComboPooledDataSource();//使用C3P0的默认配置来创建数据源
		             ds = new ComboPooledDataSource("MySQL");//使用C3P0的命名配置来创建数据源
		             
		         }catch (Exception e) {
		             throw new ExceptionInInitializerError(e);
		         }
		     }


**JNDI技术简介：**

JNDI(Java Naming and Directory Interface)，Java命名和目录接口。

主要作用在于：它可以把Java对象放在一个容器中（JNDI容器），并为容器中的java对象取一个名称，以后程序想获得Java对象，只需通过名称检索即可。其核心API为Context，它代表JNDI容器，其lookup方法为检索容器中对应名称的对象。

配置tomcat服务器的数据源：

	 <Context>
	   <Resource name="jdbc/datasource" auth="Container"
	             type="javax.sql.DataSource" username="root" password="XDP"
	             driverClassName="com.mysql.jdbc.Driver" 
	             url="jdbc:mysql://localhost:3306/jdbcstudy"
	             maxActive="8" maxIdle="4"/>
	 </Context>

获取数据源资源：

	 Context initCtx = new InitialContext();
	 Context envCtx = (Context) initCtx.lookup("java:comp/env");
	 dataSource = (DataSource)envCtx.lookup("jdbc/datasource");

平时做javaEE开发时，服务器会为我们的应用程序创建很多资源，比如request对象，response对象，服务器创建的这些资源有两种方式提供给我们的应用程序使用：

* 第一种是通过方法参数的形式传递进来，比如我们在Servlet中写的doPost和doGet方法中使用到的request对象和response对象就是服务器以参数的形式传递给我们的；
* 第二种就是JNDI的方式，服务器把创建好的资源绑定到JNDI容器中去，应用程序想要使用资源时，就直接从JNDI容器中获取相应的资源即可。



**配置Tomcat数据源：**

让Tomcat服务器在启动的时候帮我们创建一个数据库连接池。数据库的驱动jar文件需放置在tomcat的lib下。

步骤：

* 在Web项目的WebRoot目录下的META-INF目录创建一个context.xml文件

* 在context.xml文件配置tomcat服务器的数据源

		 <Context>
		    <Resource 
		        name="jdbc/datasource" 
		        auth="Container"
		        type="javax.sql.DataSource" 
		        username="root" 
		        password="XDP"
		        driverClassName="com.mysql.jdbc.Driver" 
		        url="jdbc:mysql://localhost:3306/jdbcstudy"
		        maxActive="8" 
		        maxIdle="4"/>
		 </Context>

* 将数据库的驱动jar文件需放置在tomcat的lib下

* 在获取数据库连接的工具类(如jdbcUtils)的静态代码块中获取JNDI容器中的数据源

	     private static DataSource ds = null;
	     //在静态代码块中创建数据库连接池
	     static{
	         try{
	              //初始化JNDI
	             Context initCtx = new InitialContext();
	              //得到JNDI容器
	             Context envCtx = (Context) initCtx.lookup("java:comp/env");
	              //从JNDI容器中检索name为jdbc/datasource的数据源
	             ds = (DataSource)envCtx.lookup("jdbc/datasource");
	         }catch (Exception e) {
	             throw new ExceptionInInitializerError(e);
	         }
     
### 编写自己的JDBC框架： ###

**元数据：**

* DataBaseMetaData元数据

Connection.getDatabaseMetaData()获得代表DatabaseMetaData元数据的DatabaseMetaData对象。

	public void testDataBaseMetaData() throws SQLException {
         Connection conn = JdbcUtils.getConnection();
         DatabaseMetaData metadata = conn.getMetaData();
         //getURL()：返回一个String类对象，代表数据库的URL
         System.out.println(metadata.getURL());
         //getUserName()：返回连接当前数据库管理系统的用户名
         System.out.println(metadata.getUserName());
         //getDatabaseProductName()：返回数据库的产品名称
         System.out.println(metadata.getDatabaseProductName());
         //getDatabaseProductVersion()：返回数据库的版本号
         System.out.println(metadata.getDatabaseProductVersion());
         //getDriverName()：返回驱动驱动程序的名称
         System.out.println(metadata.getDriverName());
         //getDriverVersion()：返回驱动程序的版本号
         System.out.println(metadata.getDriverVersion());
         //isReadOnly()：返回一个boolean值，指示数据库是否只允许读操作
         System.out.println(metadata.isReadOnly());
         JdbcUtils.release(conn, null, null);
     }

* ParameterMetaData元数据

PreparedStatement.getParameterMetaData() 获得代表PreparedStatement元数据的ParameterMetaData对象。

     public void testParameterMetaData() throws SQLException {
         Connection conn = JdbcUtils.getConnection();
         String sql = "select * from user wherer name=? and password=?";
         //将SQL预编译一下
         PreparedStatement st = conn.prepareStatement(sql);
         ParameterMetaData pm = st.getParameterMetaData();
         //getParameterCount() 获得指定参数的个数
         System.out.println(pm.getParameterCount());
         //getParameterType(int param)：获得指定参数的sql类型，MySQL数据库驱动不支持
         System.out.println(pm.getParameterType(1));
         JdbcUtils.release(conn, null, null);
     }


* ResultSetMetaData元数据

ResultSet.getMetaData() 获得代表ResultSet对象元数据的ResultSetMetaData对象。

     public void testResultSetMetaData() throws Exception {
         Connection conn = JdbcUtils.getConnection();
         String sql = "select * from account";
         PreparedStatement st  = conn.prepareStatement(sql);
         ResultSet rs = st.executeQuery();
         //ResultSet.getMetaData()获得代表ResultSet对象元数据的ResultSetMetaData对象
         ResultSetMetaData metadata = rs.getMetaData();
         //getColumnCount() 返回resultset对象的列数
         System.out.println(metadata.getColumnCount());
         //getColumnName(int column) 获得指定列的名称
         System.out.println(metadata.getColumnName(1));
         //getColumnTypeName(int column)获得指定列的类型
         System.out.println(metadata.getColumnTypeName(1));
         JdbcUtils.release(conn, st, rs);
     }


**封装简单的JDBC框架：**







































---

Filter
---
Servlet API中提供了一个Filter接口，实现这个接口的Java类称之为过滤器，实现用户在访问某个目标资源之前，对访问的请求和响应进行拦截，如实现URL级别的权限访问控制、过滤敏感词汇、压缩响应信息等一些高级功能。

![](https://i.imgur.com/ITJuGLH.png)

#### 实现原理： ####

Filter接口中有一个doFilter方法，当我们编写好Filter，并配置对哪个web资源进行拦截后，WEB服务器每次在调用web资源的service方法之前，都会先调用一下filter的doFilter方法。可在在该方法内编写代码：

* 调用目标资源之前，让一段代码执行

* 是否调用目标资源

* 调用目标资源之后，让一段代码执行

web服务器在调用doFilter方法时，会传递一个filterChain对象进来，它也提供了一个doFilter方法，可以将请求放行到下一个过滤器，直到最后一个过滤器放行才可以访问web资源。

#### 实现步骤： ####

* 编写java类实现Filter接口，并实现其doFilter方法

         System.out.println("FilterDemo01执行前！！！");
         chain.doFilter(request, response);  //让目标资源执行，放行
         System.out.println("FilterDemo01执行后！！！");

* 在 web.xml 文件中使用<filter>和<filter-mapping>元素对编写的filter类进行注册，并设置它所能拦截的资源

		   <!--配置过滤器-->
		   <filter>
		       <filter-name>FilterDemo01</filter-name>
		       <filter-class>me.gacl.web.filter.FilterDemo01</filter-class>
		   </filter>
		   
		   <!--映射过滤器-->
		   <filter-mapping>
		       <filter-name>FilterDemo01</filter-name>
		       <!--“/*”表示拦截所有的请求 -->
		       <url-pattern>/*</url-pattern>
		   </filter-mapping>

* Filter链

web服务器根据Filter在web.xml文件中的注册顺序，决定先调用哪个Filter，当第一个Filter的doFilter方法被调用时，web服务器会创建一个代表Filter链的FilterChain对象传递给该方法。在doFilter方法中，开发人员如果调用了FilterChain对象的doFilter方法，则web服务器会检查FilterChain对象中是否还有filter，如果有，则调用第2个filter，如果没有，则调用目标资源。

#### Filter的生命周期： ####

* **Filter的创建：**

web 应用程序启动时，web 服务器将创建Filter 的实例对象，并调用其init方法，完成对象的初始化功能。

* **Filter的销毁：**

Web容器调用destroy方法销毁Filter，在destroy方法中，可以释放过滤器使用的资源。


**FilterConfig接口：**

用户在配置filter时，可以使用<init-param>为filter配置一些初始化参数，当web容器实例化Filter对象，调用其init方法时，会把封装了filter初始化参数的filterConfig对象传递进来。通过filterConfig对象的方法可以获得**初始化参数和当前的名称**。

#### Filter的部署： ####

* **注册Filter：**

		 <filter>
		           <description>FilterDemo02过滤器</description>
		           <filter-name>FilterDemo02</filter-name>
		           <filter-class>me.gacl.web.filter.FilterDemo02</filter-class>
		           <!--配置FilterDemo02过滤器的初始化参数-->
		           <init-param>
		               <description>配置FilterDemo02过滤器的初始化参数</description>
		               <param-name>name</param-name>
		               <param-value>gacl</param-value>
		           </init-param>
		           <init-param>
		               <description>配置FilterDemo02过滤器的初始化参数</description>
		               <param-name>like</param-name>
		               <param-value>java</param-value>
		           </init-param>
		 </filter>

* **映射Filter:**

		 <filter-mapping>
		     <filter-name>testFilter</filter-name>
		    <url-pattern>/index.jsp</url-pattern>
		    <dispatcher>REQUEST</dispatcher>
		    <dispatcher>FORWARD</dispatcher>
		 </filter-mapping>

<url-pattern>设置 filter 所拦截的请求路径(过滤器关联的URL样式)

<servlet-name>指定过滤器所拦截的Servlet名称

<dispatcher>指定过滤器所拦截的资源被 Servlet 容器调用的方式

* REQUEST：当用户直接访问页面时，Web容器将会调用过滤器。
* INCLUDE：如果目标资源是通过RequestDispatcher的include()方法访问时，那么该过滤器将被调用。
* FORWARD：如果目标资源是通过RequestDispatcher的forward()方法访问时，那么该过滤器将被调用。
* ERROR：如果目标资源是通过声明式异常处理机制调用时，那么该过滤器将被调用。


### 高级开发： ###

* 统一全站字符编码
* 禁止浏览器缓存所有动态页面

         response.setDateHeader("Expires", -1);
         response.setHeader("Cache-Control", "no-cache");
         response.setHeader("Pragma", "no-cache");

* 控制浏览器缓存页面中的静态资源
* 实现用户自动登陆

[https://www.cnblogs.com/xdp-gacl/p/3948422.html](https://www.cnblogs.com/xdp-gacl/p/3948422.html)

#### 使用Decorator设计模式增强request对象： ####

Servlet API 中提供了一个request对象的Decorator设计模式的默认实现类**HttpServletRequestWrapper**。当需要增强request对象时，只需要写一个类继承HttpServletRequestWrapper类，然后重写需要增强的方法即可。

步骤：

* 实现与被增强对象相同的接口 
* 定义一个变量记住被增强对象
* 定义一个构造函数，接收被增强对象
* 覆盖需要增强的方法
* 对于不想增强的方法，直接调用被增强对象（目标对象）的方法

**解决get和post请求方式下的中文乱码问题**

**实现html标签转义功能**

**实现敏感字符过滤功能**

#### 使用Decorator设计模式增强response对象： ####

Servlet  API 中提供了response对象的Decorator设计模式的默认实现类**HttpServletResponseWrapper**。 

**压缩响应正文内容**

**缓存数据到内存**

[https://www.cnblogs.com/xdp-gacl/p/3952405.html](https://www.cnblogs.com/xdp-gacl/p/3952405.html)

---

Listener
---
#### 监听器： ####

参考观察者模式：

![](https://i.imgur.com/SFJfPve.png)

#### JavaWeb中的监听器： ####

**概念：** 用于监听web应用程序中的ServletContext, HttpSession和 ServletRequest等域对象的创建与销毁事件，以及监听这些域对象中的属性发生修改的事件。

**分类：**

* 监听域对象自身的创建和销毁的事件监听器

* 监听域对象中的属性的增加和删除的事件监听器

* 监听绑定到HttpSession域中的某个对象的状态的事件监听器

**监听ServletContext域对象的创建和销毁：**

实现了ServletContextListener接口的类都可以对ServletContext对象的创建和销毁进行监听。

当ServletContext对象被创建时，激发contextInitialized (ServletContextEvent sce)方法。

当ServletContext对象被销毁时，激发contextDestroyed(ServletContextEvent sce)方法。

步骤：

* 编写一个MyServletContextListener类监听器，实现ServletContextListener接口

		 public class MyServletContextListener implements ServletContextListener {
		 
		     @Override
		     public void contextInitialized(ServletContextEvent sce) {
		         System.out.println("ServletContext对象创建");
		     }
		 
		     @Override
		     public void contextDestroyed(ServletContextEvent sce) {
		         System.out.println("ServletContext对象销毁");
		     }
		 }

* 将监听器注册到事件源上，在web.xml文件中注册监听器

		   <listener>
		       <description>ServletContextListener监听器</description>
		       <!--实现了ServletContextListener接口的监听器类 -->
		       <listener-class>me.gacl.web.listener.MyServletContextListener</listener-class>
		   </listener>

Web服务器在启动时，就会自动把在web.xml中配置的监听器注册到ServletContext对象上，这样开发好的MyServletContextListener监听器就可以对ServletContext对象进行监听了

**监听HttpSession域对象的创建和销毁：**

HttpSessionListener 接口用于监听HttpSession对象的创建和销毁。
　　

创建一个Session时，激发sessionCreated (HttpSessionEvent se) 方法。
　　

销毁一个Session时，激发sessionDestroyed (HttpSessionEvent se) 方法。

	     <listener>
	        <description>HttpSessionListener监听器</description>
	        <listener-class>me.gacl.web.listener.MyHttpSessionListener</listener-class>
	    </listener>
	   <!-- 配置HttpSession对象的销毁时机 -->
	    <session-config>
	        <!--配置HttpSession对象的1分钟之后销毁 -->
	        <session-timeout>1</session-timeout>
	   </session-config>


**监听ServletRequest域对象的创建和销毁：**

ServletRequestListener接口用于监听ServletRequest 对象的创建和销毁。

Request对象被创建时，监听器的requestInitialized(ServletRequestEvent sre)方法将会被调用。

Request对象被销毁时，监听器的requestDestroyed(ServletRequestEvent sre)方法将会被调用。

**监听域对象中属性的变更的监听器：**

三个监听器接口分别是ServletContextAttributeListener, HttpSessionAttributeListener 和ServletRequestAttributeListener，三个接口中都定义了三个方法来处理被监听对象中的属性的增加，删除和替换的事件，**同一个事件在这三个接口中对应的方法名称完全相同，只是接受的参数类型不同**。

* 当向被监听对象中增加一个属性时，这个方法接收一个事件类型的参数，监听器可以通过这个参数来获得正在增加属性的域对象和被保存到域中的属性对象

		public void attributeAdded(ServletContextAttributeEvent scae)
		public void attributeAdded(HttpSessionBindingEvent  hsbe)
		public void attributeAdded(ServletRequestAttributeEvent srae)

* 当删除被监听对象中的一个属性时，web容器调用事件监听器的attributeRemoved方法进行响应

		 public void attributeRemoved(ServletContextAttributeEvent scae)
		 public void attributeRemoved (HttpSessionBindingEvent  hsbe)
		 public void attributeRemoved (ServletRequestAttributeEvent srae)

* 当监听器的域对象中的某个属性被替换时，web容器调用事件监听器的attributeReplaced方法进行响应

		 public void attributeReplaced(ServletContextAttributeEvent scae)
		 public void attributeReplaced (HttpSessionBindingEvent  hsbe)
		 public void attributeReplaced (ServletRequestAttributeEvent srae)

**感知Session绑定的事件监听器：**

保存在Session域中的对象可以有多种状态，**绑定**（session.setAttribute("bean",Object)）、**解绑**（session.removeAttribute("bean")）、**钝化**（随Session对象持久化到一个存储设备中）、**活化**（随Session对象从一个存储设备中恢复）。

* **HttpSessionBindingListener：**

实现了HttpSessionBindingListener接口的JavaBean对象可以感知自己被绑定到Session中和 Session中删除的事件。

当对象被绑定到HttpSession对象中时，web服务器调用该对象的void valueBound(HttpSessionBindingEvent event)方法。

当对象从HttpSession对象中解除绑定时，web服务器调用该对象的void valueUnbound(HttpSessionBindingEvent event)方法。

* **HttpSessionActivationListener：**

实现了HttpSessionActivationListener接口的JavaBean对象可以感知自己被活化(反序列化)和钝化(序列化)的事件。

当绑定到HttpSession对象中的javabean对象将要随HttpSession对象被钝化(序列化)之前，web服务器调用该javabean对象的void sessionWillPassivate(HttpSessionEvent event) 方法。

当绑定到HttpSession对象中的javabean对象将要随HttpSession对象被活化(反序列化)之后，web服务器调用该javabean对象的void sessionDidActive(HttpSessionEvent event)方法。


#### 高级开发： ####

**统计当前在线人数**

	14 public class OnLineCountListener implements HttpSessionListener {
	15 
	16     @Override
	17     public void sessionCreated(HttpSessionEvent se) {
	18         ServletContext context = se.getSession().getServletContext();
	19         Integer onLineCount = (Integer) context.getAttribute("onLineCount");
	20         if(onLineCount==null){
	21             context.setAttribute("onLineCount", 1);
	22         }else{
	23             onLineCount++;
	24             context.setAttribute("onLineCount", onLineCount);
	25         }
	26     }
	27 
	28     @Override
	29     public void sessionDestroyed(HttpSessionEvent se) {
	30         ServletContext context = se.getSession().getServletContext();
	31         Integer onLineCount = (Integer) context.getAttribute("onLineCount");
	32         if(onLineCount==null){
	33             context.setAttribute("onLineCount", 1);
	34         }else{
	35             onLineCount--;
	36             context.setAttribute("onLineCount", onLineCount);
	37         }
	38     }
	39 }

**自定义Session扫描器**



---

web开发模式
---

**web开发中的请求响应模型：**

![](https://i.imgur.com/SajOaAH.png)

**标准MVC模型：**

![](https://i.imgur.com/6XZnPdw.png)

Model（模型）：数据模型，提供要展示的数据，因此包含数据和行为。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括**数据和业务**。

View（视图）：负责进行模型的展示，一般就是我们见到的用户界面，用户看到的东西。

Controller（控制器）：接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。做的是**调度**的工作。

<font color='red'>标准的MVC中模型能主动推数据给视图进行更新，可以通过观察者模式，在模型上注册视图，当模型更新时自动更新视图。</font>

**Web MVC模型：**

![](https://i.imgur.com/uOCwWYQ.png)

<font color='red'>Web MVC模式下，模型无法主动推数据给视图，如果用户想要视图更新，需要再发送一次请求（即请求-响应模型）。</font>


**Servlet+JSP+JavaBean开发模式:**

![](https://i.imgur.com/cBXfI4P.png)


**Servlet+JSP+JavaBean开发模式的缺点：**

* **Servlet作为控制器的缺点：**
   
  * 控制逻辑可能比较复杂，在Struts2中类似解决的处理机制就称为"动态方法调用"；
 
  * 请求参数到模型的封装比较麻烦；
  
  * 选择下一个视图，严重依赖Servlet API；
  
  * 给视图传输要展示的模型数据，也需要使用Servlet API


* **JavaBean作为模型的缺点：**

JavaBean组件类既负责收集封装数据，又要进行业务逻辑处理，这样可能造成JavaBean组件类很庞大。

![](https://i.imgur.com/BX9X2XU.png)

service domain dao 分层思路：

 domain 主要是域对象，里边有 get 和 set 对象来作用对象内的实例变量

 dao 是持久层，有 find 方法，参数是域对象中的实例变量，找到数据库相应的对象之后，通过 域对象.set() 来实例化域对象，并返回域对象。 有 add 方法，参数是域对象，通过 域对象.get() 来获取域对象的实例变量，将其加入到数据库中。

 service 主要是业务层，里边的实例变量为 dao 对象，完成业务逻辑，在方法中调用 dao 的相关方法，还可以添加事务。

* **视图的缺点：**

现在被绑定在JSP，很难更换视图

---

Web开发模式实战
---
导入开发包（jar包）：

![](https://i.imgur.com/9hXxikm.png)

编写包：

![](https://i.imgur.com/hj9rI46.png)

项目架构图：

![](https://i.imgur.com/vGzyjAR.png)

在src目录(类目录)下面，创建用于保存用户数据的xml文件(DB.xml)。

在WEB-INF目录下创建一个pages目录，pages目录存放系统的一些受保护(不允许用户直接通过URL地址访问)的jsp页面，用户要想访问这些受保护的jsp页面，那么只能通过me.gacl.web.UI这个包里面的Servlet。

**代码编写：**

分层架构的代码也是按照【域模型层(domain)】→【数据访问层(dao、dao.impl)】→【业务处理层(service、service.impl)】→【表现层(web.controller、web.UI、web.filter、web.listener)】→【工具类(util)】→【测试类(junit.test)】的顺序进行编写的。


对于开发接口类，习惯以字母I作类的前缀，这样一眼就看出当前这个类是一个接口。

对于接口的实现类命名方式，习惯以"接口名+impl"形式来命名。


**常看：**
[https://www.cnblogs.com/xdp-gacl/p/3902537.html](https://www.cnblogs.com/xdp-gacl/p/3902537.html)

---

基于注解的方式配置servlet
---

