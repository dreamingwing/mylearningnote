## kafka

+ 特性

  1. 高吞吐、低延时
  2. 可扩展：支持热扩展，轻松水平扩展
  3. 持久化、可靠性：消息持久化到磁盘
  4. 使用发布订阅模式：生产者写入指定topic的消息，消费者订阅topic来消费消息

+ 使用场景

  + 消息队列
  + 数据采集，包括：用户行为采集、日志采集等

+ 引入消息队列带来的问题

  1. 系统复杂增加(重复消费、消息丢失、顺序消费)
  2. 数据一致性
  3. 可用性

+ 基本架构

  从基本架构层面来说，一个kafka集群是由多个broker构成的。一个broker可以单独部署在一台机器上面，也可以多个broker部署在同一台机器(每个broker需要监听不同的端口，例如一个监听9092，一个监听9093)。一般在生产环境下，都会分别部署在不同的机器上。

  集群中的其中一个broker会被选择成为这个集群的controller。controller需要承担以下的任务

  + 选择分片leader
  + 分区到broker的分配
  + 监控broker状态
  + 管理分区状态和副本状态

+ kafka与zk的关系

  kafka使用zk来进行元数据的存储以及通过zk临时节点的特性来进行broker状态的监控

  + broker启动以后，会通过向zk的/brokers/ids/{broker的id}写入临时节点的方式来进行注册，该节点包含了如下内如：

    ```json
    {"listener_security_protocol_map":{"PLAINTEXT":"PLAINTEXT"},"endpoints":["PLAINTEXT://10.16.13.32:9092"],"jmx_port":-1,"host":"10.16.13.32","timestamp":"1589503263654","port":9092,"version":4}
    ```

    kafka的其他组件能通过这个这个节点来获取到改broker的情况。

  + 每个broker启动以后，都会尝试创建/controller这个临时节点，如果能创建成功，这个broker就会成为controller（正常情况只有是第一个启动的broker会创建个成功，别的都会失败，因为节点已经存在）。同时所有的broker都会监控这个节点。当监控到这个临时节点被删除，所有的都会再次尝试去创建这个节点，创建成功的broker就会成为新的controller。新的controller被选出来以后，/controller_epoch永久节点的值会+1，当一个broker接收到来自controller请求里面的epoch值小于这个节点当前的值，就意味着这是个过期的请求。

  + 当controller发现一个broker离开了集群（通过监控zk中对应的临时节点），会对在分区leader位于这个broker内的分区进行新的leader选着，这个过程结束后，会把结果发送给所有的broker。

  + 当新的broker加入的时候（通过监控zk中对应的临时节点），controller也会把消息发送给到所有的broker

+ 主题、分区与副本

  一个主题包含了一个或者多个分区，每个分区会有一个或者多个副本，这些副本分别存放在不同的broker上面（一个topic创建的时候，如果指定的副本数量超过broker数量，这个创建会失败）。

  每个partition的副本，有两种角色：

  1. leader副本

     每个分区都会有一个leader副本，所有的读写请求都是到这个副本当中

  2. follower副本

     follower副本不参与到读写请求服务当中，只会同步保存leader副本的数据。当leader副本挂了，其中一个follower副本会成为leader副本。follower副本通过使用和消费者一样的命令(fetch命令)，来不断获取写入leader副本中的消息。

  kafka会尽量的把所有的副本平均分配到各个broker当中，来保证负载均衡和可用性。

  下图中，一个topic包含了三个分区，每个分区有三个副本（1个leader，2个follower）。

  ```mermaid
  graph BT
  subgraph broker2
      p0_replica1
      p1_replica1
      p2_leader
    end
    subgraph broker1
      p0_replica0
      p1_leader
      p2_replica1
    end
    subgraph broker0 
      p0_leader
      p1_replica0
      p2_replica0
    end
    
  ```

  + leader副本选择

    kafka通过监听/brokers/ids/{broker的id}临时节点来判断一个broker是否存活。当一个broker宕机以后，controller会对leader分区在该broker的进行leader选择。

    controller会读取 /brokers/topics/[topic]/partitions/[partition]/state 节点获取所有的ISR(in_sync replica)。从这些ISR里面选择一个作为新的leader， 更新这个节点的leader和leader_epoch的值。最后通过RPC的方式通知所有的broker，这个分区的leader换了。


+ 消息读写过程

  kafka的读写动作都必须由分区的leader副本来完成，客户端（生产者或者消费者）在请求的时候需要知道对应的leader副本究竟在哪个broker。

  客户端能通过向任意一个broker发送一个元数据请求（metadata request）来获取所有的分区分布情况。获取到返回以后，客户端会缓存这些数据，并且周期性的刷新这个这个分布情况。

  + 写入

    一个消息包含四个部分topic、key、partition和value，其中topic和value是必填项，key和partition选填。

    发消息的时候，需要通过一个serializer来对消息进行序列化。如果没有指定partition，就需要通过partitioner根据key来选择写入到哪个分区。然后这条消息会被加入到对应分区的等待发送队列中，另外的一条线程会负责把队列里的消息发送到对应的broker。

    当消息写入到分区leader以后，这条消息会同步到对应的follower分区。

    生产者的ack参数配置了多少个副本成功写入了，才认为这条消息是正常的写入

    + acks=0

      生产者只要发送出去就认为成功，不管broker是否收到

    + acks=1

      leader分区写入就认为是成功

    + acks=all

      所有的in_sync分区写入了才认为是成功。

    ```mermaid
      graph TD
        subgraph client part
          producer-->serializer
          serializer-->partitioner
        end
        subgraph kafka_cluster_part
          partitioner_leader-->replica0
          partitioner_leader-->replica1
        end
        partitioner-->partitioner_leader
        subgraph msg_record
          topic
          key
          partition
          value
        end
    
    ```

  + 读取

    消费者拉消息的时候，也是一批批的拉取，可以配置拉取的时间间隔以及拉取的大小。

    消费者从分区leader读取到消息，通过serializer进行序列化，消费者消费完消息以后，需要一个机制来记录已经消费消息的offset。

    旧版本的kafka是通过zk来维护哪个offset已经被消费了，但是zk不适合这样的大量写入场景。现在的版本是使用__consumeroffsets这个主题来维护这个关系。当消费者消费完消息以后，就将offset写入到这个主题当中。

    消费者offset的提交，有两种方式：

    + 自动提交

      指定的时间间隔自动进行提交，这样带来的问题是会重复消费或者数据丢失

    + 手动提交

      手动提交能分为

      + 同步提交

        同步提交会一直重试、阻塞，知道提交成功

      + 异步提交

        异步提交不会重试和阻塞

    ```mermaid
    graph TD
    	subgraph kafka_cluster_part
    		partition_leader
    	end
    	subgraph client_part
    		serializer-->consumer
    	end
    	subgraph topic
    		__consumeroffsets
    	end 
    	partition_leader-->serializer
    	consumer-->__consumeroffsets
    ```

    

+ 消费者、消费者组与分区

  通过下面的图我们可以看到，一个主题里面会分成多个分区，而一个消费者最起码属于一个消费者组。消费者组这个概念，让kafak的消费者端可以很轻易地进行水平扩容，只要启动新的实例，并且把这个实例分配到需要扩容的消费者组即可。

  kafka会保证每个partition只被一个消费者消费。如下图的topic0和group0所示，partition0被绑定到了consumer0，partition1和partition2被绑定到了consumer1。这样的绑定关系，使得写入partition0的消息必定是由consumer0消费，写入partition1和partition2必定由consumer1消费。这个绑定关系是在rebalance过程构建的。

  当一个消费者组里面消费者的数量超过了主题的分配数，这样就会有消费者数处于空闲状态的。如图中的consumer6，因为group1中有4个消费者，而topic0只有三个分区，这样就回有一个consumer会一直处于空闲状态。

  如果我们需要提高吞吐量，可以增加一个topic的分区数量，同时增加消费者组里面消费者的数量。

 ```mermaid
  graph TB
  subgraph group1
    consumer3
    consumer4
    consumer5
    consumer6
  end
  subgraph group0
    consumer1
    consumer0
  end
  subgraph topic0
    partition0
    partition1
    partition2
  end
partition0-->consumer1
  partition1-->consumer0
  partition2-->consumer0
  partition0-->consumer3
  partition1-->consumer4
  partition2-->consumer5
 ```

+ rebalance机制

  rebalance这个过程实际上就是把主题的分区分配给消费者组里面各个消费者的过程

  + 触发条件

    1. 消费者组里面的消费者数量发生了变化

       消费者是通过发送心跳到group coordinator这个broker来表明这个消费者还活着

    2. 主题的分区数量发生改变

  + 分配策略

    + RangeAssignor(默认)
    + RoundRobinAssignor
    + StickyAssignor
    + 自己实现

  + 流程

    1. 所有消费者向group coordinator发送JoinGroup请求。group coordinator会从所有的消费者中选取一个作为leader，这个leader负责消费分配方案的制定。
    2. Leader会分配消费方案。leader会把这个方案写入到SyncGroup请求中发给group coordinator。group coordinator会把方案塞入到SyncGroup的resonse中，返回给所有的消费者。

+ 消息持久化

  + 消息存储

    每条消息都有一个offset来表示它在分区中的偏移量。每个partition会对应到broker中的一个目录，这个目录的命名规则为<topic_name>_<partition_id>。例如，share-test-topic这个主题的两个分区的文件夹分别是share-test-topic-0、share-test-topic-1。

    实际存储数据的是log文件，这个log文件会被分成多个段（LogSegement）。每个分段会包含一个log文件(日志文件)，一个index文件(索引文件)和一个timeindex文件(时间索引文件)。分段文件的命名规则是按照顺序的，取得是前一个分段文件最后一个offset，例如
    第一个文件：00000000000000000000.log

    第二个文件：0000000000000005376.log(假设第一个文件的最后一个offset为5376)

    log文件里面，除了记录了消息的具体内容以外，还保存了offset和position，这个position是这条消息在本文件中的位置。

    index文件主要是存放了offset和position的对照关系

    ```mermaid
    graph LR
    subgraph index
    offset=917,position=17800 
    offset=918,position=17801
    offset=919,position=17822
    end
    subgraph log
    offset=917,position=17800,value=abc
    offset=918,position=17801,value=bad
    offset=919,position=17822,value=eee
    end
    offset=917,position=17800-->offset=917,position=17800,value=abc
    offset=918,position=17801-->offset=918,position=17801,value=bad
    offset=919,position=17822-->offset=919,position=17822,value=eee
    ```

    所以，当需要通过offset查找到消息的时候

    1. 通过二分法查找到属于那个分段
    2. 通过index文件超找到position
    3. 通过position去到日志文件读取消息

  + 日志清除

    有一条后台的线程，定期清除不符合保留条件的日志分段文件。

    1. 基于时间的保留策略

       一般保留7天的分段

    2. 基于日志大小的保留策略

    3. 基于日志起始偏移量的保留策略

  + 日志压缩

    TODO


+ 顺序消费

  1. kafka是保证分区内有序，但是分区间是无法保证顺序的，所以在需要保证消息消费顺序的场景下，需要把主题的分区数设置为1。
  2. 写入消息的时候指定到固定的partition

+ 避免重复消费

  有些场景下，生产者需要重复发送一条消息，这样消费者需要避免重复消费

  1. 通过流水表，接到消息以后，先查流水表，流水表存在记录，就不消费了，不存在就消费，然后写入流水表，这是强校验，但是性能相对差些
  2. 通过redis来缓存已经消费了的消息的id，这样的是弱校验，只能是无关紧要的场景，

+ 事务

  0.11.0版本以后，kafka开始支持事务消息。

  事务消息可以保证本地逻辑和消息发送是原子原子性的，先发送一条消息到消息中间件，然后执行本地逻辑，本地逻辑成功以后向消息中间件发送确认，这个时候这条消息才会被消费者感知。

  TODO

+ 动态水平扩展

  新的broker加入到集群以后，新建的主题会分配到这个broker，但是已经创建的主题不会主动进行重新分配。

  可以通过kafka-reassign-partitions.sh，这个工具来进行重新分配。

+ 生产者异常重试

+ 消费者失败重试

+ 高吞吐原因

  TODO

  参考

  <https://baixiaoustc.github.io/2019/07/10/2019-07-10-kafka-why-so-high-write-throughput/>

  
