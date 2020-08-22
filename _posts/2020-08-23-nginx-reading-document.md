---
title: nginx -- Reading document
date: 2020-08-23
categories:
- Tech
- Methodology
- nginx
tags:
- blog
- post
---

> nginx reading document


## 简介  
nginx[engine x] 可作为  
1，HTTP 服务器  
2，反向代理服务器  
3，邮件代理服务器  
4，TCP/UDP代理服务器  
  
源码和文档发布在 2-clause BSD-like license.  
  
  
## 基本HTTP服务器特性  
1，发送静态和索引文件，自动索引化；可做打开文件描述符的缓存。  
2，使用缓存技术加速反向代理；并可做此服务的负载均衡和容错。  
3，使用缓存技术加速 FastCGI，uwsgi, SCGI，memcached 服务器；并可做这类服务的负载均衡和容错。  
4，模块化架构。过滤器包含 gzipping，字节范围，段相应，XSLT，SSI，图像转换。如果可以被服务支持，单网页中的多SSI可被并行处理。  
5，SSL TLS SNI 支持。  
6，支持HTTP/2的权重和依赖支持的优先级。  
  
  
## 其它HTTP服务器特性  
1，支持基于名字和基于IP的虚拟服务器  
2，支持保活和管道连接，keep-alive, pipelined connection  
3，格式化访问日志，写缓存日志，快速日志回卷，系统日志支持  
4，3xx-5xx错误码重定向  
5，重写模块：使用正则表达式做URI转换  
6，根据客户端地址执行不同函数  
7，基于客户端地址，密钥（HTTP基础认证），子请求结果，做访问控制  
8，HTTP引用的合法性鉴别  
9，支持PUT, DELETE, MKCOL, COPY, MOVE方法  
10，FLV，MP4流  
11，响应速率的限制  
12，对同一地址的并发连接和请求的限制  
13，基于IP的地理位置  
14，A/B测试  
15，请求镜像  
16，嵌入Perl  
17，njs脚本语言  
  
  
## 邮件服务器特性  
1，使用外部HTTP认证服务器做IMAP或POP3服务器的用户重定向  
2，用户认证，使用外部HTTP认证服务器，连接重定向到内部SMTP服务器  
3，认证方法：  
	POP3： USER/PASS, APOP, AUTH LOGIN/PLAIN/CRAM-MD5.  
	IMAP: LOGIN, AUTH LOGIN/PLAIN/CRAM-MD5.  
	SMTP: AUTH LOGIN/PLAIN/CRAM-MD5.  
4，支持SLL  
5，支持 STARTTLS STLS  
  
  
## TCP/UDP代理服务器特性  
1，对TCP和UDP的通用代理  
2，TCP支持 SSL 和 TLS SNI  
3，负载均衡和容错  
4，基于客户端地址的访问控制  
5，基于客户端地址执行不同函数  
6，限制来之同一地址的同时连接数  
7，格式化的访问日志，写缓存日志，快速日志回卷，系统日志  
8，基于IP的地理位置  
9，A/B测试  
10，njs脚本语言  
  
  
## 架构和可扩展性  
1，一个master，多个worker processes，工作进程运行在非特权用户  
2，灵活的配置  
3，重配置，程序的升级，不需要中断客户端服务  
4，支持kqueue(freebsd 4.1+), epoll(linux 2.6+), /dev/poll(solaris 7 11/99+), event ports (solaris 10), select, and poll  
5，支持kqueue的很多特性，EV_CLEAR, EV_DISABLE(to temporarily disable events), NOTE_LOWAT, EV_EOF, number of available data, error codes  
5，支持epoll的很多特性，EPOLLRDHUP(linux 2.6.17+, glic2.8+)，EPOLLEXCLUSIVE(linux 4.5+, glic 2.24+)  
6，支持 sendfile(freebsd 3.1+, linux2.2+, macos 10.5+), sendfile64(linux 2.4.21+), and sendfilev(solaris 8 7/01 +)  
7，file aio(freebsd 4.3+, linux 2.6.22+)  
8，DIRECTIO(freebsd 4.4+, linux 2.4+, solaris 2.6+, macos)  
9, Accept-filters and TCP_DEFER_ACCEPT  
10, 10000非活动HTTP长链接只需要 2.5M内存。  
11，最小化数据拷贝  
  
  
## 负载均衡  
模块 ngx_http_upstream_module 可以定义一组服务器，  
用来被 proxy_pass, fastcgi_pass, uwsgi_pass, scgi_pass, memcached_pass, grpc_pass指令使用  
  
### 模块包含的指令  
upstream，  
	定义一组服务器，服务器可在不同端口监听，可包含TCP，UNIX-domain套接字  
	默认，请求按照加权轮询平衡方法分发到服务器。	  
	如果分发到服务器失败，则分发到下一个轮询的服务器。  
	如果没有收到成功的响应，客户端将接收到最后一个服务器发送的错误信息。  
server  
	定义服务器的地址和其它参数。upstream的子指令  
	默认80端口。  
	可以使用的参数  
		weight=*number*  
		max_conns=*number*  
		max_fails=*number*  
		fail_timeout=*time*  
		backup  
		down  
	以下参数商业版可用  
		resolve  
		route=*string*  
		service=*name*  
		slow_start=*time*  
		drain  
zone  
state  
hash  
ip_hash  
	平衡方法  
keepalive  
keepalive_requests  
keepalive_timeout  
ntlm  
least_conn  
	平衡方法，最少存活连接优先，如果有相同，相同的执行轮询。  
least_time  
	平衡方法，最小平均响应时间和最少存活连接数优先，考虑加权，如果有相同，相同的执行轮询。  
queue  
random  
	平衡方法，随机选取服务器，考虑加权。  
resolver  
	指定域名解析服务器  
resolver_timeout  
sticky  
sticky_cookie_insert  
  
商业版本可配置 health check  
以上有的参数需要商业版才能支持。  
详细信息请参考原文链接。  
  
  
## 基本命令  
```shell  
nginx  
nginx -s [stop|quit|reload|reopen]  
```  
  
## 配置文件结构  
nginx包含简单指令和块指令  
简单指令是由名字，参数，结束符(;)组成  
块指令由多个简单指令组成，并由{}包围.  
包含块指令的称为一个上下文，比如 events, http, server, location  
在配置文件中，不在任何context中的指令考虑为main context中。  
events http in main, server in http, location in server  
  
树形结构如下  
```text  
main  
	http  
		server  
			location  
	events  
```  
在#后面的部分是注释  
  
注意，配置中的名字是指定的，我们仅仅可以根据语法来设置参数  
基本服务器是向客户端提供文件，这些文件在指定位置，只需要按照请求格式将文件发送给客户端  
代理服务器需要运行一个提供内容的服务器。  
  
## 作为反向代理，如何处理一个请求  
nginx首先接收到客户端的请求数据，一般包含头部和数据部  
nginx根据头部找到合适的服务器，  
将请求转发给服务器，  
收到服务器的响应，  
将响应发送给客户端  
  
## 作为网页服务器  
nginx根据配置文件中的正则表达式进行匹配，找到一个位置。  
然后将位置中匹配到的数据发送给客户端。  
  
  
## 如何处理 TCP/UDP session  
一个客户端来的TCP/UDP会话按照步骤执行，phases  
Post-accept  
Pre-access  
Access  
SSL  
Preread  
Content  
Log  
  
  
## 如何部署 High Available  
  
### 云服务提供商如何部署 HA  
  
### 可控机房内如何部署 HA   
使用 keepalived 做 HA  
  
keepalived 包含三个组件  
1，keepalived 守护进程  
2，虚拟路由冗余协议的实现，管理虚拟路由  
3，健康检查机制，用来确认服务是否运行以及其操作性  
  
  
### 同步配置文件  
  
  
  
## IMPOSSIBLE  
nginx 作为入口，整个服务的入口  
客户端请求中是服务中的资源路径  
但是请求头部换成了nginx的地址和端口  
也就是说，客户端向nginx要数据，  
nginx根据配置将请求的路径发送给其它服务器  
  
高可用方案中，以虚拟IP作为入口，  
通过keepalived作用，再找到nginx的IP，  
也就是将虚拟IP映射到nginx的IP，  
客户端访问的是虚拟IP，实际将数据发送到的是nginx的实际的IP。  
  
  
  
  
## 原文链接  
1，[简介](http://nginx.org/en/)  
2，[负载均衡](http://nginx.org/en/docs/http/ngx_http_upstream_module.html)  
  
