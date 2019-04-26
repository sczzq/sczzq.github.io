---
title: ceph系统的模式语言剖析
date: 2019-04-26
categories:
- Tech
- Patterns
- ceph
tags:
- blog
- ceph
- patterns
---

> 模式语言在系统分析中的使用

首先，分层，参与者有：客户端，存储集群，Web控制面板，命令行部署模块，
客户端通过调用librados和集群进行数据传输，此为代理模式。  
客户端的实现有cephfs、rbd、radosgw，都调用librados，这些是可运行程序  
存储集群中有manager、monitor、osd、mds，  
其中monitor相当于一个broker，创建和维护集群映射图  
osd是业务处理程序，负责存储，  
manager是管理程序，负责集群状态的获取和改变，也就是接收命令行和web端的关于集群状态的命令。  
mds是metedata server，当运行cephfs时保存文件元信息，是一种资源管理。  

所以，存储集群中有分布式基础设施中的broker模式，应用控制中的Application Controller模式，  
cephx即是授权模式的一种实现。  

具体实现的模块：  
cephx模块  
记录模块  
日志模块  

消息模块  
在消息模块中，消息分发使用责任链模式，每个消息有多个Dispatcher，挨个调用它们执行分发，直到某个分发器完成分发，然后就终止。  
消息分为多种，每个运行者的程序都会向外发送消息。  
消息模块中有：消息，连接，分发器，（带优先级的）分发队列，分发策略。消息类有接口类和各类消息的实现类。 
使用模版类定义一个消息工厂类，此类只有一个构建消息成员，即build成员。  
作为分布式基础设施，存在于消息模块中，连接类，分发器，（带优先级的）分发队列，这些共同组成网络传输模块。  
但是，有两个目录中都有网络事件处理，crimson，msg。  
其中crimson下面是seastar messenger interfaces  
从[http://blog.umcloud.com/ceph-开发者月报-2019-03/](http://blog.umcloud.com/ceph-开发者月报-2019-03/)中可看到，‘基于 seastar 重构 OSD 的工作推进’，crimson即是重构的过程。  

seastar参见[http://seastar.io/](http://seastar.io/)
seastar是基于C++的现代硬件上的高性能服务程序框架。  
包含的特性：  
- Shared-nothing design  
- High-performance networking  
- Futures and promises  
- Message passing  


Wrapper Facade模式，封装底层API和数据，方便模块化和调用。  


存储引擎  
filestore对不同的文件系统的接口进行封装，提供存储接口  
bluestore对块设备的接口进行封装，提供存储接口  
而objectstore是一个抽象工厂类，bluestore、filestore、kstore、memstore是具体的实现。  


kv模块  
KeyValueDB是抽象工厂类，也是接口类，  
KineticStore、LevelDBStore、MemDB、RocksDBStore是具体类，  
这些类封装了各自的kv存储系统的接口，即Kinetic，LevelDB，Mem，RocksDB。  


数据容错性模块  
数据容错性，有两种方式，副本复制，纠删码编码  
纠删码编码是一个单独的模块  
副本复制是osd中的一个类   
erasure code有多种实现：  
- clay  
- isa  
- jerasure  
- lrc  
- shec  
这些都在erasure-code下面，isa-l作为第三方库在上一级目录。  
在erasure-code下面的ErasureCode类只是作为具体实现的父类，充当哨兵。  
类继承关系为 ErasureCodeXXX -> ErasureCode -> ErasureCodeInterface  


事务的支持  
使用命令处理模式，  
一个事务由一个或多个命令组成，  
可以保存命令，  
支持提交，执行，撤销  


存放位置计算模块  
作为一项服务，根据哈希值和映射图计算存放位置  
此项服务不是一个单独运行的进程，而是一个类，或者函数接口。  


spdk  
Storage Performance Development Kit  
提供工具集和库，用于高性能、可扩展、用户模式存储程序。  
将所有的必须的驱动都移到用户空间，并提供基于polled模式的接口，以替代中断，  
这样可避免内核上下文切换，也消除了处理中断的造成的消耗。  


数据压缩模块  
Compressor是接口类  
实现的有几种压缩方式：brotli，lz4，snappy，zlib，zstd
也就是将各个库进行类的封装，并实现Compressor的接口    


Web控制面板  
作为应用控制问题域的一个实现，它和manager程序进行通信，获取和存取集群的状态，并可以设置一些状态。  
manager应当是使用的Front Controller模式。  
不过，如果将模式和实现做到一一对照，也是不现实的。  
使用模式只是对实现的一种解释和参照。  
就集群的状态来说，这里有MVC模式  
视图即是Web控制面板  
控制有Web控制面板和manager程序  
模型就是manager程序，manager程序保存所有的数据模型，不过，manager程序也是从集群中其它程序获取的数据。   

部署程序  
它的作用和Web控制面板相似，不过就目前来看，支持的功能更多。  
部署程序和整个集群进行交互，支持所有的命令。  
它所使用的模式应当有代理模式。  
