| 操作   | 接口                                                         | 说明                                                         |
| ------ | ------------------------------------------------------------ | :----------------------------------------------------------- |
| Index  | put my_index/_doc/1<br/>{
  "user":"mkie",
  "comment":"you know, for search"
} | 相同的id如果不存在，就插入一条新的，如果相同的id存在，就delete旧的，插入新的，version+1 |
| Create | POST my_index/_doc/<br/>{
  "user":"mkie",
  "comment":"you know, for search"
} | es会生成id。                                                 |
| Create | put my_index/_create/1<br/>{
  "user":"mkie",
  "comment":"you know, for search"
} | 指定id。同样的id，插入两次，第二次抛错。                     |
| Update | POST my_index/_update/1<br/>{
  "doc":{
    "user":"mike",
    "comment":"You know, Search"
  }
} |                                                              |
| Read   | Get my_index/_doc/1                                          |                                                              |
| Delete | Delete my_index/_doc/1                                       |                                                              |
|        |                                                              |                                                              |

