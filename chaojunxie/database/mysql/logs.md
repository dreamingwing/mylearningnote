## 日志

+ Redo log

  redo log是InnoDB独有的。当执行一条更新操作的时候，InnoDB会先把记录写入到redo log，同时更新内存记录。在系统空闲的时候，InnoDB才会把记录更新到磁盘当中。这样的技术称为WAL(write ahead logging)。Redo log使得InnoDB实现了crash-safe。

  Redo log的文件大小是固定的，所以需要重复使用。

+ binlog

  binlog位于server层。只能用于归档，不提供crash-safe

  + Binlog 格式

    1. statemeng格式

       statement格式里面，binlog记录的是sql语句的原文。

       优点：binlog比较小

       缺点：可能会出现主备数据不一致的情况

    2. row格式

       row格式里面，binlog记录的时候实际操作的是哪一行，进行什么操作。

       优点：准确修改到哪一行，不会出现错误

       缺点：binlog占用会比较多

    3. mixed格式(statement+row)

       MySql自己判断是否会出现主备不一致的，如果会出现不一致，就用row模式，不会出现，就使用statement模式

+ binlog vs redo log

  1. Redo log为InnoDB独有，binlog位于server层面
  2. Redo log是物理日志，记录了例如哪个数据页做了什么操作。binlog记录的是原始的逻辑，例如ID=2这行的某字段+1
  3. Redo log是有限的，循环写的。binlog是追加的，文件大小达到一定程度以后，会再写入另外一个。

+ Undo log

  每条更新操作，都会在undo log里面产生一条记录，这个记录是记录怎么回滚到之前状态，例如对于将某字段记录重1设置到2，会记录为将2改成1。

+ Redo log vs undo log

  1. redo是物理上的，undo log 是逻辑上的
  2. undo log会在不存在需要回滚到更早之前的视图的时候，会被删除

+ slow query log

+ errorlog

+ general log

+ relay log