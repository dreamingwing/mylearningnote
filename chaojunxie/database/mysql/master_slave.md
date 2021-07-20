## 高可用

MySql的高可用、主备同步等，都是基于binlog。

+ 主备同步

  ```mermaid
  graph TD
  	subgraph master
  		master_binlog[master binlog]
  	end
  	subgraph slave
  		io_thread[io_thread:维持和master的链接]
  		sql_thread[sql_thread]
  		worker1[worker1]
  		worker2[worker2]
  		worker3[worker3]
  		relay_log[relay_log]
  		relay_log-.读取.->sql_thread
  		store_data[实际数据存]
  		slave_binlog[slave binlog]
  		sql_thread-.分派.->worker1
  		sql_thread-.分派.->worker2
  		sql_thread-.分派.->worker3
  		store_data-.写入.->slave_binlog
  		worker1-.写入.->store_data
  		worker2-.写入.->store_data
  		worker3-.写入.->store_data
  	end
  	master_binlog-.同步.->relay_log
  ```

+ 双master情况下binlog循环复制问题

  很多场景下，使用的是双M模式部署，也就是两台机器都是master，互相做主备切换。

  这样就可能出现binlog循环的问题。解决这个问题的方法就是，每台机器的server id必须不一样。binlog需要把server id传进去。备库生成的binlog里面也会写入本来的server id。当判断到server id和自己的server id一致，就不执行。

+ 主备切换方案(双M模式下)

  + 可靠性优先策略

    1. 检查从库的seconds_behind_master，如果小于一定的值就进入第二步，否则一直循环
    2. 主库进入read-only状态
    3. 循环检查从库seconds_behind_master，直到为0
    4. 从库进入到可读可写状态
    5. 把请求切换到从库

    这个策略的优点是，数据一直是可靠的，不会出现数据丢失或者主备不一致的情况。但是，在主库和备库都处于read only状态的时候，是不可用的，而且这个不可用时间，也是没法控制的。

  + 可用性优先策略

    TODO

+ 读写分离防延迟方案

  + 强制走主库

    对于实时性要求高的查询，走主库；实时性要求低的查询，走从库

  + sleep方案

    主库更新后，从库读的过程先sleep一下

  + 判断主备无延迟方案

  + 配合semi-sync方案

  + 等主库位点方案

  + 等GTID方案

+ 