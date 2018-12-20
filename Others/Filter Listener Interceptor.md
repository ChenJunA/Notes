# Servelet
servlet是一种运行服务器端的java应用程序，具有独立于平台和协议的特性，并且可以动态的生成web页面，它工作在客户端请求与服务器响应的中间层.Servlet 的主要功能在于交互式地浏览和修改数据，生成动态 Web 内容。这个过程为：
1. 客户端发送请求至服务器端；
2. 服务器将请求信息发送至 Servlet；
3. Servlet 生成响应内容并将其传给服务器。响应内容动态生成，通常取决于客户端的请求；
4. 服务器将响应返回给客户端。
在 Web 应用程序中，一个 Servlet 在一个时刻可能被多个用户同时访问。这时 Web 容器将为每个用户创建一个线程来执行 Servlet。如果 Servlet 不涉及共享资源的问题，不必关心多线程问题。但如果 Servlet 需要共享资源，需要保证 Servlet 是线程安全的。
版本3.1，为了简化开发流程，Servlet 3.0 引入了注解（annotation），这使得 web 部署描述符 web.xml 不再是必须的选择。
# Filter
属于Servlet范畴，Filter与servlet具有完全相同的生命周期行为。
ilter是一个可以复用的代码片段，可以用来转换HTTP请求、响应和头信息。Filter不像Servlet，它不能产生一个请求或者响应，它只是修改对某一资源的请求，或者修改从某一的响应。Servlet中的过滤器Filter是实现了javax.servlet.Filter接口的服务器端程序，主要的用途是过滤字符编码、做一些业务逻辑判断等。其工作原理是，只要你在web.xml文件配置好要拦截的客户端请求，它都会帮你拦截到请求，此时你就可以对请求或响应(Request、Response)统一设置编码，简化操作；同时还可进行逻辑判断，如用户是否已经登陆、有没有权限访问该页面等等工作。它是随你的web应用启动而启动的，只初始化一次，以后就可以拦截相关请求，只有当你的web应用停止或重新部署的时候才销毁。完整的流程是：Filter对用户请求进行预处理，接着将请求交给Servlet进行处理并生成响应，最后Filter再对服务器响应进行后处理。

Filter有如下几个用处。
1. 在HttpServletRequest到达Servlet之前，拦截客户的HttpServletRequest。
2. 根据需要检查HttpServletRequest，也可以修改HttpServletRequest头和数据。
3. 在HttpServletResponse到达客户端之前，拦截HttpServletResponse。
4. 根据需要检查HttpServletResponse，也可以修改HttpServletResponse头和数据。

Filter有如下几个种类。
1. 用户授权的Filter：Filter负责检查用户请求，根据请求过滤用户非法请求。
2. 日志Filter：详细记录某些特殊的用户请求。
3. 负责解码的Filter：包括对非标准编码的请求解码。
4. 能改变XML内容的XSLT Filter等。
5. Filter可负责拦截多个请求或响应；一个请求或响应也可被多个请求拦截。

# Listener
监听web应用内部各种事件并做出处理,属于Servlet范畴

ServletAPI给出的常用Listener接口，自定义Listener要选择性实现它们：
1、  ServletContextAttributeListener
2、  ServletContextListener
3、  HttpSessionListener
4、  HttpSessionAttributeListener
# Interceptor
interceptor动态拦截Action调用的对象，在service或者其他方法前后调用一个方法进行处理。采用了**java动态代理和反射的机制**来实现，与servlet无关。不同框架实现的拦截器内部方法是不同的。

AOP是一种思想，实现包括Filter和Interceptor。Filter基于回调函数，Spring中AOP封装动态代理完成的。动态代理包括JDK动态代理（反射）和CGLIB动态代理（继承）。Interceptor基于JDK动态代理，反射。

**过滤器（filter）和拦截器（interceptor）区别：**

1. filter基于filter接口中的doFilter回调函数，interceptor则基于Java本身的反射机制；

2. filter是依赖于servlet容器的，没有servlet容器就无法回调doFilter方法，而interceptor与servlet无关；

3. filter的过滤范围比interceptor大，filter除了过滤请求外通过通配符可以保护页面、图片、文件等，而interceptor只能过滤请求，只对action起作用，在action之前开始，在action完成后结束（如被拦截，不执行action）；

4. filter的过滤一般在加载的时候在init方法声明，而interceptor可以通过在xml声明是guest请求还是user请求来辨别是否过滤；

5. interceptor可以访问action上下文、值栈里的对象，而filter不能；

6. 在action的生命周期中，拦截器可以被多次调用，而过滤器只能在容器初始化时被调用一次。

7. filter是servlet规范定义的，而interceptor是spring定义的。

**拦截器（Interceptor）和过滤器（Filter）的执行顺序**
过滤前-拦截前-Action处理-拦截后-过滤后