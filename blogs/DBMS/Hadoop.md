---
layout: page
permalink: /blogs/DBMS/Hadoop/index.html
title: Hadoop
---

# Hadoop

## 一. HDFS

### **1） Introduction**

> HDFS（Hadoop Distributed File System）是Hadoop项目的核心子项目，是分布式计算中数据存储管理的基础，是基于流数据模式访问和处理超大文件的需求而开发的，可以运行于廉价的商用服务器上。它所具有的<span style="color: red;">**高容错、高可靠性、高可扩展性、高获得性、高吞吐率**</span>等特征为海量数据提供了不怕故障的存储，为超大数据集（Large Data Set）的应用处理带来了很多便利。HDFS 源于 Google 在2003年10月份发表的GFS（Google File System） 论文。 它其实就是 GFS（Google File System） 的一个克隆版本。

**PROS**:

- **高容错性：**数据自动保存多个副本，通过增加副本的形式，提高容错性。某一副本丢失后，它可以自动恢复，这是由HDFS内部机制实现的。
- **适合批量处理：**他是通过移动计算而不是移动数据。它会把数据位置暴露给计算框架。
- **适合大数据处理：**能够处理GB、TB、甚至PB级别的数据。能够处理百万规模以上的文件数量。能够处理10K节点的规模。
- **流式文件访问：**一次写入，多次读取。文件一旦写入不能修改，只能append。keep the data consistency.
- **可构建在廉价机器上：**它通过多副本机制，提高可靠性。它提供了容错和恢复机制。如果一个副本丢失，它可以通过其他副本来恢复。

**CONS:**

- **低延时数据访问：**HDFS适合高吞吐率的场景，即在某一时间写入大量数据。但他在低延时的情况下表现并不理想，如果要它在毫秒级以内读取数据，他是很难做到的。
- **小文件存储：**存储大量小文件（i.e. file smaller than HDFS Block - 128M）的话，会占用NameNode大量的内存来存储文件、目录和块信息。然而NameNode的内存总是有限的，小文件存储的寻道时间会超过读取时间，这就违反了HDFS的设计目标（处理超大文件）。
- **并发写入、文件随机修改：**一个文件只能有一个写，不允许多个线程同时写。仅支持数据append，不支持文件的random modification.

### **2） HDFS组成**

![HDFS架构](https://drunkcat69.github.io/images/DBMS/Hadoop/HDFS架构.png)

> HDFS 采用Master/Slave的架构来存储数据，这种架构主要由四个部分组成，分别为HDFS Client、NameNode、DataNode和Secondary NameNode。下面我们分别介绍这四个组成部分 ：

**1、Client**

- 文件切分。文件上传HDFS的时候，Client将文件切分成 一个一个的 Block，然后储存起来。
- 与NameNode交互，获取文件的位置信息。
- 与DataNode交互，read/write data.
- 提供一些命令来管理HDFS，比如启动/关闭HDFS.
- 通过一些命令来visit HDFS.

**2、NameNode（NN）**

> NameNode就是master，他是一个主管、管理者。

**3、DataNode（DN）**

> DataNode就是Slave。NameNode 下达命令，DataNode 执行实际的操作。

- 存储实际的数据块。
- 执行数据块的R/W操作。

**4、Secondary NameNode（2NN）**

> Secondary NameNode并非 NameNode 的备份。当NameNode 挂掉的时候，它并不能马上替换 NameNode 并提供服务。

- Secondary NameNode仅仅是NameNode的一个工具，这个工具帮助NameNode管理元数据信息。
- 定期合并 fsimage和fsedits，并推送给NameNode。
- 在紧急情况下，可辅助恢复 NameNode。

### **3） HDFS具体工作原理**

**1、Fslmage和EditLog**

- FsImage负责维护文件系统树和树中所有文件和文件夹的元数据。
  ———维护文件结构和文件元信息的镜像
- EditLog操作日志文件中记录了所有针对文件的创建，删除，重命名操作。
  ———记录对文件的操作

> PS:
> 1.NN的元数据为了读写速度块是写在内存里的，FsImage只是它的一个镜像保存文件
> 2.当每输入一个增删改操作，EditLog都会单独生成一个文件，最后EL会生成多个文件
> 3.2NN不是NN的备份（但可以做备份），它的主要工作是帮助NN合并edits log，减少NN启动时间。
> 4.拓扑距离：根据节点网络构成的树形结构计算最短路径
> 5.机架感知：根据拓扑距离得到的节点摆放位置
> 6.元数据（MetaData）：关于数据的信息，提供关于数据的各种属性和特征的描述，从而增强了数据的可理解性、可管理性和可发现性。

**2、工作流程**

![HDFS流程](https://drunkcat69.github.io/images/DBMS/Hadoop/HDFS流程.png)

1. 当客户端对元数据进行增删改请求时，由于hadoop安全性要求比较高，**它会先将操作写入到editlog文件里，先持久化**。
2. 然后将具体增删改操作，将FSimage和edit写入内存里进行具体的操作，而不是直接写入硬盘上的静态文件。（PS. FsImage+内存元数据+EditLog经典架构采用：R/W数据直接面向内存，内存到一定限值后写入磁盘）
3. 将操作先写入内存，即使宕机了也可以恢复数据，由于FSimage和edit日志的存在，系统可以通过恢复这两者的内容来还原元数据状态。具体原因是因为**内存中的操作是瞬时的，即使在操作进行到一半时系统宕机，内存中的数据也不会受到永久性的破坏。**
4. 此时2NN发现时间到了，或者edit数据满了或者刚开机时（即reach checkpoint），就会请求执行辅助操作，NN收到后将edit瞬间复制一份，这个时候客户端传过来的数据继续写到edit里。
5. 把复制的edit和fsimage拷贝到2NN（SecondaryNameNode）里，操作写在2NN的内存里合并，合并后将文件返回给NN做为新的Fsimage，此时NN会清空旧的edit，从而为新的变更操作腾出空间。所以一旦NN宕机2NN比NN差一个edit部分，但旧的edit仍然存在在合并Fsimage里，但新的后续操作就没有保存，导致无法完全恢复原先状态，只能说辅助恢复。

**3、HDFS读文件流程**

**[1]. Client调用FileSystem.open()方法**

- FileSystem通过RPC与NN通信，NN返回该文件的部分或全部block列表（含有block拷贝的DN地址）。
- 选取客户端最近的DN建立连接，读取block，返回FSDataInputStream。   

**[2]. Client调用输入流的read()方法**

- 当读到block结尾时，FSDataInputStream关闭与当前DN的连接，并未读取下一个block寻找最近DN。
- 读取完一个block都会进行checksum验证，如果读取DN时出现错误，客户端会通知NN，然后再从下一个拥有该block拷贝的DN继续读。
- 如果block列表读完后，文件还未结束，FileSystem会继续从NN获取下一批block列表。

**[3]. 关闭FSDataInputStream**

![HDFS读文件](https://drunkcat69.github.io/images/DBMS/Hadoop/HDFS读文件.png)

**4、HDFS文件写入流程**

**【1】Client调用FileSystem的create()方法**

- FileSystem向NN发出请求，在NN的namespace里面创建一个新的文件，但是并不关联任何块。
- NN检查文件是否已经存在、操作权限。如果检查通过，NN记录新文件信息，并在某一个DN上创建数据块。
- 返回FSDataOutputStream，将Client引导至该数据块执行写入操作。

**【2】Client调用输出流的write()方法**

- HDFS默认将每个数据块放置3份。FSDataOutputStream将数据首先写到第一节点，第一节点将数据包传送并写入第二节点，第二节点 --> 第三节点。

**【3】Client调用流的close()方法**

- flush缓冲区的数据包，block完成复制份数后，NN返回成功消息。

![HDFS文件进流程](https://drunkcat69.github.io/images/DBMS/Hadoop/HDFS文件进流程.png)



**MapReduce**

- 一个编程模型及实现，用于处理或生产超大数据集

- 针对超大量数据，MapReduce解决如何在**很多台机器上并行计算**、数据分配及故障处理等问题。

- Map函数处理一个键值对（key/value pair）的数据集合，输出中间结果（依然是键值对集合）；然后 Reduce 函数处理中间结果，合并具有相同 key 的 value 值得到最终输出。

- **总的来说，MapReduce 所执行的分布式计算会以一组键值对作为输入，输出另一组键值对，用户则通过编写 Map 函数和 Reduce 函数来指定所要进行的计算。**

- 例子：统计大量文档每个单词出现的次数（Word Count），伪代码如下：

  ```
  map(String key, String value):
    // key: document name
    // value: document contents
    for each word w in value:
      EmitIntermediate(w, “1”); #每个单词出现一次，赋予一个1的值
  
  reduce(String key, Iterator values):
    // key: a word
    // values: a list of counts
    int result = 0;
    for each v in values:
      result += ParseInt(v);  #将之前的value(1)转换为int的格式
    Emit(AsString(result));   #累加所有单词的出现次数
  ```



# Ref:

[大数据Hadoop原理介绍+安装+实战操作 (HDFS+YARN+MapReduce)](https://www.cnblogs.com/liugp/p/16101242.html)

