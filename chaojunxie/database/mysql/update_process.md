## 数据写入流程

数据写入的整体流程，涉及到redo log，change buffer，undo log，整体流程如下

1. 执行器调用InnoDB接口写入数据
2. 如果对应的数据页在内存(buffer pool)中，InnoDB会直接更新内存中的数据页(这样的和磁盘中不一样的数据页称为脏页)
3. 如果数据页不在内存中，InnoDB会把操作缓存到change buffer当中
4. 写入redo log和undo log
   1. InnoDB把操作写入到redo log(这样先写入日志，然后再同步到磁盘的技术，成为write ahead logging-WAL)
   2. InnoDB产生对应的undo log(用于MVCC)
      1. 对这条记录加上排它锁
      2. 把这行记录整行拷贝到undo log
      3. 修改这行记录的DATA_TRX_ID为当前事务ID
      4. 修改这行记录的DATA_ROLL_PTR，指向刚拷贝到undo log的那行
   3. 这个时候redo log处于prepare状态
5. 执行器把操作写入到binlog。
6. 执行器调用InnoDB接口提交事务
7. InnoDB将redo log的状态更新为commit(二阶段提交)
8. Change buffer中的操作，会在数据页被读入到内存中的时候，同步到实际的数据页(位于buffer pool中的实际数据页面)，这个动作称为merge。InnoDB也会周期性的触发merge动作。
9. 第2和第8步骤中产生的脏页，会在合适的时候触发刷脏页动作，把正确数据写入到磁盘，这个动作叫做purge或者flush。触发的时机包括：
   + Redo log被写满了。这个场景MySQL会处于无法更新数据的状态
   + Buffer pool满了，没法加载新的数据页
   + MySql在不忙的时候
   + MySql正常关闭

