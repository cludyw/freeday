+++
author = "cludyw"
title = "JAVA GC 调优常用命令"
date = "2019-11-20"
description = ""
tags = [
    "java",
    "gc",
]
categories = [
    "java"
]
+++

### 打印线程信息
```sbtshell
ps -mp pid -o THREAD,tid,time 
```

-m 代表打印线程信息
-o 输出格式

格式：args, cmd, comm, command, fname, ucmd, ucomm, lstart, bsdstart, start

### 打印所有进程
```sbtshell
ps –ef 或 ps aux 
```
推荐ps -ef 因为aux会截断显示command

### 打印线程堆栈信息 
10进制转16进制
```sbtshell
printf "%x\n" tid
```

```sbtshell
jstack pid | grep tid -A 30
```
tid为16进制

-A 代表行数


### 解决jmap:Operation not permitted
```sbtshell
echo 0 > /proc/sys/kernel/yama/ptrace_scope
```

永久更改：
```sbtshell
vi  /etc/sysctl.d/10-ptrace.conf 

kernel.yama.ptrace_scope = 0 
```

### GC日志参数
- XX:+PrintGC：输出GC日志
- XX:+PrintGCDetails：输出GC的详细日志
- XX:+PrintGCTimeStamps：输出GC的时间戳（以基准时间的形式）
- XX:+PrintGCDateStamps：输出GC的时间戳（以日期的形式，如2018-08-29T19:22:48.741-0800）
- XX:+PrintHeapAtGC：在进行GC的前后打印出堆的信息
- Xloggc:gc.log：日志文件的输出路径

- XX:+HeapDumpOnOutOfMemoryError：发生内存溢出时生成heapdump文件
- XX:HeapDumpPath：指定heapdump文件

### 查看jvm 堆的情况
```sbtshell
jstat -gc pid 1000
```
1000代表每隔1000毫秒打印一次

### dump jvm堆的信息
```sbtshell
jmap -dump:live,format=b,file=dump.hprof pid
```
