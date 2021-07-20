## Class文件总体结构

|        名称         | 长度（字节数） |        数量         |                     简述                     |
| :-----------------: | :------------: | :-----------------: | :------------------------------------------: |
|    Magic Number     |       4        |          1          |   确定是否为一个能比虚拟机接受的Class文件    |
|    Minor Version    |       2        |          1          |                   次版本号                   |
|    Major Version    |       2        |          1          |                   主版本号                   |
| Constant_pool_count |       2        |          1          |              常量池中常量的数量              |
|    Constant_pool    |                | Constant_pool_count |                                              |
|    Access_flags     |       2        |          1          |                   访问标志                   |
|     This_class      |       2        |          1          |              类索引，指向常量池              |
|     Super_class     |       2        |          1          |             父类索引，指向常量池             |
|  Interfaces_count   |       2        |          1          |                 接口索引数量                 |
|     Interfaces      |                |  Interfaces_count   |        接口索引，描述这个类实现的接口        |
|    Fields_count     |       2        |          1          |                                              |
|       fields        |                |    Fields_count     | 描述接口或者类中的变量，包括类变量和实例变量 |
|    Methods_count    |       2        |          1          |                                              |
|       Methods       |                |    Methods_count    |                描述类中的方法                |
|  Attributes_count   |       2        |          1          |                                              |
|     Attributes      |                |  Attributes_count   |           字段表，方法表的自带属性           |
