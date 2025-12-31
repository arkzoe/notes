# 逻辑存储结构
![[存储结构.png]]
# 架构
![[架构.png]]
## 内存结构
- Buffer Pool：缓冲池是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据(若缓冲池没有数据，则从磁盘加载并缓存)，然后再以一定频率刷新到磁盘，从而减少磁盘I0，加快处理速度
	- 以页为单位，底层采用链表
	- free page：未被使用
	- clean page：被使用，数据没被修改
	- dirty page：数据被修改，与磁盘数据冲突

- Change Buffer：更改缓冲区(针对于非唯一二级索引页)，在执行DML语句时，如果这些数据Page没有在Buffer Pool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区Change Buffer中，在未来数据被读取时，再将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中，减少IO

- Adaptive Hash Index：自适应hash索引，用于优化对Buffer Pool数据的查询。InnoDB存储引擎会监控对表上各索引页的查询，如果观察到hash索引可以提升速度，则建立hash索引，称之为自适应hash索引
	- 自适应哈希索引，无需人工干预，是系统根据情况自动完成
	- 参数：adaptive_hash_index

- Log Buffer：日志缓冲区，用来保存要写入到磁盘中的log日志数据(redo log、undo log)，默认大小为16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新、插入或删除许多行的事务，增日志缓冲区的大小可以节省磁盘I/O
	- 参数：
	- innodb_log_buffer_size：缓冲区大小
	- innodb_flush_log_at_trx_commit： 日志刷新到磁盘时机
		- 1：日志在事务每次提交时，写入刷新到磁盘
		- 0：每秒将日志写入并刷新到磁盘
		- 2：日志在事务每次提交后写入，并每秒刷新到磁盘一次
## 磁盘结构
- System Tablespace：系统表空间是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或通用表空间中创建的，它也可能包含表和索引数据。(在MySQL5.x版本中还包含InnoDB数据字典、undolog等)
	- 参数：innodb_data_file_path
- File-Per-Table Tablespaces：每个表的文件表空间包含单个InnoDB表的数据和索引，并存储在文件系统上的单个数据文件中
	- 参数：innodb_file_per_table
- General Tablespaces：通用表空间，需要通过CREATE TABLESPACE 语法创建通用表空间，在创建表时，可以指定该表空间
```mysql
CREATE TABLESPACE xxx ADD
DATABASE 'file_name'--关联表空间
ENGINE = engine_name;
```
- Undo Tablespaces：撤销表空间，MySQL实例在初始化时会自动创建两个默认的undo表空间(初始大小16M)，用于存储undo log日志
- Temporary Tablespaces：InnoDB 使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据
- Doublewrite Buffer Files（dblwr）：双写缓冲区，innoDB引擎将数据页从Buffer Pool刷新到磁盘前，先将数据页写入双写缓冲区文件中，便于系统异常时恢复数据
- Redo Log：重做日志，是用来实现事务的持久性。该日志文件由两部分组成：重做日志缓冲(redo log buffer)以及重做日志文件(redo log)，前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都会存到该日志中，用于在刷新脏页到磁盘时，发生错误时，进行数据恢复使用
## 后台线程
1. Master Thread
	- 核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中，保持数据的一致性，还包括脏页的刷新、合并插入缓存、undo页的回收
2. 10 Thread
	- 在InnoDB存储引擎中大量使用了AIO来处理IO请求，这样可以极大地提高数据库的性能，而IOThread主要负责这些IO请求的回调

| 线程类型                 | 默认个数 | 职责             |
| -------------------- | ---- | -------------- |
| Read thread          | 4    | 负责读操作          |
| Write thread         | 4    | 负责写操作          |
| Log thread           | 1    | 负责将日志缓冲区刷新到磁盘  |
| Insert buffer thread | 1    | 负责将写缓冲区内容刷新到磁盘 |

3. Purge Thread
	- 主要用于回收事务已经提交了的undo log，在事务提交之后，undo log可能不用了，就用它来回收
4. Page Cleaner Thread
	- 协助 Master Thread 刷新脏页到磁盘的线程，它可以减轻Master Thread 的工作压力，减少阻塞
# 事务原理
- undo.log--原子性
- 回滚日志，用于记录数据被修改前的信息，作用包含两个：提供回滚和MVCC(多版本并发控制)。undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚
	- Undo log销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些日志可能还用于MVCC。
	- Undo log存储：undo.log采用段的方式进行管理和记录，存放在前面介绍的rollback segment回滚段中，内部包含1024个undo.log segment.
- redo.log--持久性
	- 重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。该日志文件由两部分组成：重做日志缓冲(redo log buffer)以及重做日志文件(redo log file)，前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中，用于在刷新脏页到磁盘，发生错误时，进行数据恢复使用
	- WAL(Write-Ahead Logging)
- 一致性由原子性、持久性、隔离性三大特性，以及数据库自身的完整性约束，共同协作来实现的
- 隔离性
	- 锁
	- MVCC
# MVCC
- 当前读
读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。对于我们日常的操作，如：
select ... lock in share mode(共享锁)，select ... for update、update、insert、delete(排他锁)都是一种当前读。

- 快照读
- 简单的select(不加锁)就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读。
	- Read Committed：每次select，都生成一个快照读。
	- Repeatable Read：开启事务后第一个select语句才是快照读的地方。
	- Serializable：快照读会退化为当前读。

- MVCC
全称 Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本，使得读写操作没有冲突，快照读为MySQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需要依赖于数据库记录中的三个隐式字段、undo log日志、readView
## 实现原理
1. 隐藏字段

| 隐藏字段        | 含义                                     |
| ----------- | -------------------------------------- |
| DB_TRXND    | 最近修改事务ID，记录插入这条记录或最后一次修改该记录的事务ID       |
| DB_ROLL_PTR | 回滚指针，指向这条记录的上一个版本，用于配合undo log，指向上一个版本 |
| DB_ROW_ID   | 隐藏主键，没有勾选指定主键，生成该隐藏字段                  |
2. undo log
- 回滚日志，在insert、update、delete的时候产生的便于数据回滚的日志
- 当insert的时候，产生的undo log日志只在回滚时需要，在事务提交后，可被立即删除
- 而update、delete的时候，产生的undo log日志不仅在回滚时需要，在快照读时也需要，不会立即被删除
- undo log版本链
	- 不同事务或相同事务对同一条记录进行修改，会导致该记录的undolog生成一条记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录
2. readview
- ReadView(读视图)是快照读SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务(未提交的)id

| 字段             | 含义                        |
| -------------- | ------------------------- |
| m_ids          | 当前活跃事务ID集合                |
| min_trx_id     | 最小活跃事务ID                  |
| max_trx_id     | 预分配事务ID，当前最大事务ID+1（事务ID自增 |
| creator_trx_id | ReadView创建者的事务ID          |
- 版本数据链访问规则
1. 1.trx_id == creator_trx_id?可以访问该版本 -> 成立，说明数据是当前这个事务更改的
2. trx_id<min_trx_id?可以访问该版本 -> 成立，说明数据已经提交了
3. trx_id>max_trx_id?不可以访问该版本 -> 成立，说明该事务是在ReadView生成后才开启
4. min_trx_id <= trx_id <= max_trx_id?如果trx_id不在m_ids中是可以访问该版本的 -> 成立，说明数据已经提交
- 不同的隔离级别，生成Readview的时机不同：
	- READ COMMITTED：在事务中每一次执行快照读时生成ReadView
	- REPEATABLE READ：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView