## SQL调优

### exlain

|     字段      |                    |      |
| :-----------: | :----------------: | :--: |
|      id       |                    |      |
|  Select_type  |      操作类型      |      |
|     table     |    作用在哪个表    |      |
|  partitions   |                    |      |
|     type      |                    |      |
| Possible_keys |  可能使用哪些索引  |      |
|      key      | 实际使用了哪些索引 |      |
|    Key_len    |                    |      |
|      ref      |                    |      |
|     rows      |    扫描了多少行    |      |
|   filtered    |                    |      |
|     Extra     |                    |      |

### 调优步骤

1. 用explain+sql_no_cache，在实际环境商品跑一下这条sql。使用sql_no_cache的原因是防止缓存的干扰
2. 关注点在possible_keys、keys以及rows上面，看下是否用上了期望中的索引
3. show index from 看下这个表的索引，关注下cardinality这列，这列能体现这个数据的多样性
4. 有些时候选错索引，是因为mysql的统计出了问题，可以用analyze table来修正
5. 如果还是不行，可能是因为mysql觉得现在的选择是最优的，这是因为有时候是否需要回表等因素
6. 可以使用下force index语句来指明用的哪个索引来验证使用这个索引的想法
7. 各个索引
8. 避免索引失效

### Join优化

+ Index Nested-Loop Join

  驱动表做全表扫码，被驱动表做树搜索

+ Block Nested-Loop Join

+ 

