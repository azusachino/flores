---
title: MySQL实战
created: 2023-02-06 01:34:23
modified: 2023-07-18 13:31:24
tags: [Column, MySQL]
author: 林晓斌
read: 30
total: 45
status: halt
---

**如果内存够，就要多利用内存，尽量减少磁盘访问。**

## 基础知识

![[../../images/mysql-arch.png]]

### 日志系统

- Redo Log - WAL 先写入 redo-log (memory)，避免每次更新写入磁盘造成 I/O 压力过大

**redo-log | bin-log**

1. redo log 是 InnoDB 引擎特有的；binlog 是 MySQL 的 Server 层实现的，所有引擎都可以使用。
2. redo log 是物理日志，记录的是“在某个数据页上做了什么修改”；binlog 是逻辑日志，记录的是这个语句的原始逻辑，比如“给 ID=2 这一行的 c 字段加 1 ”。
3. redo log 是循环写的，空间固定会用完；binlog 是可以追加写入的。“追加写”是指 binlog 文件写到一定大小后会切换到下一个，并不会覆盖以前的日志。

**update 流程**

1. 执行器先找引擎取 ID=2 这一行。ID 是主键，引擎直接用树搜索找到这一行。如果 ID=2 这一行所在的数据页本来就在内存中，就直接返回给执行器；否则，需要先从磁盘读入内存，然后再返回。
2. 执行器拿到引擎给的行数据，把这个值加上 1，比如原来是 N，现在就是 N+1，得到新的一行数据，再调用引擎接口写入这行新数据。
3. 引擎将这行新数据更新到内存中，同时将这个更新操作记录到 redo log 里面，此时 redo log 处于 prepare 状态。然后告知执行器执行完成了，随时可以提交事务。
4. 执行器生成这个操作的 binlog，并把 binlog 写入磁盘。
5. 执行器调用引擎的提交事务接口，引擎把刚刚写入的 redo log 改成提交（commit）状态，更新完成。

#### binlog

事务执行过程中，先把日志写到 binlog cache，事务提交的时候，再把 binlog cache 写到 binlog 文件中。

1. sync_binlog=0 的时候，表示每次提交事务都只 write，不 fsync；
2. sync_binlog=1 的时候，表示每次提交事务都会执行 fsync；
3. sync_binlog=N(N>1) 的时候，表示每次提交事务都 write，但累积 N 个事务后才 fsync。

#### redolog

为控制 redo log 的写入策略，InnoDB 提供了 `innodb_flush_log_at_trx_commit` 参数，它有三种可能取值：

1. 设置为 0 的时候，表示每次事务提交时都只是把 redo log 留在 redo log buffer 中 ;
2. 设置为 1 的时候，表示每次事务提交时都将 redo log 直接持久化到磁盘；
3. 设置为 2 的时候，表示每次事务提交时都只是把 redo log 写到 page cache。

![[../../images/mysql-log-write-sequences.png]]

### 索引

当不使用索引查询数据时，会执行 full table scan (explain type ALL)

- hash index 不适合范围查询
- sorted array 适合固定数据
- B-Tree 综合能力强

主键索引的叶子节点存储的是整行数据，非主键索引的叶子节点存储的是主键的值。【查询两次：回表】

**覆盖索引是指，索引上的信息足够满足查询请求，不需要再回到主键索引上去取数据。**

```sh
select a,b,c from t where a = 'abc' sort by b limit 10;

# 1. alter table t add index a_index(a) => for where
# 2. alter table t add index ab_index(a, b) => 优先全字段排序
# 3. alter table t add index abc_index(a, b, c) => 覆盖索引，无需回表
```

> [!question] MySQL 为什么有时候会选错索引？
> 对于每个索引，依据区分度，会计算出【基数】，这是判断索引效率的基准
>
> 在 MySQL 中，基数是通过采样计算的，即有可能产生问题描述的情况
>
> 1. 采用 force index 强行选择索引
> 2. 修改 SQL 语句，引导 MySQL 使用期望的索引
> 3. 新建更加合适的索引

#### 字符串索引

1. 直接创建完整索引，这样可能比较占用空间；
2. 创建前缀索引，节省空间，但会增加查询扫描次数，并且不能使用覆盖索引；
3. 倒序存储，再创建前缀索引，用于绕过字符串本身前缀的区分度不够的问题；
4. 创建 hash 字段索引，查询性能稳定，有额外的存储和计算消耗，跟第三种方式一样，都不支持范围扫描。

### 锁

- 全局锁：对整个数据库实例加锁 **全局锁的典型使用场景是，做全库逻辑备份。**
- 表级锁
  - `lock tables tbl_sample read/write`
  - MDL (metadata lock) 可能后阻塞后面的读操作，在 `alter table` 时最好加上超时时间
- 行级锁
  - **在 InnoDB 事务中，行锁是在需要的时候才加上的，但并不是不需要了就立刻释放，而是要等到事务结束时才释放。这个就是两阶段锁协议。**

**行锁死锁**

- 一种策略是，直接进入等待，直到超时。这个超时时间可以通过参数 `innodb_lock_wait_timeout` 来设置。
- 另一种策略是，发起死锁检测，发现死锁后，主动回滚死锁链条中的某一个事务，让其他事务得以继续执行。将参数 `innodb_deadlock_detect` 设置为 on，表示开启这个逻辑。

### 事务隔离

- 一致性读 【row tx_id】
  - `select k from t where id = 1` 仅能读取可见最新版本数据
- 当前读：**更新数据都是先读后写的，而这个读 (最新 row tx_id 版本)，只能读当前的值，称为“当前读”（current read）。**
  - `update t set k = k + 1 where id = 1`
  - `select k from t where id = 1 lock for share mode`
  - `select k from t where id = 1 for update`

**可见性**

1. 版本未提交：不可见
2. 版本已提交，但在视图创建后提交：不可见
3. 版本已提交，在视图创建前提交：可见

> begin/start transaction 命令并不是一个事务的起点，在执行到它们之后的第一个操作 InnoDB 表的语句，事务才真正启动。如果你想要马上启动一个事务，可以使用 start transaction with consistent snapshot 这个命令。

可重复读的核心就是一致性读（consistent read）；而事务更新数据的时候，只能用当前读。如果当前的记录的行锁被其他事务占用的话，就需要进入锁等待。

- 对于可重复读，查询只承认在事务启动前就已经提交完成的数据；
- 对于读提交，查询只承认在语句启动前就已经提交完成的数据；

### 脏页 flush

`innodb_io_capacity` **当前系统的 io 处理能力，直接影响 mysql 的脏页 flush 效率**

1. redo log 写满：MySQL 会停止所有的更新操作，把 checkpoint 往前推进， redo log 才可以继续写入数据
2. 系统内存不足：当需要新的内存页、而内存不够用的时候，需要淘汰一些数据页，空出内存；如果淘汰的是脏页，需要先进行 flush
3. 系统空闲时
4. 系统正常关闭

### 表空间

`innodb_file_per_table` 控制表数据存放在共享表空间，还是单独的文件。**ON 表示每个 InnoDB 表数据存储在 `*.idb` 文件中** 通过 `drop table` 命令可以直接删除该表文件。

> [!question] 为什么表数据删掉一半，表文件大小不变？
> InnoDB 是按页存储的，如果删除一整个数据页的记录，整个数据也就可以被复用了；问题出在，数据页的复用与记录的复用是不同的。
>
> **delete 命令其实只是把记录的位置，或者数据页标记为了“可复用”，但磁盘文件的大小是不会变的。**
>
> 重建表：`alter table t engine=innodb,ALGORITHM=inplace`

### `count (*)`

- MyISAM 虽然 `count(*)` 很快，但不支持事务【额外存储在指定位置】
- `show table status` 命令很快，但是不准确
- InnoDB `count(*)` 时会遍历全表，有性能损耗

## 实践建议

1. 避免使用 `order by rand()`，会使用 using temporary 和 using filesort，查询代价巨大。
2. 如果对字段做了函数计算，就用不上索引了，这是 MySQL 的规定。
3. 隐式类型转换会导致全表扫描。【在 MySQL 中，字符串和数字做比较的话，是将字符串转换成数字。】
4. 隐式字符编码【utf8，utf8bm4】转换会导致全表扫描。

> [!question] 如果你的 MySQL 现在出现了性能瓶颈，而且瓶颈在 IO 上，可以通过哪些方法来提升性能呢？
>
> 1. 设置 binlog_group_commit_sync_delay 和 binlog_group_commit_sync_no_delay_count 参数，减少 binlog 的写盘次数。这个方法是基于“额外的故意等待”来实现的，因此可能会增加语句的响应时间，但没有丢失数据的风险。
> 2. 将 sync_binlog 设置为大于 1 的值（比较常见是 100~1000）。这样做的风险是，主机掉电时会丢 binlog 日志。
> 3. 将 innodb_flush_log_at_trx_commit 设置为 2。这样做的风险是，主机掉电的时候会丢数据。

## References

- [MySQL 实战 45 讲 - 极客时间](http://localhost)
