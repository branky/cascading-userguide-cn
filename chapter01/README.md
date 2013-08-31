
1.关于Cascading
==============

1.1 什么是Cascading
------------------
Cascading是一个在单计算结点或分布式计算集群上用于定义，分享和执行数据处理工作流数据处理API和处理查询计划器。单节点模式中，Cascading的“本地模式（local mode）“能够用于在被部署到集群上之前有效的测试代码和处理本地文件。在分布式计算集群上使用Apache Hadoop平台上时，Cascading增加了一个Hadoop API之上的抽象层，极大的简化了Hadoop应用开发，任务创建和任务调度。

1.2 使用场景
-----------

### 为什么使用Cascading
Cascading开发的目的时为了允许组织能够利用Hadoop快速开发复杂的数据处理应用。以下两类事实中使得对Cascading的作用更加显著：

<b>不断增长的数据量</b> 超过了单计算系统的处理能够.作为应对，开发者也许会采取Apache Hadoop 作为基础计算设施，但是在Hadoop在开发有用的应用并非一件容易的事情。Cascading缓解了这些开发者的负担，并允许他们快速创建，重构，测试和执行复杂的能够在计算机集群上线性增长的应用。

<b>数据中心不断增长的处理复杂度</b> 导致一次性的数据处理应用程序杂乱的蔓延到任何可用的磁盘空间或者CPU.Apache Hadoop 利用它的全局命名空间(Global Namespace)文件系统解决了这个问题，它提供一个独立可靠的存储框架。在此类场景中,Cascading缓解了当开发者为了稳定性和可扩展性而将已经存在的应用程序转化至Hadoop集群上执行时的学习曲线。此外，它还允许开发者创建可重用的库和应用以供分析人员使用，他们可以使用它从Hadoop文件系统中抽取数据。

至从Cascading被创建以来，许多包装了Cascading APIs的领域特定语言（Domain Specific Languages, DSLs）作为查询语言出现，开发者和分析人员可以用来创建专门的为了数据挖掘和探索的查询。这些DSL与Cascading本地模式结合，用户可以在生产级环境执行前，运行快速查询和分析相当大的本地系统数据集。

### 谁是使用者
Cascading用户主要处于三类角色：

<b>应用执行者（Application Executor）</b> 是指在给定集群上运行数据处理应用的一个人（例如：开发则或者分析员）或一个过程（例如：一个cron 任务）。通常情况下，是使用基于Apache Hadoop和Cascading库打包好的Java jar文件在命令行下执行。该应用可能会接受命令行参数来定义具体的执行，而且通常输出的数据集会被从Hadoop文件系统上导出以满足某些特殊的目的。

<b>过程组装者（Process Assembler）</b> 是指将数据处理工作流过程组装至单独应用程序中的人。这项工作一般牵涉到将一些列操作(Operation)串联起来以处理一个或者多个输入数据集来产生一个或多个输出数据集的开发任务。完成这个任务可以单纯使用Java Cascading API 或者使用类似Scala，Clojure，Groovy，JRuby，或者Jython的脚本语言（或某种利用这些语言实现的领域特定语言）。

<b>操作开发者（Operation Developer）</b> 是指写单独方法(Function)或者操作(Operation)（通常使用Java）或重用组件的人，它们在通过数据处理工作流时作用于数据上。一个简单的例子比如一个获取一个字符串然后转化为一个整数的解析器。操作（Operation）就获取输入参数然后返回数据这点而言等价于Java方法。并且它们能够以任何粒度执行，从简单的解析字符串到利用第三方库来处理参数数据的复杂程序。


虽然所有这三种角色都能被同一个开发者胜任，但是因为Cascading提供对这些职责清晰的分类，一些组织可能会选择使用非开发者来运行一些专门的应用或者在一个Hadoop 集群上创建生产过程。


1.3 什么是Apache Hadoop
----------------------
根据Hadoop网站，它是易于开发和运行处理大量数据的应用程序的软件平台（“a software platform that lets one easily write and run applications that process vast amounts of data”）。Hadoop 通过提供一个能够处理大量数据的存储层和一个在集群上利用协调的子数据集并行的运行应用程序的执行层。

这个存储层，叫做<b>Hadoop文件系统（HDFS）</b>,就像一个为了大量大数据文件并行序列化读取而优化后的单一存储卷，这个“大“也许是以千兆字节(GB)或者拍字节(PB)来度量。然而，它也有局限。例如，没有一种有效的方式来随机访问数据。而且Hadoop只支持对出的单一写入。但是这个局限在另一方面使得Hadoop非常高性能可依赖，它允许数据在集群上冗余存储，减少了数据丢失的概率。

这个执行层，叫做<b>MapReduce</b>，基于分而治之的策略管理大量数据集和计算过程。对MapReduce的解释超过了本书的范围，但是它的复杂度和利用它创建真实应用程序的难度是促使Cascading产生的主要推动力。

根据Hadoop文档，它能被配置以三种模式运行：独立模式（standalone mode，即在本机上，对测试和调试非常有用），伪并行模式（“pseudo-distributed”，即在单一机器的模拟集群上），和完整并行模式（“fully-distributed”，即在一个完整的集群上，用于生产目的）。伪并行模式在大多数情况下并没有实质作用，但是将在后文讨论。Cascading 本身能够在本地或者Hadoop平台上运行，无论Hadoop本身是独立模式还是分布式模式。这两个平台的主要区别是当Cascading在本地运行时，它完全不使用Hadoop API并且所有的工作都在内存中进行，所以它非常快，但是结果是它没有在Hadoop平台上的健壮性和可扩展性。

Apache Hadoop是一个开源Apache项目并且可免费获得。它能够从Hadoop网站：[http://hadoop.apache.org/core/](http://hadoop.apache.org/core/) 上下载。


--------
本节译者：[haoch](http://github.com/haoch)

