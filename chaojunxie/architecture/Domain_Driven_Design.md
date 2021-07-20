## 基本概念

+ 领域

+ 子领域

+ 通用语言

  定义上下文含义。

  团队成员达成共识，能简单、清晰、准确描述业务涵义和规则的语言就是通用语言。

  + 术语
  + 用例场景

+ 限界上下文(bounded context)

  限定领域边界，确定语义所在的领域边界。

  限定通用语言能适用分范围。

+ 实体(entity)

  + 业务形态
  + 代码形态
  + 运行形态
  + 数据库形态

+ 值对象(value object)

+ 聚合(aggregate)
+ 聚合根(aggregate root)
+ 领域事件(Domain Event)

## 架构分层

+ 用户接口层
  + 用户界面
  + Web服务
  + 其他
+ 应用层
  + 应用服务(application service)
+ 领域层
  + 聚合(aggregate)
    + 实体(entity)
    + 值对象(value object)
  + 领域服务(domain service)
+ 基础层
  + 数据库
  + 事件总线
  + API网关
  + 缓存
  + 基础服务
  + 第三方工具
  + 其他基础组件

## 代码分层

+ interfaces
  + assembler
  + dto
  + facade
+ application
  + event
    + publish
    + subscribe
  + service
+ domain
  + entity
  + event
  + repository
  + service
+ infrastructure
  + config
  + util
    + api
    + driver
    + eventbus
    + mq