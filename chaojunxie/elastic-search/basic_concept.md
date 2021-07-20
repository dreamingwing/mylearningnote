## 基本概念

+ 文档(document)

  es里面可以搜索的最小单位。一个文档会被序列化为json格式保存。每个文档会有一个unique id，这个id可以自己指定，也可以由es自动生成。文档可以类比为关系型数据库里面的一行记录。

+ 文档的元数据(meta data)

  包括如下

  + _index 所属的索引
  + _type 所属的类型
  + _id 文档的id
  + _source 文档原始json
  + _all 整合所有的字段到all字段当中
  + _version 版本
  + _score 相关性打分

+ 索引(index)

  一类相似文档的集合。每个索引都会有一个mapping定义，包含了文档的字段名和字段类型。索引和类别关系型数据库中的一张表。索引里面的setting定义了索引的分布。

+ 类型(type)

## 类别关系型数据库

| RDBMS  | ES       |
| ------ | -------- |
| Table  | Index    |
| Row    | Document |
| Column | Field    |
| Schema | Mapping  |
| SQL    | DSL      |

## 基本概念2

+ 节点(node)

  一个节点是一个es的实例，本质上就是一个java进程。一台机器上面能运行多个节点。每个节点都已自己的名字以及会被分配一个UID，这个UID会被记录在data目录下。

+ Master-eligible node和master node

  每个节点启动了以后，除非node.master被设置为false，要不都能参与到主节点的选举当中，这的节点就是Master-eligible node。集群中第一个启动的节点，会把自己选举成master节点。集群状态(cluster state)，每个节点都会保留，但是只有master节点能修改。

+ 数据节点(data node)

  负责保存分片中数据的节点

+ Coordinating node

  接收client的请求，然后转发到合适的节点，得到结果以后做汇总。每个节点默认都是coordinating node

+ 分片(shard)

  + Primary shard(主分片)

    接近水平扩展问题。一个分片运行一个luence实例。主分片数量，在创建索引的时候指定，后续不能修改，除非reindex。

  + replica shard(副本)

    可以理解为主分片的备份。主要作用于高可用。副本数量可以动态调整。