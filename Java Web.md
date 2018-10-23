Java Web入门
---
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

