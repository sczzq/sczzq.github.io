---
title: C++11新特性
date: 2019-04-30
categories:
- Tech
- c++11
tags:
- blog
- c++11
---


## 前记  
  
总有面试问到C++11特性，虽然心里清楚了解，  
但而又不能像喝水那样的随口而出，  
可能和个人表达有关，但是，总结一下，列出来，  
最起码将这些名词抄一遍，加强认知，多熟悉这些名词，  
  
  
面试大忌，心里明白，说出来是乱七八糟的，  
也许心里是不明白的，没有那么有条理，  
不知从何处说起，也不知道到何处结束  
思维逻辑和知识熟悉程度的关系  
  
  
  
## 常用特性  
  
根据C++程序设计语言，Bjarne Stroustrup，C++11常用特性包含：  
  
### 语言特性  
  
默认操作的控制，default, delete  
从初始化器推断对象的类型, auto  
推广的常量表达式求值，constexpr  
类内成员初始化器, 直接可以在类型进行成员初始化  
继承的构造函数，using parent<>::parent;  
Lambda表达式，[]()->{}  
移动语义  
一种声明函数不能抛出异常的方法，noexcept  
空指针一个更合适的名字，nullptr  
范围for语句，foreach  
覆盖控制，final，override  
类型别名，一种为类型或模版提供别名的机制，特别是，一种通过绑定另一个模版的某些实参来定义一个新模版的方法，using  
类型和限定作用域的枚举，enum class  
通用和统一的初始化（包括任意长度的初始化器列表和防止窄化转换）  
可变参数模版，一种向模版传递任意数量任意类型实参的机制  
  
### 标准库  
  
哈希容器，比如unorderd_map  
基础并发库组件，如thread, mutex, lock  
异步计算发射和结果返回，future, promise, async  
垃圾收集接口  
正则表达式库，regexp  
随机数库  
一种用于简单高效传递资源的指针  
一种可以表示共享所有权的指针  
tuple库  
通用的bind  
用于保存可调用对象的function类型  
  
  
以上是常用的特性，  
在第二本书中，详细列出了C++11新语言特性和标准库的新增部分  
  
## 更完整清单  
  
### 语言特性  
  
使用{}列表进行一致且通用的初始化  
从初始化器进行类型推断，auto  
避免窄化转换  
推广的且有保证的常量表达式，constexpr  
范围for语句  
空指针关键字，nullptr  
限域且强类型的枚举，enum class  
编译时断言，static_assert  
{}列表到std::intializer_list的语言映射  
右值引用  
以>>结束的嵌套模版参数  
lambda  
可变参数模版  
类型和模版别名  
Unicode字符  
long long整数类型  
对齐控制，alignas和alignof  
在声明中将表达式的类型用作类型的能力,decltype  
裸字符串字面值常量  
推广的POD  
推广的union  
局部类作为模版实参  
尾置返回类型语法  
属性语法和两种标准属性，[[carries_dependency]]，[[noreturn]]  
阻止异常传播，noexcept说明符  
检测表达式中抛出异常的可能性，noexcept  
C99特性，扩展的整数类型，窄字符串和宽字符串的链接,__func____STDC_HOSTED, _Pragma(X)，可变参数宏及空宏参数  
inline名字空间  
委托构造函数  
类内成员初始化器  
默认控制，default, delete  
显式转换运算符  
用户自定义字面值常量  
template实例化更为显式的控制，extern template  
函数模版的默认模版实参  
继承构造函数  
覆盖控制，override, final  
更简单、更通用的SFINAE规则  
内存模型  
线程局部存储  
  
  
### 标准库组件  
  
容器的initializer_list构造函数  
容器的移动语义  
单向链表，forward_list  
哈希容器，unordered_map，unorderes_multimap，unordered_set，unordered_multiset  
资源管理指针，unique_ptr，shared_ptr，weak_ptr  
并发的支持，thread，互斥量，锁，条件变量  
高层并发支持，packaged_thread, future, promise, async()  
tuple  
正则表达式,regex  
随机数，uniform_int_distribution，normal_distribution，random_engine  
整数类型名，int16_t, uint32_t,int_fast64_t  
固定大小连续存储序列容器,array  
拷贝和重抛出异常  
使用错误码报告错误,system_error  
容器的emplace操作  
广泛使用的constexpr函数  
系统使用noexcept函数  
改进的函数适配器，function，bind()  
string到数值的转换  
限域分配器  
类型萃取，比如is_integral，is_base_of  
时间工具，duration，time_point  
编译时有理数算术运算，ratio  
放弃进程,quit_exit  
更多算法，如move(),copy_if(), is_sorted()  
垃圾收集ABI  
低层并发支持  
  
  
  
内存模型
计算机体系结构中，内存层次结构，有多层内存，也就是多个内存实体，
一般的过程是，先将对象从主村中加载到缓存中，然后加载到处理器的寄存器中，在那里修改，然后再逐级写回，缓存可能会缓存一些数据。
两个数据可能处于同一内存位置，也就是从高层看是两个变量，实际处理的是同一个位置的变量。
那么，C++内存模型保证了两个更新和访问不同内存位置的线程可以互不影响地执行。
另外，我们所说的编程的机器是由硬件和非常低层的（由编译器生成的）软件组成的
C++将内存位置定义为能保证合理行为的内存单元。
注意指令重排
为提高性能，编译器，优化器，以及硬件都有可能重排指令顺序。
内存序，用来描述一个线程从内存访问一个值时会看到什么。

数据竞争，在多线程中，有多个执行体访问和操作一块内存，那么从内存位置，到指令重排，再到内存序，都有可能导致程序的执行结果和想要的不一样，也就是产生了数据竞争
一些场景，比如，两个线程同时访问同一个内存位置且至少其中一个是进行写操作，优化器（或硬件指令调度器）可能基于对内存中值的一些假设重排代码
有很多方法用来避免数据竞争：
- 只使用单线程（可使用多进程或协程）
- 对每个有明显数据竞争倾向的数据项使用锁机制
- 尝试仔细检查代码，选择性地添加锁来避免数据竞争
- 让检测软件检测所有数据竞争并报告给主程序员来修正或自动插入锁
- 设计代码，令线程仅通过放-取风格的接口完成
- 使用更高层的库或工具实现隐式或足够程式化的数据共享或并发，从而令共享可管理


