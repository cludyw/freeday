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
例如:

```
   Database: business_service
      Table: app
     In_use: 1
Name_locked: 0
```

### 查看事务列表
```mysql
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;
```
例如:
```
                    trx_id: 281479823969856
                 trx_state: RUNNING
               trx_started: 2019-12-18 16:29:51
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 1
       trx_mysql_thread_id: 11062
                 trx_query: NULL
       trx_operation_state: NULL
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 1
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 0
         trx_rows_modified: 0
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
```
#### 查看线程sql执行历史
```mysql
SELECT * FROM performance_schema.events_statements_history WHERE thread_id = (SELECT THREAD_ID FROM performance_schema.threads WHERE PROCESSLIST_ID = 11062)
```
例如:
```
              THREAD_ID: 11102
               EVENT_ID: 174
           END_EVENT_ID: 174
             EVENT_NAME: statement/sql/show_warnings
                 SOURCE: init_net_server_extension.cc:95
            TIMER_START: 2614085239021000000
              TIMER_END: 2614085239076000000
             TIMER_WAIT: 55000000
              LOCK_TIME: 0
               SQL_TEXT: SHOW WARNINGS
                 DIGEST: 118d653c380580e38366e6c2f770958c117ff4937f4a30ea3b324cc43674dc3e
            DIGEST_TEXT: SHOW WARNINGS
         CURRENT_SCHEMA: business_service
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: NULL
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 0
          ROWS_EXAMINED: 0
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: 170
     NESTING_EVENT_TYPE: TRANSACTION
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 528738
```

### 查看线程列表
```mysql
SHOW PROCESSLIST;
```
例如:
```
     Id: 11266
   User: root
   Host: localhost:61576
     db: paypal_service
Command: Sleep
   Time: 106
  State:
   Info: NULL
```

### mysql8.0.1版本后可以performance_schema.data_locks查看锁
```mysql
SELECT * from performance_schema.data_locks;
```
例如:
```
              ENGINE: INNODB
       ENGINE_LOCK_ID: 4847259200:2044:140244260341224
ENGINE_TRANSACTION_ID: 281479823969856
            THREAD_ID: 11102
             EVENT_ID: 171
        OBJECT_SCHEMA: business_service
          OBJECT_NAME: app
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: NULL
OBJECT_INSTANCE_BEGIN: 140244260341224
            LOCK_TYPE: TABLE
            LOCK_MODE: S
          LOCK_STATUS: GRANTED
            LOCK_DATA: NULL
```

