---
title: high concurrence
date: 2020-08-16
categories:
- Tech
- Network
tags:
- blog
- post
---

> one of three high question


## IO multiplexing  
  
This is essential for high concurrence.  
  
  
## Base processing  
  
```c++  
wait event / IO multiplexing  
	read data  
	deal data  
	write data  
  
```  
  
Note: here the IO event is async  
  
## Half-sync/Half-async  
  
We just could wait the events in one thread.  
But we can deal with data in multi-thread.  
So, use one thread to wait events,  
	and use worker threads to deal data.  
  
Using data queue to transfer data between threads.  
  
```c++  
wait events / IO multiplexing  
	read data  
	put data to queue  
	notify thread to deal with data  
	again  
  
wait deal signal  
	get data from queue  
	deal data  
	write data  
	again  
  
```  
  
Here we consume that always writable on socket.  
If we need check whether it is writable or not,  
   we should create write thread to  
   check the complete signal and write event   
  
```c++  
wait read events / IO multiplexing  
	read data  
	put data to queue  
	notify deal thread to deal data  
	again  
  
wait deal signal  
	get data from queue  
	deal data  
	notify write thread to write  
	again  
  
  
wait write events and write signal / IO multiplexing  
	get data from queue  
	write data  
	agian  
	  
```  
  
  
## Reactor  
  
We also can put fd, the socket file descriptor, to queue.  
And in deal thread, we do read, deal, write work together.  
  
```c++  
wait read events / IO multiplexing  
	put fd to queue  
	notify actor thread  
	again  
  
wait deal signal  
	get fd from queue  
	read data from fd  
	deal data  
	write data to fd  
	again  
```  
  
Also, we consume it always can be write.  
If not, we can do like this.  
  
```c++  
wait read events / IO multiplexing  
	put fd to queue  
	notify actor thread  
	again  
  
wait deal signal  
	get fd from queue  
	read data from fd  
	deal data  
	put fd and data to queue  
	notify write thread  
	agian  
  
wait write event and write signal  
	get fd and data from queue  
	write data to fd  
	again  
```  
  
Above, do not consider the size of queue,  
	so, we need more code to check and wait if queue is empty or full.  
	We can check whole number in entry,  
    if the processing number greater than Largest, DO NOT ACCEPT NEW REQUEST.  
  
If it is short-connection, we just do one read, one write in a fd, then close it.  
But if it is long-connection, after read one time, we need read more.  
  
~~So, we can continue read in deal thread with timeout-condition~~  
~~if no more data, put it into read-events monitor.~~  
~~then we can in one thread do read and write,~~  
~~after deal, we put the fd and data in queue, to notify RW-thread~~  
~~using a variable to mark its status, to read or to write.~~  
  
More, we donot close the fd actively, it is passive mode.  
But, if client do not close fd, the open fd in server will always there.  
This is a mistake.  
  
So, we need mark each fd,  
	using timer, after each read or deal or write, to start a timer,  
	if a fd's timer timeout, then close it immediately.  
  
Note, we donot open/close/wait-event about fd in deal thread,  
	we deal fd in read events thread and timer thread.  
We update timer after each read and write.  
In deal thread, we just do one-processing, that is to say,  
   we read, deal, write just once, one time.  
   **All above should be consider like this**.    
  
  
## Leader/Follower  
  
Also, we can wait event, read, deal, write in one thread.  
  
If read event coming, then notify another thread to wait new event,    
   and current thread to read, deal, write    
  
  

```c++
wait signal to become leader
	wait read event / IO multiplexing
	set current thread is `leader`
	get the event fd
	notify another to wait
	set current thread is `processing`
	read data
	deal data
	write data
	set current is `follower`
	again
```
 
Question:  
1, If there are event coming when deal thread processing,  
	what problem accur?  
Answer, we should read exactly fixed size data in deal thread.  
then, if new event coming, next deal thread can read correctly.  
and also, if reading error caused by no-enough data,  
	the deal thread should break and wait another event.  
  
2, If no enough data, we can wait, put data to unfinished set, drop data.  
    which method is better?  
Answer, we need to check whick node produces the un-complete data.  
	then, according the more freqency node to deal it.  
	For example, if we find out the client is the node, we just drop it.  
	if we find out the IPC is the node, I donot know how to deal,  
	or we can wait a moment, some milliseconds.  
  
  
  
## Some impossible  
  
### sync and async  
  
#### sync  
in concurrence programming,  
	it means processing sequence is same as code sequence.  
in IO programming,  
	it means the calling thread is blocked until called function return.  
  
#### async  
so, we can get the means according explaination of sync.  
in concurrence programming,  
	the processing sequence is drived by system events.  
in IO programming,  
	the calling thread is unblocked whether the called function return or not.  
    
### context switch  
if we notify another thread, it must do context switch.  
this maybe consume some time.  
In addition with cache data, in HAHS mode.  
  
  
## participant  
### half-sync/half-async  
- IO thread  
- worker thread pool  
- timer thread  
- data queue  
- mutex and conditional variable, for data queue, for fd set  
  
### reactor  
- listener thread  
- worker thread pool  
- timer thread  
- mutex and conditional variable, for fd set  

### leader/follower
- thread pool
- event processor
- worker processor
- fd set
- timer thread  
- mutex, for fd set  



