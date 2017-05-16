# Data Warehouse及Pentaho BI入门书籍及资源

创建一个数据仓库之前，有几件事情必须弄明白。

1. 数据仓库的原理。什么是数据仓库？它与数据库有什么不同？有哪些基本的模型可以选用？

2. 数据仓库软件。比如数据仓库建模工具，ETL工具，报表制作软件，报表管理软件等。

本文将推荐一些经典的入门书籍和资源，希望对后来者有所帮助。

## 数据仓库原理相关的书籍
数据仓库相关的书籍多如牛毛，但大多都是基于一些比较经典的书籍。作者认为如果想对数据仓库有个透彻的了解，直接看这些经典著作比较恰当。下面以出版年代为顺序逐一介绍。欢迎增补。

1. (2002 Wiley)The Data Warehouse Toolkit The Complete Guide to Dimensional Modeling 2nd

	从书名就可以看出，本书围绕维度建模(Dimensional Modeling)的思想来展开，开头介绍了数据仓库的目标，组成部分，维度建模的优点等。后面的章节则按照不同领域的数据仓库建模一一举例。对于初学者来书，没有比例子更直接的了吧？！
1. (2003)Mastering Data Warehouse Design Relational and Dimensional Techniques

	本书相对比较全面，从数据仓库的概念，到如何创建数据仓库及其中一些必要的方面和细节，均按照顺序展开。从头到尾通读自然会获得好的效果，但如果你心急，完成前几章入门后，也可以直接跳至跟你的主题相关的部分。
3. (2004)The Data Warehouse ETL Toolkit

	为了获得高质量的数据，适当的ETL方法是少不了的。毕竟数据仓库的良好运行直接依靠ETL(Extract-Transform-Load)系统。本书就是从ETL切入数据仓库。
5. (2008)The Data Warehouse Lifecycle Toolkit 2nd

	本书详细讨论了数据仓库每个细节，正如标题所说，它的生命周期。对于一个数据仓库团队来说本书可能更适合。因为它从项目管理开始讲起，需求分析，数据仓库技术架构，维度模型的概念和设计方法，数据库设计及性能因素，ETL，商业智能等高级话题等，面面俱全，而且均按照顺序展开。
7. (2010)Star Schema The Complete Reference

	本书主要围绕数据仓库中比较流行的星型模型(Star Schema)展开，详细介绍了星型模型的基础，多星，维表设计，事实表设计，及性能方面的细节。

## 建模工具
有了上面的理论基础，你可以尝试为你的领域设计数据仓库模型了。当然你可以在纸上画出模型，然后再用传统的数据库创建数据表。但有了工具你的效率会更高。本文推荐SQL Power Architect社区版。对于数据库建模来说，SQL Power Architect拥有众多特色。比如基于Java，开源免费跨平台，支持众多的流行数据库，数据库模型比较，反向工程，支持拖拽操作等等。

## Pentaho BI Platform资料
Pentaho BI Suit是一个开源的跨平台的，比较全面的数据仓库平台。它的社区版(Community Edition)可以免费使用并有论坛支持。它有几个重要组成部分：

Pentaho BI Server， Pentaho Data Integration Community Edition (PDI CE)（又叫Kettle），Pentaho Reporting Community Edition (CE)，Pentaho Analysis Services Community Edition(又叫Mondrian)，Pentaho Data Mining Community Edition (CE) also known as Weka，Pentaho Metadata Editor(PME)。

* Pentaho BI Server是个管理平台，数据仓库的报表及OLAP的Cube都可以放到这里来管理。这是数据仓库管理员和终端用户共同需要的地方。管理员可以在这里只做报表或者将制作好的报表发布到这里，便于管理，用户可以通过这里访问并下载它们。

* Pentaho Data Integration是ETL工具，你可以通过拖拽操作来定义ETL的每一个步骤，只涉及少量的SQL和Javascript代码。所以入门很低。创建好的Transformation和Jobs可以方便的运行。

* Pentaho Reporting Community Edition (CE)包括了Pentaho Report Designer(PRD), Pentaho Reporting Engine, Pentaho Reporting SDK。有了PRD你可以方便的涉及报表(Reports)，当然背后的支持是Pentaho Reporting Engine，你也可以将其放到自己的Application里面来使用。其实Pentaho BI Server就已经将其包含了，这就是为什么在Pentaho BI Server中也可以创建报表的原因了。

* Weka是一个比较出名的开源数据挖掘软件，提供了一些聚类，分类，关联规则挖掘等数据挖掘算法。而且是图形界面的。Weka可以很容易的被集成到Pentaho中。

* Pentaho Metadata Editor为不懂SQL的报表制作人员提供了便利。通常，制作报表少不了编写SQL代码来访问数据库，但是有了PME这就不是必须的了。PME是位于数据模型和报表之间的一个特殊层，你可以通过PME的支持，以图形界面的方式找到并组合你需要的Table或者数据库，而不必触碰SQL代码。

当然除了上述这些，Pentaho还支持Big Data，也就是大数据分析，还有其他高级功能，有待你的发掘。

说了半天Pentaho的好处，如何入门呢？

* 最好的书籍便是(2009 Sep)Pentaho Solutions - Business Intelligence and Data Warehousing with Pentaho and MySQL。
	
	这本书叫你怎么用Pentaho来开发数据仓库，还对必须要的数据仓库知识做了介绍，一些高级应用也涉及了一些。如果你觉得数据仓库的知识太少，请参考第一部分列举的书籍。

* (2010 April)Pentaho 3.2 Data Integration Beginner’s Guide，(2010)Pentaho Kettle Solutions Building Open Source ETL Solutions with Pentaho Data Integration(Epub格式)和(2011 Jun Packtpub)Pentaho Data Integration 4 Cookbook

	是专门介绍PDI的，各种功能，按钮，组建如果使用，都在这几本书里了。

* (2009 Sep Packtpub)Pentaho Reporting 3.5 for Java Developers 

	是教你如果利用PRD设计报表的，你可以利用其自带的报表模版，也可以自己制作，制作基于JDBC或者Metadata的报表，发布到Pentaho BI Server等等，总之所有相关的知识都可以在这本书里找到。
	
有了上面的书籍和工具相信你可以快速的建立数据仓库。如果不幸，你确实遇到了一些棘手或者特别的问题，不要紧，可以Google一下，或者去Pentaho社区论坛寻找答案或求助。你一定不是第一个遇到此类问题的人！还有就是Pentaho自己也提供了很多文档，比如其[Wiki](http://wiki.pentaho.com/display/COM/Community+Wiki+Home)和[社区](http://community.pentaho.com/)等。

PS: 本文最早发表于2012年9月9日。