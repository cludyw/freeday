---
author: cludyw
title: Mysql 死锁排查常用命令 
date: 2019-12-18T15:57:07+08:00
draft: false
description: "mysql 死锁常用命令"
tags:
    - dealock
    - mysql
categories:
    - mysql
---

### 查看最后一次死锁信息

```mysql
show engine innodb status;
```

### 通过查看错误日志排查

#### 查看是否开启错误日志
```mysql
show variables like 'innodb_print_all_deadlocks';
```
#### 查看日志路径
```mysql
show variables like 'log_error'
```
如果为stderr则输入到console, 可在启动时指定--log-error或配置/etc/mysql/my.cnf中log_error参数指定错误日志路径

### 查看表锁
```mysql
show OPEN TABLES where In_use > 0;
```
![](/img/EAD1977E-EB64-4B3A-91F2-31B1F39FC60C.png)

### 查看事务列表
```mysql
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;
```

#### 查看线程sql执行历史
```mysql
SELECT * FROM performance_schema.events_statements_history WHERE thread_id = (SELECT THREAD_ID FROM performance_schema.threads WHERE PROCESSLIST_ID = 11062)
```

### 查看线程列表
```mysql
SHOW PROCESSLIST;
```

### mysql8.0.1版本后可以performance_schema.data_locks查看锁
```mysql
SELECT * from performance_schema.data_locks;
```


