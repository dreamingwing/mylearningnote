## 事务

+ ACID

  + 原子性(Atomicity)
  + 一致性(Consistency)
  + 隔离性(Isolation)
  + 持久性(Duration)

+ 事务带来的问题

  + 更新丢失(lost update)->读未提交解决

    两个事务在同时更新一行数据，第一个事务更新的结果会被第二个事务更新的结果覆盖

  + 脏读(dirty read)->读提交解决

    一个事务读取了另外一个事务还未提交的修改

  + 不可重复读(non-repeatable read)->不可重复读解决

    一个事务内多次读取同一数据，在读的间隔中另外的事务修改了这个值，这就出现了两次读的数据不一样

  + 幻读(phantom read)->串行化解决

    事务1读取了一批数据，事务2插入了几条数据，事务1用同样的条件读取，发现多了几条新插入的数据。

+ 事务隔离级别

  + 读未提交(read unommitted)

    事务未提交时，变更就能被别的事务看到

  + 读提交(read committed)-oracle默认

    事务提交以后，变更才能被别的事务看到

  + 可重复读(repeatable read)-mysql默认

    事务执行的过程中看到的数据，总是跟事务在启动过程中看到的数据时一致的

  + 串行化(serializable)

    对于同一行记录，写会加写锁，读会加读锁

  实现上，事务隔离级别时通过创建视图，访问的时候以视图的数据为准。可重复读会在事务启动的时候就创建视图，整个事务期间都使用这个视图。读提交是在每条sql执行的时候创建视图。读未提交，直接读最新记录，不使用视图。串行化是通过加锁的方式。

  每个视图其实是通过undo log来实现的。假设讲某个值从1按照顺序设置到2、3、4，undo log里面会有如下

  ```mermaid
  graph RL
  	subgraph 回滚段
  		将4改成3-->将3改成2
  		将3改成2-->将2改成1
  	end
  	当前值4-->将4改成3
  	视图c.->当前值4
  	视图b.->将3改成2
  	视图a.->将2改成1
  	
  	
  ```

  这个图中，视图a，b，c读到的值分别是1，2，4

+ MVCC

  MVCC(multi version concurrency control)是MySQL中实现事务隔离、解决读写冲突的手段。

  每条行记录中，除了数据以外，InnodDB还会添加以下三行数据

  + DATA_TRX_ID

    最近更新这行记录的事务id

  + DATA_ROLL_PTR

    表示指向改行回滚段(rollback segment)的指针

  + DB_ROW_ID

    没有定义主键的表会存在这一列，表示ID

  通过DATA_ROLL_PTR，就能形成一条undo log链，通过这个链，能快速的查找到之前的版本。

  事务启动的时候，InnoDB会按照递增的方式，分配一个唯一的事务id。同时会构建一个数组来保存当前活跃的所有事务ID，这个数据就构成了一致性视图(read view)。数据能够读取到哪个版本，就是通过这行数据的事务id和这个一致性视图来判断。这个视图可以理解为事务启动时候，整个数据库的快照。

  read view里面最新的事务id是低水位，最大的事务id是高水位。

  ```mermaid
  graph LR
  	submitted_transaction[已提交事务]
  	subgraph 未提交事务
  		低水位-->高水位
  	end
  	not_started_transaction[未开始事务]
  	submitted_transaction-->低水位
  	高水位-->not_started_transaction
  ```

  这个read view把数据的事务id分成了以下几类

  1. 小于低水位的，就是已提交的，这个版本是可见的
  2. 大于高水位的，是未开始的，这个版本是不可见的
  3. 大于低水位小于高水位，在read view当中，证明事务启动的时候，这个事务没提交，不可见
  4. 大于低水位小于高水位，不在read view当中，证明事务启动的时候，这个事务已经提交了，可见

  对于写操作，实际是都是执行了先读后写的操作，而这个读的操作，只能读当前的值，这称为当前读。select操作如果加了锁，也都是执行当前读

  ```sql
  select k from t where id=1 lock in share mode;--读共享锁
  select k from t where id=1 for update; --写锁
  ```

  

  