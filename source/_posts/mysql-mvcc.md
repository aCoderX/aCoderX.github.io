---
title: mysql mvcc
date: 2019-12-12 20:28:04
tags: mysql
---

### 介绍
多版本控制，innodb支持
### 解决的问题
* 读操作不需要加锁，为了让读写不冲突。最早的数据库系统，只有读读之间可以并发，引入MVCC之后，只有写写之间相互阻塞，其他三种操作都可以并行
* MVCC解决了快照读的幻读问题

 <!--more-->

### 存储结构
在每行的存储中，会多出四列存储，存在聚簇索引中
* data_trx_id: 该行最近修改的事务id
* data_roll_ptr 回滚指针，只想undo log中的上次修改前的历史数据
* db_row_id : 如果没有规定主键，默认的主键
* delete_bit ：是否删除

### MVCC工作流程
场景：将id=1的数据的a字段，从10设置为20
1. 给id=1的行记录加上排他锁
2. 将原本的行拷贝到undo_log中，其中data_trx_id和data_roll_ptr不动
3. 修改该行的值，更新data_trx_id为修改记录的事务id，将data_roll_ptr指向刚刚undo log链中的旧版本记录，这样undo log就会形成一个链表
4. 记录redo log,包括undo log中的修改

### 一致性读的原理-ReadView
#### 介绍
> InnoDB支持MVCC多版本，其中RC（Read Committed）和RR（Repeatable Read）隔离级别是利用consistent read view（一致读视图）方式支持的。 所谓consistent read view就是在某一时刻给事务系统trx_sys打snapshot（快照），把当时trx_sys状态（包括活跃读写事务数组）记下来，之后的所有读操作根据其事务ID（即trx_id）与snapshot中的trx_sys的状态作比较，以此判断read view对于事务的可见性。

#### ReadView结构
* up_limit_id：未提交并活跃的事务中最小的XID
* low_limit_id：**所有已提交事务中最大的XID，加1**
* low_limit_no：最小活跃事务id，用于purge去清除已删除记录
* rw_trx_ids：读写事务数组，所有活跃的事务id列表

#### 流程
RR和RC的判断是否可见的逻辑相同，区别在于RR级别下，是第一条select语句执行时，生成的ReadView，后续的所有select都复用这个ReadView；而RC级别下，每一次select都会去生成一次ReadView。

##### 判断是否可见逻辑
1. 如果被访问数据的trx_id小于up_limit_id，表示该记录最后一次修改是在read_view创建之前，可见
2. 如果trx_id大于等于low_limit_id，表示该记录的最后一次修改是在read_view创建之后，不可见，需要通过roll_ptr指针查询历史数据，然后再根据历史数据的trx_id继续计算可见性
3. 如果trx_id落在[up_limit_id,low_limit_id)，需要在活跃读写事务组里查找trx_id，如果存在则不可见；如果不存在，则表示已经提交，可见
4. 得到该条记录的可见结果之后，如果该记录的delete_flag为true，则说明记录已被删除，不返回。否则正常返回

#### 举例
假设id=1的记录trx_id为10，原本的age为18，（19为之前最后一次提交的事务id）

事务20 | 事务21
---|---
begin; |  
1.select age from test where id=1; (result=18)|  
'' | 2.begin;update test set age = 23 where id=1;commit;
3.select (result=23) | 

##### RC
1. 生成ReadView,up_limit_id=20，low_limit_id=19+1，rw_trx_ids为[20]，查询时发现10<20，所以查到数据18
2. 开启事务21修改age为23并提交。更新id=1的数据的trx_id=21
3. 由于是RC重新生成ReadView，up_limit_id=20，low_limit_id=21+1，rw_trx_ids为[20]，发现 20(up_limit_id)<=21(trx_id)<22(low_limit_id),所以可见

##### RR
1. 生成ReadView,up_limit_id=20，low_limit_id=19+1，rw_trx_ids为[20]，查询时发现10<20，所以查到数据18
2. 开启事务21修改age为23并提交。更新id=1的数据的trx_id=21
3. 由于是RR使用原本的ReadView，发现21(trx_id)>=20(low_limit_id),所以不可见


### 二级索引和mvcc
#### 二级索引结构
不像聚簇索引增加了四列，只增加了
* delete_bit ：删除状态
* db_max_id : 修改索引页的最大事务id

#### 流程
* 当update涉及到二级索引的键值时，将旧的索引del设置成true，创建一条新的二级索引即可
* 但是这样，就会导致查询的时候会查出多条二级索引，并且无法判断该条数据的可见性，就需要把多条的二级索引都回到聚簇索引中判断可见性
* 这时候，db_max_id的功能就显示出来了，db_max_id<up_limit_id，则表示在ReadView创建前被修改的，所以是可见的；否则就需要回到聚簇索引中去判断可见性

### 为什么说mvcc不能解决幻读
mvcc只是解决的快照读的幻读问题。Mysql官方给出的幻读解释是：只要在一个事务中，第二次select多出了row就算幻读。
#### 快照读
MVCC的读取方式
#### 当前读
读取最新的数据版本，并且对读取的记录加锁, 阻塞其他事务同时改动相同记录
```sql
　　select...lock in share mode (共享读锁)
　　select...for update
　　update , delete , insert
```
#### 举例1
RR级别，假设刚开始表里只有id=1 一条数据

事务20 | 事务21
---|---
begin; |  
1.select * from test; (只有一条数据)|  
'' | 2.begin;insert into test value (2,23);commit;
3.select * from test;（只有一条数据） | 
4.select * from test where id =2 for update;（能够查出条数据） | 

因为4是当前读，读取的是最新的记录

#### 举例2
RR级别，假设刚开始表里只有id=1 一条数据

事务20 | 事务21
---|---
begin; |  
1.select * from test where id >0; (只有一条数据)|  
'' | 2.begin;insert into test value (2,23);commit;
3.select * from test where id >0;（只有一条数据） | 
4.update test set age=25 where id =2;（修改成功） | 
5.select * from test where id >0;（会查出两条数据） | 

由于4修改了id=2的数据的trx_id，所以在5中发现trx_id=当前事务id，所以能够查到

#### 建议

##### 在快照读读情况下，mysql通过mvcc来避免幻读。

##### 在当前读读情况下，mysql通过next-key来避免幻读。
在刚刚举例2中，应该在1select的时候直接加上next-key for update,进入当前读，这样就没有事务21去插入的问题了

### 参考
* https://zhuanlan.zhihu.com/p/64576887
* http://mysql.taobao.org/monthly/2017/10/01/
* http://mysql.taobao.org/monthly/2018/03/01/
* https://www.cnblogs.com/stevenczp/p/8018986.html