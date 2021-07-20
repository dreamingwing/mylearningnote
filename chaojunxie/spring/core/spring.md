## IOC

+ 注入方式

  1. 构造方法注入
  2. setter方法注入
  3. 接口注入

+ IOC容器职责

  + 业务对象的构建管理
  + 业务对象的依赖绑定

+ spring容器类型

  + BeanFactory

    最基础的容器，使用延迟加载机制，所以启动比较快

  + ApplicationContext

    继承了BeanFactory，在BeanFactory的基础上提供了类似事件发布、国际化等支持。启动的时候会把所有的bean都加载，并且构建好bean之间的关系，相对于BeanFactory，需要更多的资源。

+ bean作用域

  + singleton

    默认，一个IOC容器里面只有一个bean实例

  + prototype

    每次getBean都会返回一个新的实例

  + request

    每次http请求都会创建一个新的实例

  + session

    每个新的session创建一个新的实例

  + global-session

+ 容器启动

  1. 容器启动阶段

     1. 通过BeanDefinitionReader加载bean的配置，然后生成对应的BeanDefinition
     2. 把BeanDefinition注册到对应的BeanDefinitionRegistry
     3. 执行BeanFactoryPostProcessor。这个类会对一些符合要求的BeanDefinition进行修改。例如，PropertyPlaceholderConfigurer是用来把占位符设置为实际值。

  2. Bean实例化阶段

     1. 实例化bean对象

        使用策略模式来确定是应该用反射还是cglib来生成对应的bean对象，一般都是用cglib。生产的bean会被包裹在一个BeanWrapper当中。

     2. 通过BeanWrapper对bean进行属性设置

     3. 检查bean是否继承了各种Aware接口，如果实现了，就把对应的属性设置进去

     4. 执行BeanPostProcessor.postProcessBeforeInitialization方法

     5. 如果这个bean继承了InitializingBean，就执行afterPropertiesSet方法

     6. 执行配置的initMethod方法(注解是initMethod，xml配置是init-method)

     7. 执行BeanPostProcessor.postProcessAfterInitialization方法

     8. 检查bean是否实现了DisposableBean接口，或者配置了destroyMethod，如果有，就生产一个对应的对象用于销毁的时候进行回调。

+ Spring mvc工作流程

  ```mermaid
  graph LR
  	client[client]
  	DispatcherServlet[DispatcherServlet]
  	HandlerMapping[HandlerMapping]
  	HandlerAdapter[HandlerAdapter]
  	HttpMessageConverter[HttpMessageConverter]
  	Handler[Handler]
  	ModelAndView[ModelAndView]
  	ViewResolver[ViewResolver]
  	Model[Model]
  	View[View]
  	client-.1.->DispatcherServlet
  	DispatcherServlet-.2.->HandlerMapping
  	DispatcherServlet-.3.->HandlerAdapter
  	HandlerAdapter-.4.->HttpMessageConverter
  	HttpMessageConverter-.4.->Handler
  	HandlerAdapter-.5.->ModelAndView
  	ModelAndView-.5.->DispatcherServlet
  	DispatcherServlet-.6.->ViewResolver
  	DispatcherServlet-.7.->Model
  	Model-.7.->View
  	View-.8.->client
  ```

  1. DispatcherServlet接收来自客户端的请求
  2. DispatcherServlet通过HandlerMapping解析到对应的handler(我们写的controller)
  3. DispatcherServlet通过HandlerAdapter来调用Handler
  4. HttpMessageConverter会解析过来的请求参数，传递给Handler
  5. Handler处理完以后，会返回ModelAndView对象到HandlerAdapter和DispatcherServlet
  6. DispatcherServlet通过ViewResolver查找到实际的view
  7. DispatcherServlet把model传给对应的view
  8. view回传给客户端

+ 事务(spring的事务都是基于AOP来实现 )

  + spring事务管理方式

    + 编程式事务，在代码中硬编码
    + 声明式事务
      + 基于xml
      + 基于注解

  + spring事务隔离级别

    1. TransactionDefinition.ISOLATION_DEFAULT

       使用数据的的事务隔离级别

    2. TransactionDefinition.ISOLATION_READ_UNCOMMITTED

       读未提交

    3. TransactionDefinition.ISOLATION_READ_COMMITTED

       读提交

    4. TransactionDefinition.ISOLATION_REPEATABLE_READ

       不可重复读

    5. TransactionDefinition.ISOLATION_SERIALIZABLE

       串行

  + spring事务传播

    + TransactionDefinition.PROPAGATION_REQUIRED(默认)

      如果存在当前事务，就加入当前事务，否则新建一个事务

    + TransactionDefinition.PROPAGATION_SUPPORTS

      如果存在当前事务，就加入当前事务，否则以非事务的方式运行

    + TransactionDefinition.PROPAGATION_MANDATORY

      如果存在当前事务，就加入当前事务，否则抛出异常

    + TransactionDefinition.PROPAGATION_REQUIRES_NEW

      创建一个新的事务，如果存在当前事务就挂起当前事务

    + TransactionDefinition.PROPAGATION_NOT_SUPPORT

      以非事务方式运行，如果存在当前事务就挂起当前事务

    + TransactionDefinition.PROPAGATION_NEVER

      以非事务方式运行，如果存在当前事务就抛错

    + TransactionDefinition.PROPAGATION_NESTED

      如果存在当前事务，就新建一个事务嵌套的当前事务，否则新建一个事务

+ AOP织入方式

  + 编译时织入
  + 类加载时织入
  + 运行时织入

+ 循环依赖

  Prototype作用域是无法解决循环依赖问题的，只有在singleton中才能解决。

  DefaultSingletonBeanRegistry中包含了三个Map来解决循环依赖的问题

  + singletonObjects

    单例容器，缓存创建的单例bean

  + singletonFactories

    映射创建bean的原始工厂

  + earlySingletonObjects

    映射早期的bean，这个时候的bean是不完整的，只能称为instance。

  假设A，B两个类互相依赖

  A依次执行doGetBean->查询缓存->createBean创建实例->把实例放到singletonFactories->调用populateBean方法装配属性。这个时候发现需要B的实例，所以也会调用A调用过的链路来创建实例。

  B的实例执行到populateBean的时候发现需要A的实例，也会调用doGetBean，但是在查询缓存的阶段，能成功的在singletonFactories获得A的实例。所以B的bean能成功创建，然后放置到singletonObjects。这样A的bean也能成功创建，然后放置到singletonObjects。

+ 