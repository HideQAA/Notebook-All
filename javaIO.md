Java I/O
---
按照数据流向，分为：**输入流**和**输出流**

按照处理数据单位，分为：**字节流**和**字符流**，字节占8位，字符占16位。

---
### 磁盘操作 File ###



File 类可以用于表示文件和目录的信息，但是它不表示文件的内容。

---
### 字节操作 InputStream/OutputStream ###

**字节输入流：**

![](https://i.imgur.com/NLtLLQu.jpg)

**字节输出流：**

![](https://i.imgur.com/BZXbvwy.jpg)

* **FileInputStream** 字节类型的文件输入流
* **FileOutputStream** 字节类型的文件输出流


---
### 字符操作 Reader/Writer ###

**字符输入流：**

![](https://i.imgur.com/7x8EqSt.jpg)

**字符输出流：**

![](https://i.imgur.com/2EcyPDq.jpg)

* **InputStreamReader** 实现从字节流 **解码** 成字符流
* **OutputStreamWriter** 实现从字符流 **编码** 成字节流
* **FileReader** 字符类型的文件输入流
* **FileWriter** 字符类型的文件输出流

**字节转换为字符流的框架图：**


![](https://images0.cnblogs.com/blog/497634/201310/20234430-bb419718ff01462c8d94fc2ac3e1aeb6.jpg)


编码就是把字符转换为字节，而解码是把字节重新组合成字符。**String 可以看成一个字符序列**

	String str1 = "中文";
	byte[] bytes = str1.getBytes("UTF-8");    //编码（默认为UTF-8）
	String str2 = new String(bytes, "UTF-8");    //解码
	System.out.println(str2);

---
### 对象操作 Serializable ###


序列化就是将一个对象转换成字节序列，方便存储和传输。

* 序列化：ObjectOutputStream.writeObject()
* 反序列化：ObjectInputStream.readObject()

**不会对静态变量进行序列化，因为序列化只是保存对象的状态，静态变量属于类的状态**

序列化的类需要实现 **Serializable** 接口

	public static void main(String[] args) throws IOException, ClassNotFoundException {
	
	    A a1 = new A(123, "abc");
	    String objectFile = "file/a1";
	
	    ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream(objectFile));
	    objectOutputStream.writeObject(a1);
	    objectOutputStream.close();
	
	    ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream(objectFile));
	    A a2 = (A) objectInputStream.readObject();
	    objectInputStream.close();
	    System.out.println(a2);
	}
	
	private static class A implements Serializable {
	
	    private int x;
	    private String y;
	
	    A(int x, String y) {
	        this.x = x;
	        this.y = y;
	    }
	
	    @Override
	    public String toString() {
	        return "x = " + x + "  " + "y = " + y;
	    }
	}

**transient关键字** 可以使一些属性不会被序列化

---
### 网络操作 Socket ###


Java 中的网络支持：

* **InetAddress**：用于表示网络上的硬件资源，即 IP 地址；

没有公有的构造函数，只能通过静态方法来创建实例。

	InetAddress.getByName(String host);
	InetAddress.getByAddress(byte[] address);


* **URL**：统一资源定位符；

可以直接从 URL 中读取字节流数据。

	public static void main(String[] args) throws IOException {
	
	    URL url = new URL("http://www.baidu.com");
	
	    /* 字节流 */
	    InputStream is = url.openStream();
	
	    /* 字符流 */
	    InputStreamReader isr = new InputStreamReader(is, "utf-8");
	
	    /* 提供缓存功能 */
	    BufferedReader br = new BufferedReader(isr);
	
	    String line;
	    while ((line = br.readLine()) != null) {
	        System.out.println(line);
	    }
	
	    br.close();




* **Sockets**：使用 TCP 协议实现网络通信；

![](https://i.imgur.com/9ZTu1Lg.jpg)


* **Datagram**：使用 UDP 协议实现网络通信。

---
### NIO ###



---
### 字节流类别 ###

#### ByteArrayInputStream ####

字节数组输入流，包含一个内部缓冲区（**字节数组**），缓冲区包含从流中读取的字节。InputStream通过read()向外提供接口，供它们来读取字节数据；而ByteArrayInputStream 的内部额外的定义了一个计数器，它被用来跟踪 read() 方法要读取的下一个字节。

#### ByteArrayOutputStream ####

ByteArrayOutputStream 中的数据被写入一个 byte 数组。缓冲区会随着数据的不断写入而自动增长。可使用 toByteArray() 和 toString() 获取数据。

#### PipedInputStream/PipedOutputStream ####

必须将PipedOutputStream和PipedInputStream配套使用，来让多线程可以通过管道进行线程间的通讯。

**大致的流程是**：我们在线程A中向PipedOutputStream中写入数据，这些数据会自动的发送到与PipedOutputStream对应的PipedInputStream中，进而存储在PipedInputStream的缓冲中；此时，线程B通过读取PipedInputStream中的数据。就可以实现，线程A和线程B的通信。

#### ObjectInputStream/ObjectOutputStream ####

对基本数据和对象进行序列化操作支持。创建“文件输出流”对应的ObjectOutputStream对象，该ObjectOutputStream对象能提供对“基本数据或对象”的持久存储；当我们需要读取这些存储的“基本数据或对象”时，可以创建“文件输入流”对应的ObjectInputStream，进而读取出这些“基本数据或对象”。

<font color='red'>只有支持 java.io.Serializable 或 java.io.Externalizable 接口的对象才能被ObjectInputStream/ObjectOutputStream所操作！</font>

#### FileInputStream/FileOutputStream ####

使用FileInputStream从某个文件中获得输入字节，使用FileOutputStream 将数据写入 File 或 FileDescriptor 的输出流。

#### File ####

File 是**“文件”**和**“目录路径名”**的抽象表示形式，直接继承于Object。实现Serializable接口，意味着File对象支持序列化操作。实现Comparable接口，意味着File对象之间可以比较大小。

#### FileDescriptor ####

FileDescriptor 是**“文件描述符”**，可以被用来表示开放文件、开放套接字等。

以FileDescriptor表示文件来说：当FileDescriptor表示某文件时，我们可以通俗的将FileDescriptor看成是该文件。但是，我们不能直接通过FileDescriptor对该文件进行操作；若需要通过FileDescriptor对该文件进行操作，则需要新创建FileDescriptor对应的FileOutputStream，再对文件进行操作。

    FileDescriptor对象
	(01) in  -- 标准输入(键盘)的描述符
	(02) out -- 标准输出(屏幕)的描述符
	(03) err -- 标准错误输出(屏幕)的描述符

创建out对应的**“输出流对象”**，然后通过“输出流”的write()等输出接口就可以将信息输出到屏幕上

	try {
	    FileOutputStream out = new FileOutputStream(FileDescriptor.out);
	    out.write('A');
	    out.close();
	} catch (IOException e) {
	}

#### FilterInputStream ####

封装其它的输入流，并为它们提供额外的功能。常用的子类有BufferedInputStream和DataInputStream

* **BufferedInputStream** 的作用就是为“输入流提供缓冲功能，以及mark()和reset()功能”。
* **DataInputStream** 是用来装饰其它输入流，允许应用程序以与机器无关方式从底层输入流中读取基本 Java 数据类型，可以使用DataOutputStream(数据输出流)写入由DataInputStream(数据输入流)读取的数据。

#### FilterOutputStream ####

封装其它的输出流，并为它们提供额外的功能。主要包括BufferedOutputStream, DataOutputStream和PrintStream。

* **BufferedOutputStream** 的作用就是为“输出流提供缓冲功能”。
* **DataOutputStream** 是用来装饰其它输出流，允许应用程序以与机器无关方式从底层输入流中读写基本 Java 数据类型。
* **PrintStream**  能为其他输出流添加了功能，使它们能够方便地打印各种数据值表示形式。

#### BufferedInputStream ####

通过一个 **内部缓冲区数组** 实现的，在新建某输入流对应的BufferedInputStream后，当我们通过read()读取输入流的数据时，BufferedInputStream会将该输入流的数据分批的填入到缓冲区中。每当缓冲区中的数据被读完之后，输入流会再次填充数据缓冲区；如此反复，直到我们读完输入流数据位置。

#### BufferedOutputStream ####

通过一个 **内部缓冲区数组** 实现的，BufferedOutputStream通过字节数组来缓冲数据，当缓冲区满或者用户调用flush()函数时，它就会将缓冲区的数据写入到输出流中。

#### DataInputStream/DataOutputStream ####

允许应用程序以与机器无关方式从底层输入流中读取基本 Java 数据类型，可以使用DataOutputStream(数据输出流)写入由DataInputStream(数据输入流)读取的数据。

#### PrintStream ####

为其它输出流提供打印各种数据值表示形式，使其它输出流能方便的通过print(), println()或printf()等输出各种格式的数据。**PrintStream 永远不会抛出 IOException**，PrintStream 提供了 自动flush 和 字符集设置功能


---
### 字符流类别 ###

#### CharArrayReader/CharArrayWriter ####

读取字符数组，写入数据字符

#### PipedWriter/PipedReader ####

#### InputStreamReader/OutputStreamWriter ####

创建 **“字符输出流”** 对象时，会指定 **“字节输出流”** 以及 **“字符编码”**





---
[https://www.cnblogs.com/skywang12345/p/io_01.html](https://www.cnblogs.com/skywang12345/p/io_01.html)