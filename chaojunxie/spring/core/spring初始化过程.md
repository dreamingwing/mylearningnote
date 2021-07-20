- 容器启动

  容器启动阶段

  - 通过BeanDefinitionReader加载bean配置，然后生成对应的BeanDefinition
  - 再把BeanDefinition注册到BeanDefinitionRegistry (DefaultListableBeanFactory)
  - 再执行BeanFactoryPostProcessor,这个类可以对符合要求的BeanDefinition进行修改，例如（ (PropertyResourceConfigurer)是用来把占位符设置为实际值。

    bean实例化阶段

  - 实例化bean：使用策略模式是采用反射的方式还是采用cglib的方式来生成bean对象，一般都是采用cglib,生成的bean被包裹在 BeanWapper之中
  - 通过BeanWapper对属性赋值
  - 检查bean是否集成各种 Aware（ApplicationContextAware、BeanFactoryAware）接口，如果实现了，就把对应的属性设置进去
  - 执行BeanPostProcessor.postProcessBeforeInitialization()方法
  - 如果实现了InitializingBean接口，实现afterPropertiesSet()方法
  - 执行配置的initMethod方法(注解是initMethod，xml配置是init-method)
  - 执行BeanPostProcessor.postProcessAfterInitialization方法
  - 检查bean是否实现了DisposableBean接口，实现destroy（方法）
  - 执行配置的destroyMethod方法(注解是destroyMethod，xml配置是destroy-method)或者配置了

- 循环依赖

   Prototype作用域是无法解决循环依赖的问题，只有在 singleton中才能得到解决

   DefaultSingletonBeanRegistry中包含了三个Map来解决循环依赖的问题

  ~~~java
  /** Cache of singleton objects: bean name --> bean instance */
  //单例容器，缓存创建的单例bean  一级缓存：用于存放完全初始化好的 bean
  	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(64);
  //映射创建bean的原始工厂  三级级缓存：存放 bean 工厂对象，用于解决循环依赖
  	/** Cache of singleton factories: bean name --> ObjectFactory */
  	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);
  //映射早期的bean，这个时候的bean是不完整的，只能称为instance。 二级缓存：存放原始的 bean 对象（尚未填充属性），用于解决循环依赖
  	/** Cache of early singleton objects: bean name --> bean instance */
  	
  private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
  /**A 创建过程中需要 B，于是 A 将自己放到三级缓里面 ，去实例化 B
  B 实例化的时候发现需要 A，于是 B 先查一级缓存，没有，再查二级缓存，还是没有，再查三级缓存，找到了！
  然后把三级缓存里面的这个 A 放到二级缓存里面，并删除三级缓存里面的 A
  B 顺利初始化完毕，将自己放到一级缓存里面（此时B里面的A依然是创建中状态）
  然后回来接着创建 A，此时 B 已经创建结束，直接从一级缓存里面拿到 B ，然后完成创建，并将自己放到一级缓存里面
  如此一来便解决了循环依赖的问题
  先让最底层对象完成初始化，通过三级缓存与二级缓存提前曝光创建中的 Bean，让其他 Bean 率先完成初始化。*/
  ~~~

  假设A，B两个类互相依赖

  A依次执行doGetBean->查询缓存->createBean创建实例->把实例放到singletonFactories->调用populateBean方法装配属性。这个时候发现需要B的实例，所以也会调用A调用过的链路来创建实例。

  B的实例执行到populateBean的时候发现需要A的实例，也会调用doGetBean，但是在查询缓存的阶段，能成功的在singletonFactories获得A的实例。所以B的bean能成功创建，然后放置到singletonObjects。这样A的bean也能成功创建，然后放置到singletonObjects。


