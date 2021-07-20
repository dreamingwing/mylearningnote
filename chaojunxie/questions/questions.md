## 问题汇总

+ 神车数据?忘记了
  1. Spring boot和spring cloud是怎么样的关系，大概介绍一下
  2. spring boot的核心注解，举几个例子
  3. 用redis实现session共享
  4. redis实现单点登陆
  5. redis中的数据类型
  6. 缓存雪崩
  7. 缓存穿透
  8. 缓存击穿
  9. redis内存淘汰策略
  10. redis持久化
  11. 常用的设计模式，举例子，什么场景
  12. mybatis中#{}和${}的区别是什么
  13. protected修饰，子类是否能访问？如果是default呢？
  14. Float f=3.4是否正确
  15. 自动装箱和自动拆箱

+ Han
  1. kafka消息重发怎么处理，生产者确认机制？
  2. kafak重复消费
  3. sql调优
  4. 数据量大概有多大
  5. 有没用过netty
  6. es查询优化
  7. java的线程安全，怎么实现
  8. sync和lock的区别
  9. mybatis分页是怎么实现的
  10. redis缓存击穿，怎么避免
  11. volatile关键字，项目中什么情况使用过?
  12. Spring boot优势

+ 云徙

  1. 拆成了什么模块
  2. 国际ccs库存是怎么设计
  3. 怎么防止超卖
  4. 库存的锁，锁什么对象
  5. 流量大了以后，下单扣库存，怎么优化(不用锁？锁会降低性能)
  6. 缓存里面的库存和db里面库存不一致，要怎样
  7. 分布式事务
  8. 缓存穿透
  9. 缓存击穿
  10. 数据量多大，最大的单表多大
  11. java是值传递还是引用传递
  12. 并发的bug源头原因-》内存可见性和竞态条件
  13. volatile关键字
  14. mybatis分页插件的原理
  15. ThreadLocal
  16. HashMap 1.7和1.8的区别
  17. mysql怎么排查慢查询
  18. mysql页分裂
  19. 是否可以用字符串做主键

+ 花旗银行(上海)

  + 描述一下汇丰CCR
  + 国际CCS的架构
  + 开发过程中最大的问题
  + 处理OOM问题的时候，dump文件有多大
  + java集合类有哪些
  + ArrayList进行删除操作要怎么做？在循环中怎么做？remove的原理是怎样
  + HashMap扩容
  + HashMap, HashTable, ConcurrentHashMap比较
  + 新建一个线程要怎么做
  + 如果需要拿到子线程的返回值，应该怎么做
  + ExecutorService执行runnable和callable有什么区别吗
  + 新建线程池的几个参数分别起什么作用
  + Lock怎么用，核心原理是怎样
  + 什么是乐观锁
  + 讲下jvm里面的堆和栈
  + 进行gc的时候是怎么判断对象是否进行回收

+ 没听清楚什么名字公司

  1. 讲下zookeeper的原理

  2. zk的集群数为什么一定要是基数

  3. zk的读写流程分别是怎样的

  4. jvm的主要gc算法

  5. 讲下jvm的老年代和新生代

  6. 继承和聚合分别是什么意思

  7. java反射的原理

  8. 动态代理有什么实现方式

  9. File类有哪些常见的方法

  10. 哪些集合类是线程安全的

  11. 类加载流程是怎样的

  12. docker有没用过

  13. spring事务的传播属性

  14. spring事务怎么配置

  15. 讲下spring IOC

  16. spring DispatcherServlet的初始化过程是怎样的

  17. 有什么方式可以关闭java的线程池

  18. spring controller使用了什么模式

      单例

  19. spring controller怎么保证并发安全

      <https://www.jianshu.com/p/eba1fef77442>

  20. 讲下wait和notify方法

  21. http三次握手，post和get的数据包格式有什么不一样

  22. redis和zk分布式锁比较一下

+ 卓越教育

  1. SAAS有哪些点需要考虑和特别注意的

     + 租户和用户体系的设计
     + 数据权限和数据隔离

  2. SAAS系统中租户数据隔离有什么方案

     + 通过分库的方式，每个租户一个库
     + 分表，同样的表，不同的租户不同的名字

  3. 通过分库的方式来实现租户数据隔离，在应用的时候怎么实现租户到库的路由

     用spring的话，可以自己继承AbstractRoutingDataSource来实现一个租户到库的路由

  4. 对于每个url，如果要实现到crud的权限管理，你会怎么设计

  5. 用过那么多框架，最深刻的源码是什么

  6. dubbo和mybatis，spring都用了什么样的机制来实现的

  7. 单纯是用了spring boot还是用了用了全家桶

  8. 消息队列技术选型的时候，有什么考量的纬度

     1. 吞吐量   
     2. 是否集群
     3. 团队对这个mq的熟悉程度

  9. 如果让你做系统规划，会在什么场景下使用mq

     1. 解耦  将消息同步逻辑和业务逻辑分离
     2. 广播  一个业务代码的修改通知多个点 通知机制
     3. 削峰  某一个时间点并发量比较大，达到削疯的效果

  10. 用了消息队列，会遇上什么问题，这些问题，分别有什么方案去解决

      1. 数据丢失
         + 生产者端的
         + 消费者端
      2. 重复消费

  11. 如果消息是有顺序的，怎么保证消费者顺序消费

  12. 单纯使用数据库，性能可以差点，秒杀扣减库存应该怎么做

  13. 老旧系统重构，怎么规划，怎么保证业务逻辑和本来的一致

  14. 单体应用切分微服务架构，你会基于什么原则，你是怎么思考的

  15. 分布式事务，有什么方案

  16. 淘宝购物的场景，大概会涉及到什么些表或者模块

  17. 一个下单的场景。商品的信息其实会修改的，但是我们下单以后，商品在我们订单上面的商品信息是一直不变的，这个方案要怎么做

+ 滴滴(北京)

  1. 讲下鹰眼HDE的架构

  2. HDE的微服务切分基于什么原则

  3. 你在HDE中承担了什么部分的工作

  4. HDE的服务是怎么部署的

  5. HDE你估算能抗多大的数据量？最早可能出现问题的是哪个功能或者模块？

  6. 讲下redis

  7. redis的持久化

  8. redis的主从架构

  9. mysql的核心数据结构是什么，数据是怎么存放的

  10. mysql的索引

  11. java里面线程池讲一下，包括了什么参数，各个参数分别起什么作用

  12. blockingqueue讲一下

      ArrayBlockQueue  有界

      LinkedBlockQueue  有界最大值为Integer最大值等同于无界

      ProirityBlockQueue   优先级堵塞队列

      SychronousBlockQueue  不用来存储的  只有当线程池中有空闲线程然后执行，没有空闲线程则直接创建新的线程，直到达到线程池最大值

  13. 讲下hashmap

  14. spring mvc和spring boot的区别

      spring boot是用来构建一切的   spring mvc是spring中给予servlet的一种MVC架构

  15. dispatcher servlet工作原理

  16. 树按照层次遍历

      首先需要用到一个队列 ，根节点入队，取出根节点判断左节点是否为空，非空入队，再判断右节点非空继续入队  依次循环

  17. 一个长url转化成短url，这样的一个模块要怎么做?统计怎么做，冷热数据要分别怎么保存?

  18. 用MD5来做这个长到短的转换行不行

+ 光大科技(北京)

  1. wait方法和sleep方法

  2. wait方法是否有参数

  3. sleep(0)和sleep(-1)分别会是怎样

     

  4. wait和notify的原理

  5. wait和notify分别会挂起和唤醒什么线程

  6. 线程池启动以后，核心线程数是否能改变

  7. 二叉搜索树

  8. redis持久化

  9. RDB做备份的时候，是否会阻塞

     不会

  10. redis的RDB文件copy on write机制

  11. Redis AOF文件的压缩

  12. redis除了那5个基本数据结构以外，还有什么数据结构，什么场景使用

      补充一个bitmaps  一个长达512M  2的32次方个不同比特位

       这个简单的例子中，每次用户登录时会执行一次redis.setbit(daily_active_users, user_id, 1)。将bitmap中对应位置的位置为1，时间复杂度是O(1)。统计bitmap结果显示有今天有9个用户登录。Bitmap的key是daily_active_users，它的值是1011110100100101。

  13. Future是否是阻塞的

  14. 单例，DCL单例能通过反射破坏，有什么方式避免

      私有构造函数添加非空判断 非空抛出异常

      添加readResolve方法，反序列化时，会直接返回该方法指定的对象，不需要创建对象。

  15. Mysql 什么情况下会索引失效

  16. sql调优

  17. mysql的事务隔离级别

  18. mysql的一致性视图是怎么实现的

  19. 大量的join怎么做调优

      

  20. spring的事务隔离级别

  21. spring的事务是怎么实现的

  22. spring的事务传播

  23. A和B两个方法，都有Transactional，A调用B，事务会是怎样的，是否走了AOP

  24. Controller和RestController两个注解能否转化

  25. @PathVariable和@RequestBody这两个注解分别在什么时候使用

  26. lambda的原理

  27. @Service和@Resource的区别

  28. 

+ 