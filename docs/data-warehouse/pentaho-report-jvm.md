# Pentaho教程二: Pentaho BI Server处理多值参数report及JVM设定

在实际使用中，一个报表可能需要让用户指定一些参数来筛选更加具体的report。这在Report designer中基本不是问题。当你想把report发布到Pentaho BI Server中情况可能会有些不同。比如这些参数可能会有很多值，比如说成千上万个，而且允许用户多选！而且，每个report包含的数据信息量是不同的，有些情况可能会很大，这也要求对Pentaho BI Server稍加配置以期正常运行。

默认设置下，Pentaho BI Server为report预留的memory是比较小的。这样也是出于节省服务器资源考虑的。众所周知，Pentaho BI Server是基于Java的，运行于JVM内，而Pentaho内的Application是运行于Tomcat平台上的， Tomcat也有自己的设置。所以，一个包含参数的report能否正常运行要看JVM和Tomcat的设置是否正确。下面从这两方面来分析如何设置。

## 设置JVM
在biserver-ce/start-pentaho.sh中你会找到下面这行代码：

export CATALINA_OPTS="-Xms256m -Xmx768m -XX:MaxPermSize=256m -Dsun.rmi.dgc.client.gcInterval=3600000 -Dsun.rmi.dgc.server.gcInterval=3600000"

下面咱们从头来说。
JVM分32bit和64bit两个版本。对于32位操作系统，JVM不能大于RAM中的4GB！4GB之外的OS自有用处。比如Windows需要2GB供其内核使用，剩余的才能分配给JVM。通常来说，JVM heap不会大于1.2-1.6GB。那么什么是JVM heap？下面将慢慢分析。


上图是个示例，用来描述Java进程堆(Java process heap)。
如图所示，Java Heap在内存中是一块连续的区域，这里存放的是所有的对象数据(Object Data)，包括类的实例(instance of class)，原函数(primitive)，和引用(references)。

我们可以通过一下选项来配置Java Heap：

- -Xmx:Java heap的最大值
- -Xms:Java heap的最小值。如果在32位的机器上设置-Xms=1.8GB，那么其他程序就很难运行了。
- -Xmn:新生对象(young generation，暂且这么叫吧)的堆大小，最佳设置33%。Young Generation是指所有生命周期较短的对象。这些对象位于heap中一个特殊的位置(eden)。Java垃圾回收器(Garbage collector)会经常光顾eden。所有新创建的对象都在eden，如果一个新对象在GC清扫2-3次后仍能存活，那么就会转变成老对象(Old Generation)，并转移到tenured。
* -XX:NewRatio:Young Generation与Old Generation的大小比例。
* -XX:NewSize:Young Generation在JVM中的初始大小。如果已经指定了-XX:NewRatio那么此项会被自动计算。
* -XX:MaxNewSize:Young Generation所能增长的最大空间。如果不指定此项，默认是无限的。
* -XX:SurvivorRatio:指定tenuredhe和eden的比例。
* -XX:MinHeapFreeRatio:缺省是40%。当-Xmx和-Xms无效时，JVM总依靠此项为heap分配空闲内存的40%。
* -XX:MaxHeapFreeRatio:缺省是70%。同上，但是是为了避免内存的浪费。

到现在为止，我们忽略了一个问题，那就是多线程！在多线程应用中，对象是在同一时间被创建的，所以这些线程很可能对同一heap同时进行写操作。为了避免这个问题，JVM允许每个线程拥有自己的一块eden！

接下来就是剩余的内存分配了。

* Permanent Space:这是JVM中第三部分空间，所有的类(classes)和方法(methods)存放于此。
* -XX:PermSize:Permanent Space的初始值。
* -XX:MaxPermSize:Permanent Space的最大值。
* Code Generation:字节码(byte code)被转换为原代码(native code)。
* Socket Buffer:包括2缓存(Buffer)：receive/send。
Thread Stacks:线程栈，每个线程有自己的栈(stack)，这样使得方法是线程安全的。
* -Xss:用来改变线程栈的大小。
* Direct memory space:此项让Java开发人员将一定大小的内存映射到Java对象栈(Java Object Heap)外部。
* JNI Code:很少使用。
* Garbage Collection:垃圾回收器也拥有自己的内存空间以存储线程和GC的相关信息。GC的大部分工作在tenured部分用完时才会执行。

现在我们对JVM的相关设置有了大概了解，虽然我们不需要设置以上所有的选项，但是了解这些有利于更好的为自己的程序设置内存。通常来讲，-Xmx不超过物理内存的1/2，而-Xms不要低于-Xmx的1/2。不过对于不同的生产环境，需据实际情况自行调整并测试以找到最佳设置。此部分译自[Get started with java JVM memory (heap, stack, -xss -xms -xmx -xmn...)](http://avricot.com/blog/index.php?post/2010/05/03/Get-started-with-java-JVM-memory-(heap%2C-stack%2C-xss-xms-xmx-xmn...))，更多JVM设置参考[inside-the-jvm-memory-management-and-troubleshooting](http://www.slideshare.net/gengmao/inside-the-jvm-memory-management-and-troubleshooting)。

## 设置Tomcat

在Tomcat的安装目录找到tomcat/conf/server.xml。其中有一段：

```
<Connector URIEncoding="UTF-8" port="8080"
protocol="HTTP/1.1" connectionTimeout="20000"
redirectPort="8443" />
```

这是一个Http连接器，负责Http的请求(Request)和响应(Response)。这里，我们可以更改Http请求/响应报头的大小。方法就是增加一项

`"maxHttpHeaderSize="16448""`。[`maxHttpHeaderSize`](http://tomcat.apache.org/tomcat-5.5-doc/config/http.html#HTTP/1.1_and_HTTP/1.0_Support)指定Http请求/响应报头的最大值，以字节(byte)指定，默认值是4096(4KB)。

以上两项设置合理后，就可以为report选择适当数量的参数值，并且report所能容纳的行数也会增加。