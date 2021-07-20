# SpringMvc的流程

- DispatcherService接收来自客户端的请求
- DispatcherService通过HandlerMapping解析到对应的HandlerExecutionChain（包括拦截器和Handler）
- 调用拦截器中的preHandle
- DispatcherServicet通过Adapter来调用Handler
- 调用拦截器中的postHandle
- HttpMessageConverter会解析请求过来的参数，传递给Handler
- Handler处理完以后，会返回ModelAndView对象到HandlerAdapter和DispatcherServlet
- DispatcherServlet通过ViewResolver查找到实际的view
- DispatcherServlet把model传给对应的view
- view回传给客户端
- 调用拦截器中的afterCompletion

## 九大组件

- MultipartResolver
- LocaleResolver
- ThemeResolver
- HandlerMapping（初始化URL和处理器的映射）
- HandlerAdapter（默认初始化4个适配器 根据Controller的类型选择不同的适配器）
- HandlerExceptionResolver
- RequestToViewNameTranslator
- ViewResolver
- FlashMapManager

HandlerMapping + HandlerAdapter + HandlerExceptionResolver 。

![DispatcherServlet çå·¥ä½æµç¨](https://blog-pictures.oss-cn-shanghai.aliyuncs.com/15300766829012.jpg)

① **发送请求**

用户向服务器发送 HTTP 请求，请求被 Spring MVC 的调度控制器 DispatcherServlet 捕获。

② **映射处理器**

DispatcherServlet 根据请求 URL ，调用 HandlerMapping 获得该 Handler 配置的所有相关的对象（包括 **Handler** 对象以及 Handler 对象对应的**拦截器**），最后以 HandlerExecutionChain 对象的形式返回。

- 即 HandlerExecutionChain 中，包含对应的 **Handler** 对象和**拦截器**们。

~~~java
此处，对应的方法如下：

> // HandlerMapping.java
> 
> @Nullable
> HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception;
>
~~~

③ **处理器适配**

DispatcherServlet 根据获得的 Handler，选择一个合适的HandlerAdapter 。（附注：如果成功获得 HandlerAdapter 后，此时将开始执行拦截器的 `#preHandler(...)` 方法）。

提取请求 Request 中的模型数据，填充 Handler 入参，开始执行Handler（Controller)。 在填充Handler的入参过程中，根据你的配置，Spring 将帮你做一些额外的工作：

- HttpMessageConverter ：会将请求消息（如 JSON、XML 等数据）转换成一个对象。
- 数据转换：对请求消息进行数据转换。如 String 转换成 Integer、Double 等。
- 数据格式化：对请求消息进行数据格式化。如将字符串转换成格式化数字或格式化日期等。
- 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到 BindingResult 或 Error 中。

Handler(Controller) 执行完成后，向 DispatcherServlet 返回一个 ModelAndView 对象。

~~~java
此处，对应的方法如下：

> // HandlerAdapter.java
> 
> @Nullable
> ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;
>
~~~

⑤ **解析视图**

根据返回的 ModelAndView ，选择一个适合的 ViewResolver（必须是已经注册到 Spring 容器中的 ViewResolver)，解析出 View 对象，然后返回给 DispatcherServlet。

~~~java
此处，对应的方法如下：

> // ViewResolver.java
> 
> @Nullable
> View resolveViewName(String viewName, Locale locale) throws Exception;
>
~~~



⑥ ⑦ **渲染视图** + **响应请求**

ViewResolver 结合 Model 和 View，来渲染视图，并写回给用户( 浏览器 )。

~~~java
此处，对应的方法如下：

> // View.java
> 
> void render(@Nullable Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception;
>
~~~

![æµç¨ç¤ºæå¾](http://static2.iocoder.cn/images/Spring/2022-02-21/01.png)