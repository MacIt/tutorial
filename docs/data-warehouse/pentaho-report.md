# Pentaho教程一：基于Metadata创建report并发布到Pentaho BI Server#

众所周知，Pentaho BI商业智能解决方案提供了两种reporting工具，即基于Pentaho BI Server的Ad Hoc Query and reporting Tool(WAQR)和更高级的Pentaho Report Designer(PRD)。两者均工作在由Pentaho Data Integration(PDI)创建的数据仓库之上(废话，当然，如果你不使用Pentaho也有其他方案)。区别是，WAQR只能通过JDBC连接、查询数据仓库，而PRD除了普通的JDBC连接方式之外，还有Pentaho特有的metadata连接查询方式(还有OLAP、XML等方式，在此不予讨论)！WAQR方式相对简单明了，登录Pentaho BI Server之后，创建新的数据仓库连接便可创建report了。今天要讨论的是PRD，尤其是其metadata连接。

## 利用PRD创建基于Metadata的report

如题，这种方式创建report的基础是已经创建好的metadata文件，以.prpt结尾。本文假设读者已经创建好了.prpt文件，并导出为.xmI文件，以供创建report使用。

1. 打开PRD，新建report，并保存;
2. 为空的report中的Page Header、Report Header、Report Footer、Page Footer等添加相应元素。重点在Group Header和Details;
3. 新建Metadata数据连接。打开Metadata数据源编辑器，添加.xmI文件为数据源，并提供创建.prpt文件时所使用的域名(domain);
![MetadataSourceEditor](https://github.com/MacIt/tutorial/blob/master/img/MetadataSourceEditor.png)

	新建查询(Query)，打开查询编辑器。加入想要查询的列、排序所用的列和约束条件。这里需要注意的是约束条件，因为通常report会加入一些查询参数。为了设置参数查询，需要首先创建参数，通常是通过JDBC获取数据仓库中数据表的某一列作为参数值，然后在查询编辑器中的条件(Condition)栏设置参数查询。这里注意引用参数的语法:用花括号将参数名包围起来，即{parameter}。然后选择对应的比较操作符，比如对于String有Contains、exactly matches和in等，如图2;
	
![QueryEditor](https://github.com/MacIt/tutorial/blob/master/img/QueryEditor.png)

4. 上一步完成后，会得到一些返回的列名，将这些列名按需拖入report的Details部分，一个report的主体部分就完成了;
5. 如果需要，可以添加Group区域;
6. 发布report到Pentaho BI Server，如图3。前提是已经开启Pentaho BI Server;

![PublishReport](https://github.com/MacIt/tutorial/blob/master/img/PublishReport.png)
 
7. 为发布report设置发布密码，可在biserver-ce/pentaho-solutions/system/publisher_config.xml中设置。为该report创建一个目录，**目录名必须与metadata文件域名一致**，比如tth;
8. 通过Pentaho Metadata Editor(PME)直接将.xmI文件发布到Pentaho BI Server下同一目录，即tth。
9. 最后，登录到Pentaho BI Server，刷新metadata和prpt文件，双击prpt即可看见生成的report。

**特别注意：**

如果report中的参数值不唯一，即有多个values，比较参数比较符应该选择**exactly matches**而不是in！这点与利用PRD创建基于JDBC的report是参数比较不同，后者对于多值采用in来比较。虽然对于PRD来说exactly matches和in使用效果相同，但一旦发布到Pentaho BI Server就会出现问题，如果你使用in，那么当你为一个参数选择多个值时就会出现”Report validation failed.”的错误！不知道这算不算是该版本Pentaho BI Server的bug。

对于上述这个问题，我曾在Pentaho forum发帖提问(原帖在这里[Multi-value report based on metadata failed to load on BI server](http://forums.pentaho.com/showthread.php?93280-Multi-value-report-based-on-metadata-failed-to-load-on-BI-server&p=278644#post278644))，但没有得到回答，还是自己花了很长时间才摸索出来，希望对后来者有点帮助。