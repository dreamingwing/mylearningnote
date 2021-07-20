## zookeeper

+ 特点

  + 顺序一致性

    同一个客户端发起的事务请求，最终都会严格按照其发起顺序被应用到zk中去(所有写入动作都是到了leader，从而保证了顺序一致性)

  + 原子性

    所有事务请求的处理结果在整个集群中所有机器上的应用情况是一致的

  + 单一视图(single system image)

    无论客户端连接的是哪个zk服务器，看到的数据都是一致的

  + 实时性

    zk只是保证客户端最终一定能够从服务器啥概念读取到最新的数据

+ 集群节点角色

  + Leader

    1. 处理事务性请求
    2. 保证事务的顺序性
    3. 维护集群状态

  + Follower

    1. 处理客户端的非事务性请求，并且把事务性请求转发给leader
    2. 参与到选举投票

  + Observer

    和follower类似，但是不参与到选举

+ 数据节点(ZNode)

  zk的数据节点构建起类似于linux文件这个这样的路径。

  每个事务性的操作都会有个唯一的事务id，而且这个事务id会记录在zode上面

  + 节点类型

    1. 持久节点

       创建后会一直存在，不会被清除

    2. 临时节点

       创建阶段的回话断开以后节点会被删除

    3. 持久顺序节点

       带序号的持久节点

    4. 临时顺序节点

       带序号的临时节点

  + watcher机制

    zk中，客户端对节点的监控，都是通过watcher机制来实现的。

    ```mermaid
    graph LR
    	subgraph client_side
    		client[client]
    		ClientWatcherManger[ClientWatcherManager]
    		client-.2.储存Watcher.->ClientWatcherManger
    		client-.4.执行对应watcher的process方法.->ClientWatcherManger
    	end
    	client-.1.注册监听器.->zookeeper
    	zookeeper-.3.触发事件.->client
    ```

  + 节点数据版本

    没个节点都会有个版本，这个版本的作用，主要是在写入操作的时候进行CAS判断。

+ 使用场景

  + 配置同步

    把配置信息写到zk的节点，各个相关的应用监听这个节点。当这个节点的值发现变化的时候，zk会通知监听所有监听的应用，然后各个应用主动拉取节点的数据，更新配置

  + 主节点选举(kafka使用这个模式)

    1. 第一个客户启动，往zk写入个临时节点，成功写入，成为主节点
    2. 其余的客户启动的时候，同样尝试写入这个节点，失败。然后监听这个节点
    3. 主节点离线，zk会通知所有的监听节点的客户端。然后所有的客户端都会尝试写入临时节点，成功写入的会成为主节点

  + 分布式锁

    核心是使用了zk临时顺序节点

    1. 写入临时顺序节点
    2. 判断自己写入的节点是不是当前需要最小的节点，如果是，就成功获得锁
    3. 如果不是，就监听比自己需要小的节点，这个节点被删除了就能成功获得锁

  + 分布式id生产

    通过顺序临时节点的序号来产生分布式id

+ ZAB协议

  ZAB协议是zk中实现数据一致性的协议，这个协议核心可以分为两部分：消息广播和崩溃恢复

  + 消息广播

    1. leader节点对事务性操作生成对应的proposal，并且生产一个全局唯一的事务id
    2. leader会为每个follower分配一个队列，广播proposal就是把写入到各个follower的队列当中，后台线程会把队列中的proposal分别发给不同的follower
    3. follower接收到proposal，会以事务日志的形式写入到本地磁盘，然后返回ack给leader
    4. 半数follower返回ack后，leader会广播commit消息
    5. follower节点接收到commit消息以后，提交

  + 崩溃恢复

    1. leader选举

       选举的核心是比较选片，每个选票的形式都是<机器id，最大事务id>。每个机器首先会比较最大的事务id，然后回比较机器id。当其中一台机器得到过半数的选票，就是新的leader

    2. 同步数据

       新的leader节点会把没同步的事务，同步到follower

+ 
