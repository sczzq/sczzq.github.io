---
title: ceph系统的模式语言剖析
date: 2019-04-26
categories:
- Tech
- ceph
- patterns
tags:
- blog
- ceph
- patterns
---

> 模式语言在系统分析中的使用

首先，定义系统的初始结构，一个架构基础。  
领域模式，定义系统的业务职责和变化范畴。  
模型元素是对应用领域的有用抽象，其角色和交互反映了该领域的工作流。  

有用的抽象，都是些啥，  
对象，池，存放组，存储进程，  
监控进程，管理进程，元信息管理进程，  
通信协议，客户端库，客户端实现，  
存储集群，集群拓扑图，集群状态，  
（保证集群拓扑图的）一致性算法，认证框架，  
用户，密钥(keyring)，  
部署工具，Web控制面板，  
结对操作，对等体集合，对等体集合的主存储进程，  
消息体，心跳，服务发现，  
CRUSH，PAXOS，事务处理，日志(journal)，  
重平衡，数据一致性，  
数据一致性之纠删码，  
kv数据库接口，文件系统接口，磁盘接口，  
对象存储接口，对象存储实现，  
单机存储引擎，  
系统接口，  
网络协议栈，文件系统操作，内核空间操作，用户空间操作，  
配置，部署，操作，  

这些抽象是高层次的抽象，表明所具有的功能，  
从执行体，到数据层次，到操作层次，到状态查询和维护，到核心模块，  
还有系统接口。  
用以上的抽象名词可以描述系统的工作流，业务逻辑，操作步骤。  
也可以对系统进行分析，对设计和性能做初步判断。  
  
从工作流说明以上的抽象的关系：  
对象，也就是一堆数据，带名称的，用以标示对象和后续的查找、修改、删除等操作。  
目的：将对象的名称和数据放到池中。  
池是预先建立好的，一个池包含多个存放组，在建立的时候指定存放组的数量，由算法自动分配存放组和存储进程的关系。  
操作方式：指定对象的名称、数据，和池的名称，剩余的工作由librados完成。  
有了以上操作，剩下的是librados和存储集群的执行过程。  

这里librados是客户端代理  

librados向monitor获取最新集群拓扑图  
librados使用crush计算存放组和对象存储进程  
并将对象发送到对象存储进程（对等体集合的主存储进程）  
对象存储进程根据crush计算其它对象存储进程（对等体集合中的其它存储进程）  
并将对象存入对象存储模块  
这里的对象存储模块就是指一个库，或者一个接口类  
对象存储模块调用对象存储实现类进行数据存储    



模式语言应用在不同的层面上  
这里的层面是指软件设计中考虑的编程语言产生的概念  
比如，函数，类，对象，库(so或a文件)，程序(可执行文件)，模块(库或程序)，子系统(库或程序或模块)  
分层模式应用在库和程序上，有时应用在模块和子系统上  
比如网络协议栈，应用在库或函数上，几个函数是一层，然后另几个函数是一层  
而在此处，分层作用于模块上，客户端代理和服务端  
服务端也即是存储集群，  


另一个划分，参与者有：客户端，存储集群，Web控制面板，命令行部署模块，
客户端通过调用librados和集群进行数据传输，此为代理模式。  
客户端的实现有cephfs、rbd、radosgw，都调用librados，这些是可运行程序  
存储集群中有manager、monitor、osd、mds  
其中monitor相当于一个broker，创建和维护集群拓扑图，也就是各个程序的运行状态和隶属关系    
osd是业务处理程序，负责存储  
manager是管理程序，负责集群状态的获取和改变，也就是接收命令行和web端的关于集群状态的命令  
mds是metedata server，当运行cephfs时保存文件元信息，是一种资源管理模式的实现  


所以，存储集群中有分布式基础设施中的broker模式，应用控制中的Application Controller模式，  
cephx即是授权模式的一种实现。  


组件划分，  
监控组件  
资源管理代理  

另一种模块划分，是编译单元组织形式，也是抽象概念的实现代码，也是进行封装和模块化的方式   


具体实现的模块：  

broker的实现  
在实现中使用Messenger类作为消息模块  
在消息模块中，消息分发使用责任链模式，每个消息有多个Dispatcher，挨个调用它们执行分发，直到某个分发器完成分发为止。  

Messenger和执行体的关系：  
osd中有多个Messenger对象，每个对象处理一种服务类型，比如来自客户端的消息，来自集群的消息，心跳消息等。   
截止2019-04-27，在源码的根目录中，ceph_osd.cc和osd类共同说明了多种Messenger对象，包含：客户端消息对象，集群消息对象，两个心跳客户端消息对象，两个心跳服务端消息对象，对象处理对象。  
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
memstore对内存数据保存方式进行封装，提供存储接口  
而objectstore是一个抽象工厂类，bluestore、filestore、kstore、memstore是具体的实现。  


kv模块  
KeyValueDB是抽象工厂类，也是接口类，  
KineticStore、LevelDBStore、MemDB、RocksDBStore是具体类，  
这些类封装了各自的kv存储系统的接口，即Kinetic，LevelDB，Mem，RocksDB。  


数据容错性模块  
数据容错性，有两种方式，副本复制，纠删码编码  
副本复制是osd中的一个类   
纠删码编码是一个单独的模块  
erasure code有多种实现：clay, isa, jerasure, lrc, shec  
这些都在erasure-code下面，isa-l作为第三方库在上一级目录。  
在erasure-code下面的ErasureCode类只是作为具体实现的父类，充当哨兵。  
类继承关系为 ErasureCodeXXX -> ErasureCode -> ErasureCodeInterface  


事务的支持  
使用命令处理模式，  
一个事务由一个或多个命令组成，  
可以保存命令，  
支持提交，执行，撤销  


存放位置计算模块  
作为一项服务，根据哈希值和拓扑图计算存放位置  
此项服务不是一个单独运行的进程，而是一个类，或者函数接口。  


spdk  
Storage Performance Development Kit  
提供工具集和库，用于高性能、可扩展、用户模式存储程序。  
将所有的必须的驱动都移到用户空间，并提供基于polled模式的接口，以替代中断，  
这样可避免内核上下文切换，也消除了处理中断的造成的消耗。  
caching模式增加了性能，但是caching层次太多，需要协调的关系太多，也就造成了性能的下降，这个开发套件减少了内核的缓存层和处理层，也就减少了内核中的性能消耗，直接接管磁盘驱动的管理。  


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


从以上可看出，存储进程，也就是具体处理任务的进程，其核心是执行数据存储以及提供服务质量，关于服务质量，比如数据的容错性，数据的分散性，数据读写速率，事务的处理速率等。这些服务质量是程序设计和实现的具体体现。  

那么，存储集群，包含网络处理模块，存储模块，监控模块，状态管理模块，应用管理模块，其中应用管理模块包含部署工具和Web控制面板，而网络处理模块是其它所有模块都要用到的。  

网络处理模块又包含事件处理，并发，同步，资源管理这些问题域。  
根据以上描述，使用两种模式实现网络处理模块，  
一个是基于Linux networking socket layer user interface（Linux网络套接字层用户接口）和POSIX Threads，使用端口复用、线程池、任务队列、互斥量条件变量技术，完成事件处理，并发，同步和资源管理的实现。使用的模式有half-sync/half-async，caching，thread pooling，task/message queue，  
另一个是基于seastar的实现，不过目前的这部分工作还正在进行中。  
此模块还有一个服务资源管理问题，如何发现和注册服务，以及检查服务的可用性，也就是各个进程如何相互发现并保持状态更新，使用两种模式Lookup、Heartbeat，有一个第三方库，powerdns，作为broker（monitor，监控进程）之间的服务发现，使用Heartbeat作为业务进程相互检查，业务程序也定期向broker发送自身状态，也向状态管理模块发送自身状态。  


存储模块应该属于具体领域内的设计，根据需求和目标，完成设计。  
这样看的话，使用Domain Model进行划分和分析。  
抽象概念的实现，数据存放在哪里，如何保证数据的容错性，如何支持扩展性，如何完成数据恢复，甚至怎么做数据迁移，还要保证高性能，支持高并发和大的事务数，支持大的吞吐量   
这些抽象概念是和网络处理模块的概念分离开的，它们属于不同的具体领域。  
数据存放位置，高性能，这些由CRUSH算法及其实现来保证。  
数据容错性，可扩展性，数据恢复，这些也由CRUSH算法来支持，还需要其它概念和实现，  
用复制多份和纠删码编码来支持数据容错性，  
用重平衡来保证这三项，用回填概念来实现扩展性中的数据的分散性。  

若将功能分层，  
第一层，是CRUSH算法及其实现，  
第二层，是数据容错性及其实现，  
第三层，是数据存储接口及其实现。  

数据存储接口是做什么的？数据存储接口将数据保存下来，直接保存下来，而不做更多的处理了。数据存储最终会将数据保存到系统的文件系统(使用filestore)，块设备(使用bluestore)，内存(使用memstore)中，也有数据保存到其它的单机对象存储引擎(比如LevelDB,RocksDB等)。  

存储模块也支持事务，事务应当在哪一层？

第三层可以继续细分，存储的数据有两种，一个是命令数据，一种是原始数据，其中，原始数据即是客户端发送过来后经过第二层的处理后的数据，命令数据是指包含命令和原始数据的数据。  

使用命令数据来支持事务，有了命令数据的存储之后，可以保证事务的属性（ACID）。  
同时命令数据也保证了数据的一致性和完整性，因为可以通过命令数据来检查原始数据。  

还有数据清理，定期检查数据，来保证数据的有效性，防止硬件级别的错误。  

可扩展性的支持，对以上抽象概念的操作，包括增删改查，
比如，对象的增删改查，也就是客户端或者真正需要处理的事务，
需要增删改查的抽象概念有，对象，池，存放组，存储进程，用户，密钥(keyring)
每一个增删改查会引起其它的抽象概念的登场，
维持系统的功能和特性也是需要一些其它概念的实现，

Message 消息类型：  
monitor internal  
monitor <-> mon admin tool  
osd internal  
mds  
generic  
special  
ceph-mgr <-> OSD/MDS daemons  
ceph-mgr <-> ceph-mon  
ceph-mon(mgrmonitor) -> osd/mds daemons  
ceph-mon(mgrmonitor) -> ceph-mgr  
cephmgr -> ceph-mon  
message完成了序列化消息到消息类的解析，以及消息类进行序列化的过程。  
另外，


对象交互，Processor引用Messenger，Worker，  
AsyncMessenger引用NetworkStack，Processor，DispatchQueue，Worker，AsyncConnectionRef，EventCallbackRef，  
AsyncMessenger建立连接并维护连接，往连接类中提交Message  

Processor类监听端口、建立连接、销毁连接，但是将执行函数提交到Worker中  
每一个监听端口建立一个Processor放到AsyncMessenger中  
注册到Worker的EventCenter中，submit_to有提交事务接口，有开启线程接口  
AsyncMessenger开启Processor的线程，开启dispatch_queue的流程  

AsyncConnection维护两个终端的逻辑会话。  
也就是说，一对地址可以找到唯一的一个连接，AsyncConnection  
AsyncConnection处理网络错误和读写事务  
如果一个文件描述符断了，AsyncConnection负责维护message queue和序列化，并尝试重连   
AsyncConnection引用AsyncMessenger，DispatchQueue，EventCallbackRef，Worker，EventCenter，Protocol
AsyncConnection在发送数据之前，经Protocol进行协议封装，然后放入队列中进行发送。  


DispatchQueue包含所有需要分发消息的连接，按照消息的优先级组织，以轮询的风格执行分发    

EventCenter，所有的网络事件都在这里注册，监听，使用epoll, kqueue, select等。  

Worker拥有EventCenter

CephContext  
CephContext表明一个上下文，由一个库的使用者所持有。
在一个进程中可能有多个CephContext  

对于守护进程和工具程序，应当值有一个CephContext，
CephContext包含配置，输出对象，其它任意可能需要传到libcommon库中的函数调用。  
CephContext维护一个单例对象映射集合，
引用CephContextServiceThread, AdminSocket, spinlock, PerfCountersCollection, md_config_obs_t, CephContextHook, HeartbeatMap, ForkWatcher, CryptoHandler, CephContextObs, PluginRegistry, CrushLocation, PerfCounters, 

osd中事件触发形式，一个是和其它进程的交互，也就是接收Messenger的消息，处理其它进程发送来的消息命令，一个是注册定时器事件，注册回调接口，定期执行一些事件，比如，数据清洗。



END  
  
-----------------------------


