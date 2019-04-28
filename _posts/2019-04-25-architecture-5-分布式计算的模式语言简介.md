---
title: 模式简介四
date: 2019-04-26
categories:
- Tech
- patterns
- distributed computing
tags:
- blog
- patterns
- distributed computing
---

> 分布式计算。

Pattern-Oriented Software Architecture  
Volume 4  

A Pattern Language for Distributed Computing  

## 简介  

如果使用模式语言，需要理解模式的范围、结构、内容、表现。  
从设计的角度，软件通常被认为由以下部分组成：函数、源文件、模块、对象、类、成员、包、库、组件、服务、子系统。  
模式作为一种补充，可以用来描述和改良软件设计，发掘并命名那些已经验证的通用技术。  
分布式系统是一种计算系统，系统中多个组件通过网络通信的方式互联。  
用于支持分布式的技术：Ad hoc网络编程，结构化通信，中间件。  
Ad hoc网络编程，主要依靠进程间通信进行分布式组件之间的互联和信息交换，比如共享内存、管道、套结字。  
结构化通信封装了与机器相关的细节，比如比特、字节、二进制读写等。  
结构化通信，示例：远程过程调用，RPC，remote procedure call。  
前两个的不足：  
- Ad hoc网络编程，代码与底层的API与IPC的耦合  
- RPC，需要关注对方位置，部署方式的改变比较困难，遗留代码不易整合，异构组件难以集成。   

中间件可以解决以上的不足。  
流行的中间件技术有：  
- 分布式对象计算中间件  
- 组件中间件  
- 发布/订阅中间件  
- 面向服务架构以及Web服务  

RPC平台、分布式对象计算中间件、组件技术中间件，都是基于请求/响应的通信模型，客户端向服务端发送请求，服务器将响应发送会客户端。  

发布/订阅中间件和面向消息的中间件，增加异步通信能力，也具有组播能力。  
作为一个进程运行运行在单独的节点，从分布式系统的一个全局的数据空间读/写事件消息。  

面向服务架构是一种组合和使用那些由不同组织或个人所控制的分布式处理能力的方式。  
soap的引入发展出web service。  

## 模式语言

到此处，需要先确认一些计算概念：死锁、事务、同步、远程、任务切换。  
而后再往下走。  

关于分布式计算的模式语言一共包含114个模式，分为13组问题域。  

从0开始，也即从混沌到结构之后，有几个考虑的方面：应用控制、数据库访问、分布式基础设施、组件提炼、对象协作。  
对于分布式基础设施之后，需要考虑的方面：事件处理、并发、同步、适配和扩展、资源管理。  
组件划分，需要考虑的方面：模态行为、适配与扩展、并发、资源管理。  

那么，按照无序的排列，以上的13个问题域如下：  
混沌到结构，分布式基础设施，事件分离和分发，接口划分，组件划分，应用控制，并发，同步，对象交互，适配与扩展，对象行为，资源管理，数据库访问。   


混沌到结构：  
- Domain Model  
- Layers  
- Model-View-Controller  
- Presentation-Abstraction-Control  
- Microkernel  
- Reflection  
- Pipes and Filters  
- Shared Repository  
- Blackboard  
- Domain Object  

分布式基础设施：  
- Messageing  
- Message Channel  
- Message Endpoint  
- Message Translator  
- Message Router  
- Broker  
- Client Proxy  
- Requestor  
- Invoker  
- Client Request Handler  
- Server Request Handler  
- Publisher-Subscriber  

事件分离和分发：  
- Reactor  
- Proactor  
- Acceptor-Connector  
- Asynchronous Completion Token  

接口划分：  
- Explicit Interface  
- Extension Interface  
- Introspective Interface  
- Dynamic Invocation Interface  
- Proxy  
- Business Delegate  
- Facade  
- Combined Method  
- Iterator  
- Enumeration Method  
- Batch Method  

组件划分：  
- Encapsulated Implementation  
- Whole-Part  
- Composite  
- Master-Slave  
- Half-Object plus Protocol  
- Replicated Component Group  

应用控制：  
- Page Controller  
- Front Controller  
- Application Controller  
- Command Processor  
- Template View  
- Transform View  
- Firewall Proxy  
- Authorization  

并发：  
- Half-Sync/Half-Async  
- Leader/Followers  
- Active Object  
- Monitor Object  

同步：  
- Guarded Suspension  
- Future  
- Thread-Safe Interface  
- Double-Checked Locking  
- Strategized Locking  
- Scoped Locking  
- Thread-Specific Storage  
- Copied Value  
- Immutable Value  

对象交互：  
- Observer  
- Double Dispatch  
- Mediator  
- Memento  
- Context Object  
- Data Transfer Object  
- Command  
- Message  

适配和扩展：  
- Bridge  
- Object Adapter  
- Interceptor  
- Chain of Responsibility  
- Interpreter  
- Visitor  
- Decorator  
- Template Method  
- Strategy  
- Null Object  
- Wrapper Facade  
- Execute-Around Object  
- Declarative Component Configuration  

对象行为：  
- Objects For States  
- Methods For States  
- Collections for States  

资源管理：  
- Object Manager  
- Container  
- Component Configurator  
- Lookup  
- Virtual Proxy  
- Lifecycle Callback  
- Task Coordinator  
- Resource Pool  
- Resource Cache  
- Lazy Acquisition  
- Eager Acquisition  
- Partial Acquisition  
- Activator  
- Evictor  
- Leasing  
- Automated Garbage Collection  
- Counting Handle  
- Abstract Factory  
- Builder  
- Factory Method  
- Disposal Method  

数据库访问：  
- Database Access Layer  
- Data Mapper  
- Row Data Gateway  
- Table Data Gateway  
- Active Record  

### 系统分析  

系统构建时候使用模式语言更清晰，同样，使用模式语言进行系统分析也可以更清楚的说明系统的实现方式和一些特性。  

下面几篇使用模式语言分析几个系统。

