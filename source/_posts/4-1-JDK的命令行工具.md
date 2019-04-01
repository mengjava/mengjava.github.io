---
title: 4.1 JDK的命令行工具
date: 2019-04-01 11:31:35
categories: 
- JVM读书笔记
tags:
- 命令行工具
---

## jps:虚拟机进程状态工具
> **jps(JVM Process Status Tool)** 可以列出正在运行的虚拟机进程，并显示虚拟机执行主类名称以及这些进程本地虚拟机唯一ID。

jps可以通过RMI协议查询开启了RMI服务的远程虚拟机进程状态,hostid为RMI注册表中注册的主机名。
<!--more-->
>
* 命令1：jps -l：输出主类的全名，如果进程执行的是Jar包，输出Jar路径：
* 命令2：jps -v：输出虚拟机进程启动时JVM参数：
* 命令3：jps -m：输出虚拟机进程启动时传递给主类main()函数的参数：
* 命令4：jps -q：只输出LVMID，省略主类的名称：
    ![2.png](https://i.loli.net/2019/04/01/5ca1880bc3dc8.png)


## jstat: 虚拟机统计信息监视工具
> **jstat(JVM Statistics Monitoring Tool)** 是用于监视虚拟机各种运行状态的命令行工具。它可以显示本地或者远程虚拟机进程中的类装载、内存、垃圾收集、JIT便建议等运行数据，他是运行期定位虚拟机性能问题的首选工具。

**使用jstat工具在纯文本状态下监视虚拟机不如VisualVm等可视化工具直接一图表展示的那么直观。**


* 命令1：jstat -gc 13991 200 5：每200毫秒查询一次进程2764垃圾收集状况，一共查询5次
* 命令2：jstat -gcutil 13991 200 5：监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比： 
 ![3.png](https://i.loli.net/2019/04/01/5ca18869b0be3.png)


## jinfo:Java配置信息工具
**jinfo(Configuration Info for Java)** 的作用是实时地查看和调整虚拟机各项参数。使用jps命令订单-v参数可以查看虚拟机启动时显式制定的参数列表，但是如果想知道未被显式指定的参数的系统默认值，除了找资料外，就只能使用jinfo的-flag选项进行查询了，还可以使用-sysprops选项吧虚拟机进程的System.getProperties()的内容打印出来。
*  命令1：jinfo -flag CMSInitiatingOccupancyFraction 13991：查询CMSInitiatingOccupancyFraction参数值。 

## jmap:Java内存映像工具
 
**jmap(Memory Map for Java)** 命令用于生成堆转储快照（一般称为heapdump或dump文件）。如果不使用jmap命令，要想获取Java堆转储快照，还有一些比较“暴力”的手段：譬如通过-XX:+HeapDumpOnOutOfMemoryError参数，可以让虚拟机在OOM异常出现之后自动生成dump文件；通过-XX:+HeapDumpOnCtrlBreak参数则可以使用[Ctrl] + [Break]键让虚拟机生成dump文件；或者在Linux系统下通过kill -3命令发送进程退出信号“吓唬”一下虚拟机，也能拿到dump文件。
> **作用:** 
* 1 生成堆转储快照
* 2 查询finalize执行队列、Java堆和永久代的信息，如空间使用率，当前使用的收集器。 


![4.png](https://i.loli.net/2019/04/01/5ca18869911d4.png)


* **命令1：jmap -dump:format=b,file=idea.bin 7996**




## jhat：虚拟机堆转储快照分析工具

Sun JDK提供jhat（JVM Heap Analysis Tool）命令与jmap搭配使用，来分析jmap生成的堆转储快照。jhat内置了一个微型的HTTP/HTML服务器，生成dump文件的分析结果后，可以在浏览器中查看。不过实事求是地说，在实际工作中，很少使用jhat命令来分析dump文件，主要原因有二：
> （1）一般不会在部署应用程序的服务器上直接分析dump文件，即时可以这样做，也会尽量将dump文件复制到其他机器上进行分析，因为分析工作是一个耗时而且消耗硬件资源的过程；
> （2）jhat命令分析功能相对来说比较简陋。

* 命令1：jhat idea.bin
![5.png](https://i.loli.net/2019/04/01/5ca1886879095.png)

* 在浏览器输入 http://localhost:7000/
![6.png](https://i.loli.net/2019/04/01/5ca1886993819.png)


## jstack:Java堆栈跟踪工具

**jstack（Stack Trace for Java)** 令用于生成虚拟机当前时刻的线程快照。线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合.

生成线程快照的目的主要是定位线程长时间出现停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等都是导致线程长时间停顿的原因。线程出现停顿的时候通过jstack来查看各个线程的调用堆栈，就可以知道没有响应的线程到底在后台做些什么事情，或者在等待些什么资源。
* 命令1 jstack -l 7996

> JDK1.5中,java.lang.Thread类新增了一个getAllStackTraces()方法用于获取虚拟机所有线程的StackTraceElement对象。通过这个方法可以简单代码完成jstack的大部分功能，可以做个管理界面用于查看线程堆栈。




