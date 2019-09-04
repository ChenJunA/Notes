# Spring-IOC
##一、IOC(Inversion：倒置、转化 of Control)
- **IOC是一种思想，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制**
- **谁控制谁，控制什么：传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对象的创建；谁控制谁？当然是IoC容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象包括比如文件等）。**
- **为何是反转，哪些方面反转了：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。**

传统应用程序示意图：
![传统应用程序示意图](https://images0.cnblogs.com/blog/289233/201501/261421378318292.jpg "传统应用程序示意图")
有IOC容器之后程序示意图：
![有IOC容器之后程序示意图](Spring-IOC-1.PNG "有IOC容器之后程序示意图")

## 二、DI
**DI—Dependency Injection，即“依赖注入”：组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。**
　　**理解DI的关键是：“谁依赖谁，为什么需要依赖，谁注入谁，注入了什么”，那我们来深入分析一下：**

- **谁依赖于谁：当然是应用程序依赖于IoC容器；**
- **为什么需要依赖：应用程序需要IoC容器来提供对象需要的外部资源；**
- **谁注入谁：很明显是IoC容器注入应用程序某个对象，应用程序依赖的对象；**
- **注入了什么：就是注入某个对象所需要的外部资源（包括对象、资源、常量数据）。**

### BeanFactory(IOC容器)
- **定义**
   BeanFactory 是类的通用工厂，它可以创建并管理各种类的对象，这些类就是 POJO，Spring 称这些被创建并管理的类对象为 Bean。里的Bean和我们之前理解的JavaBean有些不同，之前我们理解的JavaBean需要符合一定的规范，而这里的Bean范围更大，在这里凡是可以被Spring实例化管理的JAVA类都可以成为Bean，这样说来，这里的Bean首先包含我们刚才说的JavaBean，还包含着其他可以被实例化的Java类，这样说来BeanFactory就是一个工厂，创建可以被实例化和管理的Java对象，如果给其一个更合适的称呼，估计IOC容器算是比较合适吧。
   JavaBean:
   * 默认无参构造函数
   * 被序列化并且实现了Serializable接口
   * get&set方法
   * public修饰类
   * private修饰属性
- **体系结构**
   ![BeanFactory继承体系](https://upload-images.jianshu.io/upload_images/3386108-2a580484388112e6.png "BeanFactory继承体系")
- **初始化**
   通过BeanFactory加载配置文件，启动IOC容器
   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="people" class="com.cj.spring.ioc.People"
          p:name="cj"
          p:age="25"
            />
   </beans>
   ```
   然后使用 DefaultListableBeanFactory 与 XmlBeanDefinitionReader 来启动 Ioc 容器：
   ```
   ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
   Resource resource = resolver.getResource("classpath:beans.xml");
   System.out.println("getURL:" + resource.getURL());

   DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
   XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(factory);
   reader.loadBeanDefinitions(resource);

   System.out.println("Bean 工厂已完成初始化");

   People people = factory.getBean("people", People.class);
   System.out.println("People 已被创建");
   System.out.println(people.toString());
   ```
   **BeanFactory 只能管理单例（Singleton）Bean 的生命周期。它不能管理原型(prototype)Bean 的生命周期。这是因为原型 Bean 实例被创建之后便被传给了客户端,容器失去了对它们的引用。**
### ApplicationContext(IOC容器)
- **定义**
   ApplicationContext 由 BeanFactory 派生而来，提供了很多实际应用的功能 。 在 BeanFactory 中，很多功能需要以编程的方式实现，而在 ApplicationContext 中则可以通过配置的方式来实现。
- **体系结构**
   ![ApplicationContext体系结构](https://upload-images.jianshu.io/upload_images/3386108-0bab515765274856.png "ApplicationContext体系结构")
   ![ApplicationContext体系结构](https://upload-images.jianshu.io/upload_images/3386108-f30706cba033515c.png?imageMogr2/auto-orient/ "ApplicationContext体系结构")
   
   ***MessageSource***：为应用提供i18n 国际化消息访问的功能

   ***ResourcePatternResolver*** ： 所有ApplicationContext 实现类都实现了类似于
   PathMatchingResourcePatternResolver 的功能，可以通过带前缀的Ant风格的资源文
   件路径装载Spring的配置文件。

   ***LifeCycle***：该接口是Spring 2.0 加入的，该接口提供了start()和stop()两个方法，     主 要用于控制异步处理过程。在具体使用时，该接口同时被ApplicationContext 实现及   具体Bean 实现，ApplicationContext 会将start/stop 的信息传递给容器中所有实现了   该接口的Bean，以达到管理和控制JMX、任务调度等目的。

   ***ConfigurableApplicationContext*** 扩展于ApplicationContext，它新增加了两个主要的方法：refresh()和close()，让ApplicationContext 具有启动、刷新和关闭应用上下文的能力。在应用上下文关闭的情况下调用refresh()即可启动应用上下文，在已经启动的状态下，调用refresh()则清除缓存并重新装载配置信息，而调用close()则可关闭应用上下文。这些接口方法为容器的控制管理带来了便利。
- **实现类**
   ClassPathXmlApplicationContext - 从类路径加载配置文件。
   FileSystemXmlApplicationContext - 从文件系统加载配置文件。 
   ```
   //从类路径加载配置文件
   ApplicationContext context1 = new ClassPathXmlApplicationContext("beans.xml");
   context1.getBean();
   //从文件系统加载配置文件
   ApplicationContext context2 = new FileSystemXmlApplicationContext("d:/beans.xml");
   context2.getBean();
   ```
- **区别**
   ApplicationContext - 在初始化应用上下文时，会实例化所有**单实例**的 Bean，所以相对来说，初始化时间会比 BeanFactory 稍长
   BeanFactory - 在初始化容器时，并未实例化 Bean。直到 Bean 被访问时，才会被实例化。
### WebApplicationContext(IOC容器)
####ServletContext
- **定义**
   ServletContext官方叫servlet上下文。服务器会为每一个工程创建一个对象，这个对象就是ServletContext对象。这个对象全局唯一，而且工程内部的所有servlet都共享这个对象。所以叫全局应用程序共享对象。是一个全局信息的存储空间，代表当前web应用。
   ServletContext在web应用（服务器）启动时创建。
   ServletContext在Web应用（服务器）关闭时释放。
- **作用**
   1. 是一个域对象
   2. 可以读取全局配置参数
   3. 可以搜索当前工程目录下面的资源文件
   4. 可以获取当前工程名字（了解）

WebApplicationContext 专用于 web 应用 , 它允许从相对于 web 根目录的路径中装载配置文件完成初始化工作，从 WebApplicationContext 中可以获得 ServletContext 的引用，整个 Web 应用上下文对象将作为属性放置在 ServletContext 中，以便 web 应用可以访问 spring 上下文 ,spring 中提供 WebApplicationContextUtils 的 getWebApplicationContext(ServletContext src) 方法从 ServletContext 中获取 WebApplicationContext 实例。

WebApplicationContext 定义了一个常量 ROOT_WEB_APPLICATION_ CONTEXT_ATTRIBUTE ，在上下文启动时， WebApplicationContext 实例以这个常量为键，放置在 ServletContext 的属性列表中，因此我们可以直接通过以下语句从 Web 容器中获取 WebApplicationContext :
```
//从 servletContext 中获取 WebApplicationContext
WebApplicationContext wac= (WebApplicationContext) servletContext.getAttribute(WebApplicationContext
        .ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);

//从 WebApplicationContext 中获取 servletContext
ServletContext sc=wac.getServletContext();
```

![WebApplicationContext继承体系](https://upload-images.jianshu.io/upload_images/3386108-c3d56e7f7c16647a.png?imageMogr2/auto-orient/ "WebApplicationContext继承体系")

- **初始化 WebApplicationContext**
WebApplicationContext 需要 ServletContext 实例，它必须在拥有 Web 容器的前提下才能完成启动工作 。 我们可以在 web.xml 中配置自启动的 Servlet 或定义 Web 容器监听器（ ServletContextListener ），借助这两者中之一，就可以启动 Spring Web 应用上下文 。
---
Spring 提供了用于启动 WebApplicationContext 的 Servlet 和 Web 容器监听器：

   - org.springframework.web.context.ContextLoaderServlet。(低版本,Spring4 已经不再支持这个类)
   - org.springframework.web.context.ContextLoaderListener。（高版本)

使用 ContextLoaderListener/ContextLoaderServlet 启动 WebApplicationContext：
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">

    <!-- 指定 spring 配置文件-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:spring-*.xml</param-value>
    </context-param>

    <!-- ContextLoaderServlet -->
    <servlet>
        <servlet-name>springContextLoaderServlet</servlet-name>
        <servlet-class>org.springframework.web.context.ContextLoaderServlet         </servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <!-- ContextLoaderListener web 容器监听器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```
ContextLoaderListener 通过 Web 容器上下文参数 contextConfigLocation 获取 Spring 配置文件的所在位置 。 可以指定多个配置文件，任意选用逗号 、空格或冒号来分隔它们。 对于未带资源类型前缀的文件路径， WebApplicationContext 会默认这些路径相对于 Web 的部署根路径 。 当然，也可以采用带资源类型前缀的路径配置，比如这里的 classpath:spring-*.xml。

因为 WebApplicationContext 需要使用到日志，所以我们把 Log4J 的配置文件放置到类路径 WEB-INF/classes 下，这样 Log4J 引擎即可顺利启动 。 如果 Log4J 配置文件放置在其他位置，就必须在 web.xml 指定 Log4J 配置文件位置 。Spring 为启用 Log4J 引擎提供了两个类似于启动 WebApplicationContext 的实现类： Log4jConfigServlet（Spring4 中不再支持） 和 Log4jConfigListener，不管采用哪种方式都必须保证能够在装载 Spring 配置文件前先装载 Log4J 配置信息 。
```
<!-- 指定 log4j 配置文件-->
<context-param>
    <param-name>log4jConfigLocation</param-name>
    <param-value>/WEB-INF/log4j.properties</param-value>
</context-param>
<!-- Log4j 监听器-->
<listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
</listener>
```
**Log4jConfigListener 必须放置在 ContextLoaderListener 前面，保证前者先启动，完成装载 Log4J 配置文件并初始化 Log4J 引擎的工作，紧接着后者再启动 。**

### 父子容器
通过 HierarchicalBeanFactory 接口， Spring 的 IoC 容器可以建立父子层级关联的容器体系，子容器可以访问父容器中的 Bean ，但父容器不能访问子容器的 Bean。 在容器内， Bean 的 id 必须是唯一的，但子容器可以拥有一个和父容器 id 相同的 Bean。
### Spring Bean的作用域
**Bean的作用域是指spring容器创建Bean后的生存周期即由创建到销毁的整个过程**
非 Web 应用环境中，Bean 只有 singleton 与 prototype 两种作用域。而在 WebApplicationContext 中，它为 Bean 添加了另外三种作用域：request、session 与 global session。
- **Singleton(默认))**

每一个Bean的实例只会被创建一次，而且Spring容器在整个应用程序生存期中都可以使用该实例。
- **Prototype（原型，雏形，蓝本）**
每次获取Bean实例时都会新创建一个实例对象，类似new操作符。

特殊的情景，当一个作用域为Singleton的Bean依赖于一个作用域为prototype的Bean时如下：
```
<!-- 作用域prototype-->
<bean name="accountDao" scope="prototype"
class="com.zejian.spring.springIoc.dao.impl.AccountDaoImpl"/>
<!-- 作用域Singleton -->
<bean name="accountService" class="com.zejian.spring.springIoc.service.impl.AccountServiceImpl">
    <!-- 注入作用域为prototype的accountDao对象,需要set方法 -->
    <property name="accountDao" ref="accountDao"/>
</bean>
```
**方法一：实现ApplicationContextAware接口**
在这样的情况下希望的是每次getBean(“accountService”)处理的都是一个新的accountDao实例对象，但是由于 ***accountService的依赖是在Bean被创建时注入的，而且accountService是一个Singleton，整个生存周期中只会创建一次，因此它所依赖的accountDao实例对象也只会被注入一次，此后不会再注入任何新的accountDao实例对象***。为了解决这种困境，只能放弃使用依赖注入的功能，使用代码实现，如下：通过实现ApplicationContextAware接口，重写setApplicationContext，这样的话Spring容器在创建AccountServiceImpl实例时会自动注入ApplicationContext对象，此时便可以通过ApplicationContext获取accountDao实例了，这样可以保证每次获取的accountDao实例都是新的。
xml:
```
<bean name="accountDao" scope="prototype"  class="com.zejian.spring.springIoc.dao.impl.AccountDaoImpl"/>
<!-- accountDao通过代码注入 -->
<bean name="accountService" class="com.zejian.spring.springIoc.service.impl.AccountServiceImpl" />
```
代码注入示范：
```
public class AccountServiceImpl implements AccountService , ApplicationContextAware {
    private ApplicationContext applicationContext;

    @Override
    public void doSomething() {
        System.out.println("AccountServiceImpl#doSomething......");
        System.out.println("getAccountDao....."+ getAccountDao().toString());

    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext=applicationContext;
    }

    private AccountDao getAccountDao(){
        return applicationContext.getBean(AccountDao.class);
    }
}
```
测试代码：
```
//加载配置文件
ApplicationContext applicationContext=new ClassPathXmlApplicationContext("spring/spring-ioc.xml");
//测试获取不同实例的AccountDao
AccountService accountService= (AccountService) applicationContext.getBean("accountService");
accountService.doSomething();
AccountService accountService1= (AccountService) applicationContext.getBean("accountService");
accountService1.doSomething();
```
显然这样的方式，每次获取的daoAccount都不一样，也就解决上述的问题，另外的一种情况是当一个作用域为propotype的Bean依赖于一个Singleton作用域的Bean时，解决方案跟上述是相同的。请注意， ***当一个Bean被设置为prototype 后Spring就不会对一个bean的整个生命周期负责，容器在初始化、配置、装饰或者是装配完一个prototype实例后，将它交给客户端，随后就对该prototype实例不闻不问了***。因此我们需要慎用它，一般情况下，对 *有状态的bean应该使用prototype作用域，而对无状态的bean则应该使用singleton作用域。所谓 **有状态就是该bean有保存信息的能力**，不能共享，否则会造成线程安全问题，而无状态则不保存信息，是线程安全的，可以共享*，spring中大部分bean都是Singleton，整个生命周期过程只会存在一个。

**方法二：注解，声明动态代理模式**
```
Component
@Scope(value = "singleton")
public class SingletonBean {
    //......
}

@Component
@Scope(value = "prototype" , proxyMode = ScopedProxyMode.TARGET_CLASS)
public class PrototypeBean {
    //......
}

@Component
@Scope(value = "request" , proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestBean {
    //......
}

@Component
@Scope(value = "session" , proxyMode = ScopedProxyMode.TARGET_CLASS)
public class SessionBean {
    //........
}
```
**方法三：xml配置(只适用于reqeust和session)**
request和session作用域通过xml配置文件方式声明时必须在＜bean＞标签中放置＜aop:scoped-proxy＞作为子标签，该作用于注解声明代理模式效果相同。prototype作用域生效的方式只有基于注解方式和前面基于实现ApplicationContextAware接口两种方式。
```
<!-- 一个HTTP session作用域的Bean 作为代理暴露出去 -->
    <bean id="userPreferences" class="com.foo.UserPreferences" scope="session">
        <!--指示容器代理这个Bean -->
        <aop:scoped-proxy/>
    </bean>
    <!--一个单例Bean注入一个代理Bean -->
    <bean id="userManager" class="com.foo.UserManager">
      <!-- 实际使用的是userPreferences的代理对象 -->
        <property name="userPreferences" ref="userPreferences"/>
</bean>
```

- **Request**
对于每次HTTP请求到达应用程序，Spring容器会创建一个全新的Request作用域的bean实例，且该bean实例仅在当前HTTP request内有效，整个请求过程也只会使用相同的bean实例，因此我们可以根据需要放心的更改所建实例的内部状态，而其他请求HTTP请求则创建新bean的实例互不干扰，当处理请求结束，request作用域的bean实例将被销毁，如在接收参数时可能需要一个bean实例来装载一些参数，显然每次请求参数几乎不会相同，因此希望bean实例每次都是足够新的而且只在request作用域范围内有效。
- **Session**
每当创建一个新的HTTP Session时就会创建一个Session作用域的Bean，并该实例bean伴随着会话的存在而存在。
- **GlobalSession**
这种作用域类似于Session作用域，相当于全局变量，类似Servlet的Application，适用基于portlet的web应用程序，请注意，portlet在这指的是分布式开发，而不是portlet语言开发。
所有的Seesion共享一个实例变量


## Spring装配beans
**setter、constructor、静态工厂方法、实例工厂方法、注解@Autowired自动装配几种方式。**
- no – 缺省情况下，自动配置是通过“ref”属性手动设定
1. **setter**:
```
<bean id="customer" class="com.yiibai.common.Customer">
    <property name="person" ref="person" />
</bean>

<bean id="person" class="com.yiibai.common.Person" />
```
   2. **constructor**:
```
<bean name="accountDao" class="com.zejian.spring.springIoc.dao.impl.AccountDaoImpl"/>
<!-- 通过构造注入依赖 -->
<bean name="accountService" class="com.zejian.spring.springIoc.service.impl.AccountServiceImpl">
    <!-- 构造方法方式注入accountDao对象,-->
    <constructor-arg  ref="accountDao"/>
</bean>
```
需要注意的是，当一个bean定义中有多个＜constructor-arg＞标签时，它们的放置顺序并不重要，因为Spring容器会通过传入的依赖参数与类中的构造函数的参数进行比较，尝试找到合适的构造函数。可惜的是，在某些情况下可能会出现问题，如下的User类，带有两个构造函数，参数类型和个数都是一样的，只是顺序不同，这在class的定义中是允许的，但对于Spring容器来说却是一种灾难。
```
public class User {

    private String name;
    private int age;

    //第一个构造函数
    public User(String name , int age){
        this.name=name;
        this.age=age;
    }
    //第二个构造函数
    public User(int age,String name){
        this.name=name;
        this.age=age;
    }
}
```
配置信息：
```
配置信息：

<bean id="user" class="com.zejian.spring.springIoc.pojo.User" >
    <constructor-arg  type="java.lang.String" value="Jack"/>
    <constructor-arg  type="int" value="26"/>
</bean>
```
当程序运行时，Spring容器会尝试查找适合的User构造函数进而创建User对象，由于＜constructor-arg＞的注入顺序并不重要，从而导致不知该使用两种构造函数中的那种，这时user实例将创建失败，Spring容器也将启动失败。幸运地是，Spring早已为我们预测到这种情况，因此只要给Spring容器一点点提示，它便能成功找到适合的构造函数从而创建user实例，在＜constructor-arg＞标签中存在一个index的属性，通过index属性可以告诉spring容器传递的依赖参数的顺序，下面的配置将会令Spring容器成功找到第一个构造函数并调用创建user实例。
```
<bean id="user" class="com.zejian.spring.springIoc.pojo.User" >
    <constructor-arg index="0" value="Jack"/>
    <constructor-arg index="1" value="26"/>
</bean>
```
---
- **静态工厂和实例工厂**
public class PojoFactory {

    /**
     * 静态工厂创建
     * @return
     */
    public static Account createAccount(){
        Account account=new Account();
        account.setName("Jack");
        account.setPwd("1234");
        return account;
    }

    /**
     * 通过工厂实例创建
     * @return
     */
    public  Account createAccount2(){
        Account account=new Account();
        account.setName("Jack");
        account.setPwd("1234");
        return account;
    }
}
**xml配置如下**
```
<!-- 静态工厂创建,调用静态方法createAccount -->
<bean id="account3" class="com.zejian.spring.springIoc.conf.PojoFactory" 
  factory-method="createAccount"/>

<!-- 工厂实例创建,先创建工厂类在调用方法createAccount2 -->
<bean id="factory" class="com.zejian.spring.springIoc.conf.PojoFactory" />
<bean id="account4" factory-bean="factory"  factory-method="createAccount2"/>
```
---

- byName – 根据属性名称自动装配。如果一个bean的名称和其他bean属性的名称是一样的，将会自装配它。(需要set方法)
```
<bean id="customer" class="com.yiibai.common.Customer" autowire="byName" />	
<bean id="person" class="com.yiibai.common.Person" />
```
如果Spring容器中没有找到可以注入的实例bean时，将不会向依赖属性值注入任何bean，这时依赖bean的属性可能为null

---
- byType – 按数据类型自动装配。如果一个bean的数据类型是用其它bean属性的数据类型，兼容并自动装配它。(需要set方法)
```
<bean id="customer" class="com.yiibai.common.Customer" autowire="byType" />
<bean id="person" class="com.yiibai.common.Person" />
```
由于是基于类型的注入，因此当xml文件中存在多个相同类型名称不同的实例Bean时，Spring容器依赖注入仍然会失败，因为存在多种适合的选项，Spring容器无法知道该注入那种，此时我们需要为Spring容器提供帮助，指定注入那个Bean实例。可以通过＜bean＞标签的autowire-candidate(候选人)设置为false来过滤那些不需要注入的实例Bean
```
<bean id="userDao"  class="com.zejian.spring.springIoc.dao.impl.UserDaoImpl" />

<!-- autowire-candidate="false" 过滤该类型 -->
<bean id="userDao2" autowire-candidate="false" class="com.zejian.spring.springIoc.dao.impl.UserDaoImpl" />

<!-- byType 根据类型自动装配userDao-->
<bean id="userService" autowire="byType" class="com.zejian.spring.springIoc.service.impl.UserServiceImpl" />
```
---
- constructor – 在构造函数参数的byType方式(需要构造方法)。
在constructor模式下，存在单个实例则优先按类型进行参数匹配（无论名称是否匹配），当存在多个类型相同实例时，按名称优先匹配，如果没有找到对应名称，则注入失败，此时可以使用autowire-candidate=”false” 过滤来解决。
---
- autodetect(detect:查明，发现) – 该模式自动探测使用构造器自动装配或者byType自动装配。
首先会尝试找合适的带参数的构造器，如果找到的话就是用构造器自动装配，如果在bean内部没有找到相应的构造器或者是无参构造器，容器就会自动选择byTpe的自动装配方式。

---
- 循环依赖
构造函数注入还有一个无法解决的循环依赖的问题，如下有两个bean，A和B，这两个bean通过构造函数互为依赖，这种情况下Spring容器将无法实例化这两个bean。
```
public class A{
    private B b;
    public A(B b){
     this.b=b;
    }
}

public class B{
    private A a;
    public B(A a){
     this.a=a;
    }
}
```
```
<bean id="a" class="com.zejian.springioc.pojo.A">
    <constructor-arg ref="b" />
</bean>

<bean id="b" class="com.zejian.springioc.pojo.B">
    <constructor-arg ref="a" />
</bean>
```
这是由于A被创建时，希望B被注入到自身，然而，此时B还有没有被创建，而且B也依赖于A，这样将导致Spring容器左右为难，无法满足两方需求，最后脑袋奔溃，抛出异常。解决这种困境的方式是使用Setter依赖，但还是会造成一些不必要的困扰，因此，强烈不建议在配置文件中使用循环依赖。

---
### 基于注解的自动装配(@Autowired&@Resource&@Value)
- **@Autowired(属于spring，是通过反射来设置属性值)**
可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作(效果相同)。 通过 @Autowired的使用来消除 set ，get方法。
```
public class UserServiceImpl implements UserService {
    //标注成员变量
    @Autowired
    private UserDao userDao;
    //标注构造方法
    @Autowired
    public UserServiceImpl(UserDao userDao){
        this.userDao=userDao;
    }
    //标注set方法
    @Autowired
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void done(){
        userDao.done();
    }
}
```
在使用@Autowired时，首先在容器中查询对应类型的bean
- 如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据
- 如果查询的结果不止一个，那么@Autowired会根据名称来查找。

- 如果查询的结果为空，那么会抛出异常。解决方法时，使用required=false,false指明当userDao实例存在就注入不存就忽略，如果为true，就必须注入，若userDao实例不存在，就抛出异常
**@Qualifier注解与@Autowired结合**
@Autowired可能匹配到多个类型相同，id不同的bean,再通过@Qualifier指定bean名称
```
public class UserServiceImpl implements UserService {
    //标注成员变量
    @Autowired
    @Qualifier("userDao1")
    private UserDao userDao;   
 }
```
- **@Resource(属于J2EE)**
可以标注在成员变量和set方法上，但无法标注构造函数。
@Resource有两个中重要的属性：name和type。Spring容器对于@Resource注解的name属性解析为bean的名字，type属性则解析为bean的类型。因此使用name属性，则按byName模式的自动注入策略，如果使用type属性则按 byType模式自动注入策略。倘若既不指定name也不指定type属性，Spring容器将通过反射技术默认按byName模式注入。
默认按照名称进行装配，名称可以通过name属性进行指定，如果没有指定name属性，当注解写在字段上时，默认取字段名进行安装名称查找，如果注解写在setter方法上默认取属性名进行装配。当找不到与名称匹配的bean时才按照类型进行装配。但是需要注意的是，如果name属性一旦指定，就只会按照名称进行装配。

- **@Value**
上述两种自动装配的依赖注入并不适合简单值类型，如int、boolean、long、String以及Enum等，对于这些类型，Spring容器也提供了@Value注入的方式，这是非常具备人性化的，可以解决很多硬编码问题。@Value接收一个String的值，该值指定了将要被注入到内置的java类型属性值，放心，不必关心类型转换，大多数情况下Spring容器都会自动处理好的。一般情况下@Value会与properties文件结合使用，也分两种情况一种是SpEL（有点类似于jsp的EL），另外一种是占位符方式，
利用注解@Value获取jdbc.url和jdbc.username的值，实现如下：
```
public class UserServiceImpl implements UserService {
    //标注成员变量
    @Autowired
    @Qualifier("userDao")
    private UserDao userDao;
    //占位符方式
    @Value("${jdbc.url}")
    private String url;
    //SpEL表达方式，其中代表xml配置文件中的id值configProperties
    @Value("#{configProperties['jdbc.username']}")
    private String userName;

    @Override
    public void done(){
        System.out.println("url:"+url);
        System.out.println("username:"+userName);
        userDao.done();
    }
}
```
基于xml的配置如下：
```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

    <!--基于占位符方式 配置单个properties -->
    <!--<context:property-placeholder location="conf/jdbc.properties"/>-->
    <!--基于占位符方式 配置多个properties -->
    <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PreferencesPlaceholderConfigurer">
        <property name="location" value="conf/jdbc.properties"/>
    </bean>

    <!--基于SpEL表达式 配置多个properties id值为configProperties 提供java代码中使用 -->
    <bean id="configProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">
        <property name="locations">
            <list>
                <value>classpath:/conf/jdbc.properties</value>
            </list>
        </property>
    </bean>

    <!--基于SpEL表达式 配置单个properties -->
    <!--<util:properties id="configProperties" location="classpath:conf/jdbc.properties"/>-->

    <!--注解驱动 -->
    <context:annotation-config/>

    <bean id="userDao" class="com.zejian.spring.springIoc.dao.impl.UserDaoImpl"/>

    <bean id="userDao2" class="com.zejian.spring.springIoc.dao.impl.UserDaoImpl"/>

    <bean id="userService" class="com.zejian.spring.springIoc.service.impl.UserServiceImpl"/>
</beans>
```

## JavaConfig
@Configuration注解标明BeanConfiguration类，使得BeanConfiguration类替代了xml文件，也就是说注解@Configuration等价于＜beans＞标签。在该类中，每个使用注解@Bean的公共方法对应着一个＜bean＞标签的定义，即@Bean等价于＜bean＞标签。
@Import加载多个配置文件,最后用AnnotationConfigApplicationContext加载主配置文件就可以。

```
在Web环境中，需要在web.xml中加入如下代码：
<context-param>
    <param-name>contextClass</param-name>
    <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
</context-param>
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>xx.x.x.x</param-value>
</context-param>
```

### Bean的延时加载
在某些情况下，我们可能希望把bean的创建延迟到使用阶段，以免消耗不必要的内存，Spring也非常自愿地支持了延迟bean的初始化。因此可以在配置文件中定义bean的延迟加载，这样Spring容器将会延迟bean的创建直到真正需要时才创建。通常情况下，从一个已创建的bean引用另外一个bean，或者显示查找一个bean时会触发bean的创建即使配置了延迟属性，因此如果Spring容器在启动时创建了那些设为延长加载的bean实例，不必惊讶，可能那些延迟初始化的bean可能被注入到一个非延迟创建且作用域为singleton的bean。在xml文件中使用bean的lazy-init属性可以配置改bean是否延迟加载，如果需要配置整个xml文件的bean都延迟加载则使用defualt-lazy-init属性，请注意lazy-init属性会覆盖defualt-lazy-init属性。
```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        " default-lazy-init="true">
    <!--default-lazy-init="true" xml中全部bean延迟加载 -->
    <!-- lazy-init="false" 表示非延长加载-->
    <bean name="accountDao" lazy-init="false"
          class="com.zejian.spring.springIoc.dao.impl.AccountDaoImpl"/>

    <!-- 声明accountService对象,交给spring创建 -->
    <bean name="accountService" class="com.zejian.spring.springIoc.service.impl.AccountServiceImpl">
          <!-- 注入accountDao对象,需要set方法-->
          <property name="accountDao" ref="accountDao"/>
    </bean>
</beans>
```
默认情况下Spring容器在启动阶段就会创建bean，这个过程被称为预先bean初始化，这样是有好处的，可尽可能早发现配置错误，如配置文件的出现错别字或者某些bean还没有被定义却被注入等。当然如存在大量bean需要初始化，这可能引起spring容器启动缓慢，一些特定的bean可能只是某些场合需要而没必要在spring容器启动阶段就创建，这样的bean可能是Mybatis的SessionFactory或者hibernate SessionFactory等，延迟加载它们会让Spring容器启动更轻松些，从而也减少没必要的内存消耗。

###＜context:component-scan/＞与＜context:annotation-config/＞
前面我们使用@Autowired、@Resource、@Value等自动装配注解时用＜context:annotation-config/＞进行注解驱动注册，从而使注解生效。实际上这样＜context:annotation-config/＞一条配置，它的作用是式地向 Spring 容器注册AutowiredAnnotationBeanPostProcessor、CommonAnnotationBeanPostProcessor、PersistenceAnnotationBeanPostProcessor 以及RequiredAnnotationBeanPostProcessor 这 4 个BeanPostProcessor。注册这4个 BeanPostProcessor后Spring容器就能够识别相应的注解了，当然它们也是也可单独配置的。 
假如想使用@ Resource 、@ PostConstruct、@ PreDestroy等注解就可以单独声明CommonAnnotationBeanPostProcessor

如果想使用@PersistenceContext注解，声明PersistenceAnnotationBeanPostProcessor的Bean即可。

如果想使用 @Required的注解，就必须声明RequiredAnnotationBeanPostProcessor的Bean。

一般来说，这些注解是随处可见的，如果总是需要一条一条配置自然就非常繁琐了，于是spring容器非常智能地为我们提供＜context:annotation-config/＞的简化配置方式，自动声明。

 #### <context:annotation-config />仅能够在已经在已经注册过的bean上面起作用。对于没有在spring容器中注册的bean，它并不能执行任何操作。

对于＜context:component-scan/＞，前面在使用@Service、@Component、@Controller 、@Repository等注解时，需要在xml配置文件声明包扫描驱动＜context:component-scan/＞，它的作用是Spring容器在启动时会启动注解驱动去扫描对应包下的bean对象并将创建它们的实例（ **在xml配置了这个标签后，spring可以自动去扫描base-pack下面或者子包下面的java文件，如果扫描到有@Component @Controller@Service等这些注解的类，则把这些类注册为bean**），这样我们就无需一个个地进行bean配置声明了，极大简化了编程代码。请注意，当spring的xml配置文件出了＜context:component-scan/＞ 后，＜context:annotation-config/＞就可以退休了，因为＜context:component-scan/＞已包含了＜context:annotation-config/＞的功能了。在大部分情况下，都会直接使用＜context:component-scan/＞进行注解驱动注册和包扫描功能。

## IOC容器管理bean
每一个交给Spring IOC（后面统称Spring容器）容器创建的对象必须被分配至少一个名称，如果开发者没有提供，Spring容器将会为其分配一个内部名称，通过Bean的名称，我们可以在其他类中查找该类并使用它，如前面的案例，也是通过Bean名称获取到实际对象并执行对应的操作。在基于xml的配置信息中，可以使用id属性来为一个Bean分配名称，在同一个xml配置文件中，id必须是唯一的，但不同的xml可以相同，当然还可以使用name来为Bean分配名称，name属性可以分配多个名称，此时可使用空格、逗号、分号来分离给定Bean分配多个名称，而id属性则无法这样使用。
```
<!-- name属性配置多个名称 -->
<bean name="accountDao,accountDao2" class="com.zejian.spring.springIoc.dao.impl.AccountDaoImpl"/>
<!-- id属性配置唯一名称而且不能与name相同-->
<bean id="accountDaoId" class="com.zejian.spring.springIoc.dao.impl.AccountDaoImpl"/>
```
在name属性中声明了两个名称，除了第一个名称外，其他的名称都被称为别名(aliase)。除了在Bean中定义名称外，还可利用＜alias＞标签向Bean赋予别名：
```
<bean id="accountDaoId" class="com.zejian.spring.springIoc.dao.impl.AccountDaoImpl"/>
<!-- name属性指明要给那个Bean赋予别名,alias则指明赋予的别名-->
<alias name="accountDaoId" alias="accountDao3" />
```

**IOC：控制反转:将对象的创建权,由Spring管理。
DI(Dependency Injection,依赖注入)：在Spring创建对象的过程中,把对象依赖的属性注入到类中。**

### Bean的重写机制
- 主要是当不同的xml文件中出现同名id属性的bean时读取的优先级问题
- 重写机制原则是当声明的bean的名称一样时，后者会覆盖前者。
- 在web应用开发过程中，一般都会将配置进行分层管理，然后通过一个主springApplication.xml来聚合它，在这样的情况下分层的配置文件属于springApplication.xml的子文件，在这样的关系遇到上述的情况一般都子文件的优先级高，因此会加载子文件的bean。

@Required注解作用于Bean setter方法上，用于检查一个Bean的属性的值在配置期间是否被赋予或设置(populated：居住于，生活于)

**依赖注入与自动装配**
自动装配为no时，手动装配，使用< property ref="">或< constructor-arg  ref="">。
byname,bytype相当于使用< property ref="">,但是匹配模式不同
constructor相当于使用< constructor-arg  ref="">，按类型匹配
autodetect是constructor与byType的组合，会先进行constructor，如果不成功，再进行byType。

**通过set方法注入属性，那么spring会通过默认的空参构造方法来实例化对象，所以如果在类中写了一个带有参数的构造方法，一定要把空参数的构造方法写上，否则spring没有办法实例化对象，导致报错。**