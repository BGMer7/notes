大数据本身是一个很宽泛的概念，Hadoop生态圈（或者泛生态圈）基本上都是为了处理超过单机尺度的数据处理而生的。

## 前言

### Hadoop诞生

最早Doug Cutting（后面被称为Hadoop之父）领导创立了Apache的项目Lucene，然后Lucene又衍生出子项目Nutch，Nutch又衍生了子项目Hadoop。Lucene是一个功能全面的文本搜索和查询库，Nutch目标就是要试图以Lucene为核心建立一个完整的搜索引擎，并且能达到提到Google商业搜索引擎的目标。网络搜索引擎和基本文档搜索区别就在规模上，Lucene目标是索引数百万文档，而Nutch应该能处理数十亿的网页。因此Nutch就面临了一个极大的挑战，即在Nutch中建立一个层，来负责分布式处理、冗余、故障恢复及负载均衡等等一系列问题。

曙光的到来，2004年，Google发表了两篇论文来论述Google文件系统（GFS）和MapReduce框架，并且使用了这两项技术来拓展自己的搜索系统，于是Doug Cutting看到了这两篇论文的价值并带领他的团队便实现了这个框架，并将Nutch移植上去，于是Nutch的可扩展性得到极大的提高。这个新的框架就是最初的Hadoop。2005年，Hadoop作为Lucene的子项目Nutch的一部分正式引入Apache基金会。

在2006年1月，雅虎雇佣Doug Cutting，并让他和一个专门的团队来一起改进Hadoop，并将其作为一个开源项目继续发展。



### Hadoop生态圈

我们通常说到的Hadoop包括两部分，一是Hadoop核心技术（或者说狭义上的Hadoop），对应为Apache开源社区的一个项目，主要包括三部分内容：HDFS，MapReduce，YARN。其中hdfs用来存储海量数据，MapReduce用来对海量数据进行计算，yarn是一个通用的资源调度框架（是在Hadoop2.0中产生的）。

另一部分指广义的，广义上指一个生态圈，泛指大数据技术相关的开源组件或产品，如HBase、Hive、spark、Pig、ZooKeeper、Kafka、Flume、phoenix、Sqoop等。

<img src="https://media.geeksforgeeks.org/wp-content/cdn-uploads/HadoopEcosystem-min.png" alt="https://media.geeksforgeeks.org/wp-content/cdn-uploads/HadoopEcosystem-min.png" style="zoom: 80%;" />

<img src="https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2016/10/HADOOP-ECOSYSTEM-Edureka.png" alt="https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2016/10/HADOOP-ECOSYSTEM-Edureka.png" style="zoom:80%;" />

[Hadoop Ecosystem | Hadoop Tools for Crunching Big Data | Edureka](https://www.edureka.co/blog/hadoop-ecosystem)



## 一些术语

### ETL

ETL是一种数据仓库技术，Extract（数据抽取）、Transform（数据清洗）、Load（数据加载）。虽然是一种数据仓库技术，但是不仅仅局限与数据仓库的应用。

<img src="https://databricks.com/wp-content/uploads/2021/05/ETL-Process.jpg" alt="img" style="zoom: 33%;" />

数据抽取是从数据源中抽取数据的过程。实际应用中，数据源较多采用的是关系数据库。

1. 全量抽取

   全量抽取类似于数据迁移或者数据复制，将数据从数据源中原封不动地抽取出来，转换为自己的ETL工具可以识别的数据格式。（因为逻辑较少，所以全量抽取比较简单。）

2. 增量抽取

   增量抽取之抽取自从上次抽取以来，数据库中新增或者修改的数据。在实际生产中，增量抽取要比全量抽取更加使用广泛，关键是如和获取增量信息。



数据抽取之后，存放在数据仓库中的数据不一定都是可用数据，可能存储在脏数据、空数据、错数据等问题，需要对于抽取的数据进行清洗转换和加工。数据清洗这一步可以区分为使用数据库存储过程转换和使用高级语言转换。

>使用SQL开发存储过程完成转换作业是很多银行常用的方法。
>
>优点在于开发简单、能支持绝大部分转换场景；缺点是受制于单一数据库的性能瓶颈，无法做到横向扩展。

>使用高级语言开发主要使用Cpp/C/Java对于抽取的数据进行处理。
>
>优点是运行效率高，可以横向扩展服务器的数量，来提升系统作业的转换能力，缺点是开发比较复杂对于大数据的支持不如数据库。

[ETL 工作流程 - 简书 (jianshu.com)](https://www.jianshu.com/p/d43a517903ca)

### 结构化数据、非结构化数据、半结构化数据

结构化的数据是指可以使用关系型数据库表示和存储，表现为二维形式的数据。一般特点是：数据以行为单位，一行数据表示一个实体的信息，每一行数据的属性是相同的。

所以，结构化的数据的存储和排列是很有规律的，这对查询和修改等操作很有帮助。

但是，它的扩展性不好。比如，如果字段不固定，利用关系型数据库也是比较困难的，有人会说，需要的时候加个字段就可以了，这样的方法也不是不可以，但在实际运用中每次都进行反复的表结构变更是非常痛苦的，这也容易导致后台接口从数据库取数据出错。你也可以预先设定大量的预备字段，但这样的话，时间一长很容易弄不清除字段和数据的对应状态，即哪个字段保存有哪些数据。



半结构化数据是结构化数据的一种形式，它并不符合关系型数据库或其他数据表的形式关联起来的数据模型结构，但包含相关标记，用来分隔语义元素以及对记录和字段进行分层。因此，它也被称为自描述的结构。

半结构化数据，属于同一类实体可以有不同的属性，即使他们被组合在一起，这些属性的顺序并不重要。

常见的半结构数据有XML和JSON。



非结构化数据是数据结构不规则或不完整，没有预定义的数据模型，不方便用数据库二维逻辑表来表现的数据。包括所有格式的办公文档、文本、图片、各类报表、图像和音频/视频信息等等。

非结构化数据其格式非常多样，标准也是多样性的，而且在技术上非结构化信息比结构化信息更难标准化和理解。所以存储、检索、发布以及利用需要更加智能化的IT技术，比如海量存储、智能检索、知识挖掘、内容保护、信息的增值开发利用等。



Structured vs Unstructured Data: 5 Key Differences

1. Structured data is clearly defined and searchable types of  data, while unstructured data is usually stored in its native format. 
2. Structured data is quantitative, while unstructured data is qualitative. // 一个使用数量描述，一个使用性质描述。
3. Structured data is often stored in [data warehouses](https://www.xplenty.com/blog/what-is-a-data-warehouse/), while unstructured data is stored in [data lakes](https://www.xplenty.com/glossary/what-is-data-lake/).
4. Structured data is easy to search and analyze, while unstructured data requires more work to process and understand. 
5. Structured data exists in predefined formats, while unstructured data is in a variety of formats. 



> Structured Data:
>
> The term structured data refers to data that resides in a fixed field within a file or record. Structured data is typically stored in  a [relational database](https://www.xplenty.com/blog/which-database/) (RDBMS). It can consist of numbers and text, and sourcing can happen  automatically or manually, as long as it's within an RDBMS structure. It depends on the creation of a [data model](https://www.xplenty.com/glossary/what-is-data-modeling/), defining what types of data to include and how to store and process it. 
>
> The programming language used for structured data is SQL (Structured Query Language). Developed by IBM in the 1970s, SQL handles relational databases. Typical examples of structured data are names, addresses, credit card numbers, geolocation, and so on.

>Unstructured data is more or less all the data that is not  structured. Even though unstructured data may have a native, internal  structure, it's not structured in a predefined way. There is no data  model; the data is stored in its native format. 
>
>Typical examples of unstructured data are rich media, text, social media activity, surveillance imagery, and so on. 
>
>The amount of unstructured data is much larger than that of structured data. Unstructured data makes up a [whopping 80% or more](https://www.datamation.com/big-data/structured-vs-unstructured-data.html?utm_source=xp&utm_medium=blog&utm_campaign=content) of all enterprise data, and the percentage keeps growing. This means that  companies not taking unstructured data into account are missing out on a lot of valuable business intelligence.

>Semistructured data is a third category that falls somewhere  between the other two. It's a type of structured data that does not fit  into the formal structure of a relational database. But while not  matching the description of structured data entirely, it still employs  tagging systems or other markers, separating different elements and  enabling search. Sometimes, this is referred to as data with a [self-describing](https://en.wikipedia.org/wiki/Self-describing?utm_source=xp&utm_medium=blog&utm_campaign=content) structure.
>
>A typical example of semistructured data is smartphone photos.  Every photo taken with a smartphone contains unstructured image content  as well as the tagged time, location, and other identifiable (and  structured) information. Semi-structured data formats include JSON, CSV, and XML file types.



## HDFS

HDFS=Hadoop Distributed File System

Hadoop --- 一个分布式系统基础架构，由Apache基金会开发。用户可以在不了解分布式底层细节的情况下，开发分布式程序。充分利用集群的威力高速运算和存储。

Distributed --- 分布式计算是利用互联网上的计算机的 CPU 的共同处理能力来解决大型计算问题的一种计算科学。

File System --- 文件系统是操作系统用于明确磁盘或分区上的文件的方法和数据结构；即在磁盘上组织文件的方法。也指用于存储文件的磁盘或分区，或文件系统种类。

HDFS在Hadoop系统中是最基础的一个部分，因为涉及到数据的存储，MapReduce等计算模型都依赖存储在HDFS中的数据，HDFS是一个分布式文件系统，**以流式数据访问模式存储超大文件，将数据分块存储到一个商业硬件集群的不同机器上**。

> 涉及到几个概念的问题
>
> 1. **超大文件**：目前的Hadoop集群能存储几百PB和TB的数据。
> 2. **流式数据访问**：HDFS的访问，**一次写入多次读取**。
> 3. **商业硬件**：HDFS集群的设备不需要很高的硬件性能，只需要一些日常的普通硬件即可。正因为如此，hdfs节点故障的可能性还是很高的，所以**必须要有机制来处理这种单点故障**，保证数据的可靠。
> 4. **不支持低延时**：HDFS关心的是数据吞吐量而不是数据的时延。
> 5. **单用户写入，不支持修改**：hdfs的数据以读为主，只支持单个写入者，并且写操作总是以添加的形式在文末追加，不支持在任意位置进行修改。



### Assumptions and Goals

from [HDFS Architecture Guide (apache.org)](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)

HDFS相较于其他的DFS系统的优势在于，**高容错率，能在大量低成本的硬件上运行**。

1. Hardware Failure 硬件失灵

   硬件失灵是常态，因此**HDFS需要快速检测、快速并且自动恢复**，这是HDFS的一个核心目标。

2. Streaming Data Access 流数据访问

   The emphasis is on high throughput of data access rather than low latency of data access.

   重点是数据访问的高吞吐而不是数据访问的低延迟。

3. Large Data Sets 大数据集

   It should provide high aggregate data bandwidth and scale to hundreds of nodes in a single cluster.

   HDFS需要提供一个足以处理单个集群中数以百万计的节点的高的数据带宽。

4. Simple Coherency Model 一致性模型

   HDFS applications need a write-once-read-many access model for files.

   一次写入，多次读出。

### Data Block

[【HDFS】一、HDFS简介及基本概念 - gzshan - 博客园 (cnblogs.com)](https://www.cnblogs.com/gzshan/p/10981007.html)

HDFS同样也有数据块的概念，默认一个块（block）的大小为128MB（HDFS的块这么大，主要是想最小化寻址开销），要在HDFS中存储的文件可以划分为多个分块，每个分块可以成为一个独立的存储单元。与本地磁盘不同的是，HDFS中小于一个块大小的文件并不会占据整个HDFS数据块。

使用数据块存储的好处：

1. 一个文件的大小可以大于任意一个网络中磁盘的容量，文件的块，可以利用集群中的任意一个数据块存储。

2. 使用抽象的块，而不是整个文件作为存储单元，可以简化存储管理，使得文件的元数据可以单独管理。

3. 冗余备份。数据块非常适合数据备份，进而可以提供数据容错能力和提高可用性。

   每个块可以有多个备份，默认为三个，分别保存到各自相互独立的机器上去，这样就可以保证单点故障不会丢失。



### NameNode and DataNode

HDFS has a master/slave architecture. NameNode是一个管理节点，DataNode是一个工作节点。理解了这两种节点对于HDFS的机制非常重要。

NameNode是一个管理节点，负责整个文件系统的命名空间，并且维护着文件系统树和整棵树内所有的文件和目录，这些信息以两个文件的形式（命名空间镜像文件和编辑日志文件）永久存储在NameNode的本地磁盘上。除此之外，同时，namenode也记录每个文件中各个块所在的数据节点信息，但是不永久存储块的位置信息，因为块的信息可以在系统启动时重新构建。

DataNode作为文件系统的工作节点，根据需要存储并检索数据块，定期向NameNode发送他们所存储的块的列表。

![img](https://img2018.cnblogs.com/blog/1608161/201907/1608161-20190709150608993-1616801523.png)

NameNode作为管理节点，地位非同寻常，一旦NameNode宕机，那么所有文件都会丢失。因为NameNode是唯一存储了元数据、文件和数据与数据块之间关系的节点，所有的文件信息都保存在这里，NameNode毁坏后















## YARN









## MapReduce







## Spark







##  Pig









## Hive







## HBase







## Mahout







## Spark







## Drill





## ZooKeeper





## Oozie









## Flume





## Sqoop







## Solr





## Lucene





	## Ambari



