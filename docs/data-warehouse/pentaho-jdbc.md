# Pentaho CE通过JDBC 连接 MS SQL Server 2005

先来说点废话，着急的可直接跳到正文！

很多人看到这个标题也许会不屑一顾：这么简单的问题还用得着拿出来说吗？呵呵，我得承认，自己在Windows平台下做开发毫无经验可谈，除了会折腾系统本身，不过那都是很久以前的事了。平时除了偶尔的偶尔看电影用Windows之外，基本上没有碰过，甚至这次连配置Java都得到Google一把。如果不是项目需要，我宁愿世界上再也没有Windows这个词：我真是对它失望透顶！！我实在是想不通，MS这么多大牛，怎么开发出的产品就没有一个好使的呢？先不说功能如何，就连基本的易用性、可用性和UI设计都那么糟糕。你可以仔细回味使用Win系列的细节，有哪一项功能是容易上手的，又有哪一个程序能顺利流畅地运行，有多少好程序是免费提供给你的？花钱也就罢了，可是花的那个冤枉啊……

我对Win平台的程序的印象就是，全是迷宫！你上来要是不摸索个半天，休想找到你想要的东西。就拿MSN来说，我到现在都没弄明白它的组织结构，我指的是UI，所以中国人更偏爱QQ。尤其是那个附带的空间，天哪，进去转了N圈，就是摸不着路。当然，也许你会说我说的有点夸张了，但我实在不愿在这种鸡毛蒜皮地东西上浪费时间，程序不是应该很人性化的吗，这不也是MS引以为豪的特点和追求吗？结果就追求成这样。还有那些庞大复杂到无以复加地开发工具，就更让人迷离了。为什么大家越来越喜欢Apple的产品，无论软件硬件，都是一色的简约明了，用户起码不会被弄晕。其实，最根本的原因在文化和设计理念：MS总是自认为很了解用户，结果是把自己的那一套强塞给了用户。也许美国人使用起MS的产品更方便，可是对于我们来说，实在是费劲。这种感觉也许初学电脑的人更有感触，我现在教家里人使用电脑都快愁死了，一个地方说不到或者少说一遍就不知道怎么弄了。这不能怪人，只是Windows对于他们来说，太像机器了！你按照人的思维去使用肯定要碰壁的！记得当年读高中刚接触计算机时，一年都没明白过来整天说的Windows到底是个什么玩艺！虽然盖茨的大名如雷贯耳，却没有任何感觉。哪像今天，一天说乔布斯辞退了CEO的位置，那种惊讶和失落之情溢于言表！

与其说是乔布斯创造了苹果，不如说苹果成就了乔布斯，是苹果地产品拉近了一代商业天才与普通用户的距离：你喜欢苹果，自然期待它的缔造者，说是爱屋及乌也罢，反正是大家既青睐苹果又崇拜乔布斯。这种良性循环只说明了一个问题：Apple才真正地了解用户。真真是人如其名，Windows注定只能是个小窗口，眼界太窄，永远赶不上自由的苹果。呵呵，说地有点远了，因为昨天乔布斯突然辞职的消息，也因为折腾了两天才整明白的SQL Server。言归正传！

![PentahoBI](http://www.pentaho.com/sites/default/files/val-prop-panel-2-new.jpg)

请注意标题，这里讨论地是MS SQL Server 2005！你如果想寻找其他版本的经验，我不保证能同样适用，因为不(完全)兼容自家产品是MS一贯的特色。

先说明一下我的工作环境：

主要是想为客户创建数据仓库。客户的数据库平台自然是MS SQL Server 2005，所以我就得基于人家的平台来开展工作。但自己使用的是Ubuntu 11.04，所以需要找三方产品来衔接。数据分析及其他后期操作使用开源的[Pentaho商业智能](http://www.pentaho.com/)解决方案。问题来了，少不了一顿枯燥的配置。本来想着将MS SQL Server 2005的数据导出到MySQL下，可是折腾了许久没有成功，无奈只得直接在MS SQL Server 2005上操作。所以直接的问题就是将MS SQL Server 2005连接到Pentaho。网上有不少此类的例子，可说的不是偏了，就是版本不适用，或者言而不尽。还是自己动手折腾吧。

1. 安装Java；安装容易，但版本是有区别的，后面会再次提到。

2. 下载支持MS SQL Server 2005的JDBC驱动: sqljdbc.jar或者jtds.jar。MS自家有，你若Google一把就会很容下到。然后得到一个压缩包:jdbc3.0，你别被这个名字迷惑了，其实里面有两个版本：sqljdbc.jar和sqljdbc4.jar。搞笑吧！其实，如果你安装Java6及以后的版本，那么只能用sqljdbc4.jar。jtds是纯Java编写的开源产品，对多数数据库兼容较好，听说速度也较快、稳定，本文以此为例。

3. Authentication. 设置MS SQL Server 2005服务器端。MS SQL Server 2005是安装在Windows 2003服务器端的，要注意的是，登录MS SQL Server 2005有两种验证方式：Windows Authentication 和SQL Server Authentication，如图Authentication。前者直接用Windows 2003的用户名和密码就可以登录了。要想两者同时都开启，你在安装MS SQL Server 2005时要开启混合模式，当然安装完成以后也可以进入MS SQL Server 2005再更改。使用方法是，先以前者方式进入SQL Server Management Studio, 在左侧一栏中Security下创建新的用户(username)和密码(password)，并赋予相应的权限，下次就可以用第二种方式登录了。这点很重要，因为JDBC连接MS SQL Server 2005要使用SQL Server Authentication方式。打开SQL Server Configuration Manager, 一般(有的MS SQL Server 2005安装后只存在一种)会有两个JDBC连接模式：MSExpress和MSSQLServer，如图NewUser。两者都可以连接，但后面连接的时候要分别针对两者做些修改。

4. 配置Pentaho Community Edition (PCE)。3.0以后的PCE主目录下会有两个目录，administration-console和biserver-ce。前者是用户控制台(pac)，可以设置用户、数据源等项目，后者就是PCE的核心组件了。先将jtds分别放入administration-console/jdbc/和biserver-ce/tomcat/webapps/pentaho/WEB-INF/lin/下。然后一次启动start-pentaho.sh和start-pac.sh，也就是先开始Pentaho主服务，再开启PAC。否则的话会出错。

5. 现在可以登录PAC添加新的数据源连接了，就是SQL Server数据库。通过http://localhost:8099/进入PAC后(默认用户和密码分别是：admin和password)，选择Administrator标签->Database Connection标签下添加新的数据库源。第3步只要上面驱动放的位置正确就会自动出现`net.sourceforge.jtds.jdbc.Driver`，如果你的没出现就要检查驱动是否正确放置了。然后填入上面为SQL Server Authentication创建的用户和密码。URL最重要，应该是：`jdbc:jtds:sqlserver://localhost:1433;instance=MSSQLSERVER;DatabaseName=test`。其中localhost是你数据库所在主机域名，这里因为是本地所以是localhost，或者127.0.0.1也可以。1433是数据库默认端口，一般不需更改。instance就是告诉Pentaho使用何种方式连接SQL Server，这里选用MSSQLSERVER方式，可以根据你的偏好选择(MSExpress)。最后是数据库名。保存后可以点击Test按钮进行连接测试，如果你看见下图Successful，那么恭喜你，连接成功了。

6. 访问http://localhost:8080(随便选择一个用户都可登录)，会进入Pentaho CE的主界面，这里是后期数据分析和展现的地方。如果以上设置正确的话，你在这里应该能看见刚才新添加的数据库。方法是选择File->Manage->Data Sources，弹出数据源管理窗口。点击+添加弹出数据源添加向导，然后按上图所示步骤分别设置。Source Type选择Database，Connection里面你就能看到你前面添加的数据库，这里是SQLSERVER。右边的文本框可以填写SQL语句对数据库进行查询，结果可以快速预览(Preview)。

到这里，整个过程就结束了，后面就可以正常对 SQL Server 2005中的数据库进行操作了。下次介绍Pentaho CE的具体使用和功能。

PS: 原文发表于[新浪博客](http://blog.sina.com.cn/s/blog_67582af90100sm0f.html)，2011年8月26.