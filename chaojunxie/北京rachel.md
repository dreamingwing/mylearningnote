# 北京rachel

1、问了大量的进销存相关的知识点 （补充业务场景）

2、防止重复提交幂等判断方式三种

​     解决方案1:通过将该次操作的唯一标识作为key存入redis,如果在规定时间内该key依然存在的话则代表重复提交

​     解决方案2:session+token的方式

3、SpringBoot原理

  SpringBootApplication  

  SpringBootConfigure

  EnableAutoConfigure

  AutoConfigurePackages

  Import

  SpringFactoriesLoader

  ComponentScan

4、JVM参数调优

 