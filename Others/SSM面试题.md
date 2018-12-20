# SSM面试题
## Spring
**一、什么是spring?**
广义上讲是一系列框架的总结，狭义上就是指spring framework。他是一个轻量级的，开源的应用框架，具有分层体系结构，允许用户选择组件或集成其他框架，由多个模块组成。

**二、spring优点?**
1. 开源免费
2. 分层架构，自由选择组件
3. 解耦，方便开发

**三、spring架构图**
![spring架构图](https://upload-images.jianshu.io/upload_images/1401055-5cca69d310447b72.png?imageMogr2/auto-orient/ "spring架构图")

**四、IOC与DI**
IOC：控制反转，是一种思想。传统设计程序时，通过在对象内部new创建对象，是程序主动的去创建依赖对象。而IOC是将对象交给IOC容器管理，由IOC容器来对对象进行管理，进行创建，注入等。程序被动的接收依赖对象。
DI：依赖注入，是用来实现IOC的。为了提升组件重用的频率。应用程序依赖IOC容器，IOC容器将某个对象（应用程序）需要的资源（对象，常量）注入。

**五、Spring中的IOC****
BeanFactory ApplicationContext WebApplicationContext

**六、BeanFactory ApplicationContext WebApplicationContext区别**
BeanFactory 是类的通用工厂，它可以创建并管理各种类的对象。BeanFactory 只能管理单例（Singleton）Bean 的生命周期。它不能管理原型(prototype)Bean 的生命周期。这是因为原型 Bean 实例被创建之后便被传给了客户端,容器失去了对它们的引用。
ApplicationContext 由 BeanFactory 派生而来，提供了更多面向实际应用的功能。ApplicationEventPublisher、MessageSource、ResourcePatternResolver 、LifeCycle
* ClassPathXmlApplicationContext - 从类路径加载配置文件。
* FileSystemXmlApplicationContext - 从文件系统加载配置文件。

ApplicationContext - 在初始化应用上下文时，会实例化所有单实例的 Bean，所以相对来说，初始化时间会比 BeanFactory 稍长
BeanFactory - 在初始化容器时，并未实例化 Bean。直到 Bean 被访问时，才会被实例化。
WebApplicationContext 专用于 web 应用

**七、SpringBean的生命周期**
BeanFactory:
* bean的建立， 由BeanFactory读取Bean定义文件，并生成各个实例
* Setter注入，执行Bean的属性依赖注入
* BeanNameAware的setBeanName(), 如果实现该接口，则执行其setBeanName方法
* BeanFactoryAware的setBeanFactory()，如果实现该接口，则执行其setBeanFactory方法
* BeanPostProcessor的processBeforeInitialization()，如果有关联的processor，则在Bean初始化之前都会执行这个实例的processBeforeInitialization()方法
* InitializingBean的afterPropertiesSet()，如果实现了该接口，则执行其afterPropertiesSet()方法
* Bean定义文件中定义init-method
* BeanPostProcessors的processAfterInitialization()，如果有关联的processor，则在Bean初始化之前都会执行这个实例的processAfterInitialization()方法
* (Disposable:一次性的)DisposableBean的destroy()，在容器关闭时，如果Bean类实现了该接口，则执行它的destroy()方法
* Bean定义文件中定义destroy-method，在容器关闭时，可以在Bean定义文件中使用“destory-method”定义的方法

ApplicationContext:
 * 在执行BeanFactoryAware的setBeanFactory()后，若有Bean类实现了org.springframework.context.ApplicationContextAware接口，则执行其setApplicationContext()方法，然后再进行BeanPostProcessors的processBeforeInitialization()

 **八、Bean作用域**
 Singleton prototype request session globalsession

 **九、inner bean**
 一个bean仅用于一个特定的属性，这是提醒其声明为一个内部bean。内部bean支持setter注入“property”和构造器注入"constructor-arg“。内部bean只应用于这个外部bean时使用。
 ```
 //setter
 <bean id="CustomerBean" class="com.yiibai.common.Customer">
    <property name="person">
        <bean class="com.yiibai.common.Person">
            <property name="name" value="yiibai" />
            <property name="address" value="address1" />
            <property name="age" value="28" />
        </bean>
    </property>
</bean>

//constructor
<bean id="CustomerBean" class="com.yiibai.common.Customer">
    <constructor-arg>
        <bean class="com.yiibai.common.Person">
            <property name="name" value="yiibai" />
            <property name="address" value="address1" />
            <property name="age" value="28" />
        </bean>
    </constructor-arg>
</bean>
 ```

 **十、Spring框架中的单例Beans是线程安全的么？**
 Spring框架并没有对单例bean进行任何多线程的封装处理。关于单例bean的线程安全和并发问题需要开发者自行去搞定。但实际上，大部分的Spring bean并没有可变的状态(比如Serview类和DAO类)，所以在某种程度上说Spring的单例bean是线程安全的。如果你的bean有多种状态的话（比如 View Model 对象），就需要自行保证线程安全。
最浅显的解决办法就是将多态bean的作用域由“singleton”变更为“prototype”。

**十一、自动装配**

**十二、Spring框架中有哪些不同类型的事件？**
Spring 提供了以下5中标准的事件：
* 上下文更新事件（ContextRefreshedEvent）：该事件会在ApplicationContext被初始化或者更新时发布。也可以在调用ConfigurableApplicationContext 接口中的refresh()方法时被触发。
* 上下文开始事件（ContextStartedEvent）：当容器调用ConfigurableApplicationContext的Start()方法开始/重新开始容器时触发该事件。
* 上下文停止事件（ContextStoppedEvent）：当容器调用ConfigurableApplicationContext的Stop()方法停止容器时触发该事件。
* 上下文关闭事件（ContextClosedEvent）：当ApplicationContext被关闭时触发该事件。容器被关闭时，其管理的所有单例Bean都被销毁。
* 请求处理事件（RequestHandledEvent）：在Web应用中，当一个http请求（request）结束触发该事件。
* 还可以通过扩展ApplicationEvent 类来开发自定义的事件。

**十三、Spring 框架中都用到了哪些设计模式？**
* 单例模式：注册bean
* 观察者模式：ApplicationListener
* 代理模式：AOP
* 模板方法：JdbcTemplate
* 工厂模式：beanFactory
* 策略模式：定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。本模式使得算法可独立于使用它的客户而变化。 spring中在实例化对象的时候用到Strategy模式

## SpringMVC

## Mybatis
**一、通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？**
Dao接口，就是人们常说的Mapper接口，接口名映射xml文件的namespace的值，方法名映射xml文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。Mapper接口是没有实现类的，当调用接口方法时，接口名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement，举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id = findStudentById的MappedStatement。在Mybatis中，每一个< select>、< insert>、< update>、< delete>标签，都会被解析为一个MappedStatement对象。

Dao接口里的方法，是不能重载的，因为是接口名+方法名的保存和寻找策略。
Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用 **JDK动态代理** 为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回。

**二、#{}和${}的区别是什么？**
```
#{}是预编译处理，${}是字符串替换。
Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值；
Mybatis在处理${}时，就是把${}替换成变量的值。
使用#{}可以有效的防止SQL注入，提高系统安全性。
#{}相当于对数据加上双引号，使用order by动态参数时需要注意，用$而不是#
```
**三、什么是Mybatis**
MyBatis是一个可以自定义SQL、存储过程和高级映射的持久层框架。

**四、Mybatis缓存**
一级缓存:
Mybatis一级缓存的作用域是同一个SqlSession，在同一个sqlSession中两次执行相同的sql语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。当一个sqlSession结束后该sqlSession中的一级缓存也就不存在了。Mybatis默认开启一级缓存。
二级缓存：
Mybatis二级缓存是多个SqlSession共享的，其作用域是mapper的同一个namespace，不同的sqlSession两次执行相同namespace下的sql语句且向sql中传递参数也相同即最终执行相同的sql语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。Mybatis默认没有开启二级缓存需要在setting全局参数中配置开启二级缓存。二级缓存需要查询结果映射的pojo对象实现java.io.Serializable接口实现序列化和反序列化操作，注意如果存在父类、成员pojo都需要实现序列化接口。

**五、Mybatis是如何进行分页的？分页插件的原理是什么？**
* Mybatis使用RowBounds对象进行分页，也可以直接编写sql实现分页，也可以使用Mybatis的分页插件。
* 分页插件的原理：实现Mybatis提供的接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql。


**六、简述Mybatis的插件运行原理，以及如何编写一个插件?**
* Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis通过动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
* 实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。


**七、Mybatis动态sql是做什么的？都有哪些动态sql？能简述一下动态sql的执行原理不？**
* Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能。
* Mybatis提供了9种动态sql标签：trim|where|set|foreach|if|choose|when|otherwise|bind。
* 其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。

**八、为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？**
Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

**九、Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？**
* 需要查询关联信息时，使用mybatis延迟加载特性可有效的减少数据库压力，首次查询只查询主要信息，关联信息等用户获取时再加载。只有使用resultMap支持延迟加载设置。
* Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。
* 它的原理是，使用 **CGLIB创建目标对象的代理对象** ，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

**十、MyBatis的好处是什么？**
* MyBatis把sql语句从Java源程序中独立出来，放在单独的XML文件中编写，给程序的维护带来了很大便利。
* MyBatis封装了底层JDBC API的调用细节，并能自动将结果集转换成Java Bean对象，大大简化了Java数据库编程的重复工作。
* 因为MyBatis需要程序员自己去编写sql语句，程序员可以结合数据库自身的特点灵活控制sql语句，因此能够实现比Hibernate等全自动orm框架更高的查询效率，能够完成复杂查询。

**十一、简述Mybatis的Xml映射文件和Mybatis内部数据结构之间的映射关系？**
Mybatis将所有Xml配置信息都封装到All-In-One重量级对象Configuration内部。在Xml映射文件中，< parameterMap>标签会被解析为ParameterMap对象，其每个子元素会被解析为ParameterMapping对象。< resultMap>标签会被解析为ResultMap对象，其每个子元素会被解析为ResultMapping对象。每一个< select>、< insert>、< update>、< delete>标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象。

**十二、什么是MyBatis的接口绑定,有什么好处？**
接口绑定就是在MyBatis中任意定义接口,然后把接口里面的方法和SQL语句绑定,我们直接调用接口方法就可以,这样比起原来了SqlSession提供的方法我们可以有更加灵活的选择和设置.

**十三、接口绑定有几种实现方式,分别是怎么实现的?**
接口绑定有两种实现方式,一种是通过注解绑定,就是在接口的方法上面加上@Select@Update等注解里面包含Sql语句来绑定,另外一种就是通过xml里面写SQL来绑定,在这种情况下,要指定xml映射文件里面的namespace必须为接口的全路径名.

**十四、MyBatis实现一对一有几种方式?具体怎么操作的？**
有联合查询和嵌套查询,联合查询是几个表联合查询,只查询一次,通过在resultMap里面配置association节点配置一对一的类就可以完成;嵌套查询是先查一个表,根据这个表里面的结果的外键id,去再另外一个表里面查询数据,也是通过association配置,但另外一个表的查询通过select属性配置。

**十五、Mybatis能执行一对一、一对多的关联查询吗？都有哪些实现方式，以及它们之间的区别？**
答：能，Mybatis不仅可以执行一对一、一对多的关联查询，还可以执行多对一，多对多的关联查询，多对一查询，其实就是一对一查询，只需要把selectOne()修改为selectList()即可；多对多查询，其实就是一对多查询，只需要把selectOne()修改为selectList()即可。
关联对象查询，有两种实现方式，一种是单独发送一个sql去查询关联对象，赋给主对象，然后返回主对象。另一种是使用嵌套查询，嵌套查询的含义为使用join查询，一部分列是A对象的属性值，另外一部分列是关联对象B的属性值，好处是只发一个sql查询，就可以把主对象和其关联对象查出来。

**十六、MyBatis里面的动态Sql是怎么设定的?用什么语法?**
答：MyBatis里面的动态Sql一般是通过if节点来实现,通过OGNL语法来实现,但是如果要写的完整,必须配合where,trim节点,where节点是判断包含节点有内容就插入where,否则不插入,trim节点是用来判断如果动态语句是以and 或or开始,那么会自动把这个and或者or取掉。

**十七、Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？**
第一种是使用< resultMap>标签，逐一定义列名和对象属性名之间的映射关系。
第二种是使用sql列的别名功能，将列别名书写为对象属性名，比如T_NAME AS NAME，对象属性名一般是name，小写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成T_NAME AS NaMe，Mybatis一样可以正常工作。
有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

**十八、Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？**
还有很多其他的标签，< resultMap>、< parameterMap>、< sql>、< include>、< selectKey>，加上动态sql的9个标签，trim|where|set|foreach|if|choose|when|otherwise|bind等，其中< sql>为sql片段标签，通过< include>标签引入sql片段，< selectKey>为不支持自增的主键生成策略标签。

**十九、模糊查询like语句该怎么写**
'%#{name}%'：报错
'%${name}%'：正确
"%"#{name}"%":正确
contact函数

**二十、Mybatis映射文件中，如果A标签通过include引用了B标签的内容，请问，B标签能否定义在A标签的后面，还是说必须定义在A标签的前面？**
虽然Mybatis解析Xml映射文件是按照顺序解析的，但是，被引用的B标签依然可以定义在任何地方，Mybatis都可以正确识别。原理是，Mybatis解析A标签，发现A标签引用了B标签，但是B标签尚未解析到，尚不存在，此时，Mybatis会将A标签标记为未解析状态，然后继续解析余下的标签，包含B标签，待所有标签解析完毕，Mybatis会重新解析那些被标记为未解析的标签，此时再解析A标签时，B标签已经存在，A标签也就可以正常解析完成了。

**二十一、Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？**
不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复；毕竟namespace不是必须的，只是最佳实践而已。原因就是namespace+id是作为Map<String, MappedStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

**二十二、Mybatis中如何执行批处理？**
BatchExecutor
当我们有多条sql语句需要发送到数据库执行的时候，有两种发送方式，一种是执行一条发送一条sql语句给数据库,另一个种是发送一个sql集合给数据库，也就是发送一个批sql到数据库,就是批处理。

**二十三、Mybatis都有哪些Executor执行器？它们之间的区别是什么？**
Mybatis有三种基本的Executor执行器，SimpleExecutor、ReuseExecutor、BatchExecutor。
* SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。
* ReuseExecutor：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map
* BatchExecutor：完成批处理。

**二十四、Mybatis中如何指定使用哪一种Executor执行器？**
在Mybatis配置文件中，可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数。

**二十五、Mybatis执行批量插入，能返回数据库主键列表吗？**
能

**二十六、Mybatis是否可以映射Enum枚举类？**
Mybatis可以映射枚举类，不单可以映射枚举类，Mybatis可以映射任何对象到表的一列上。映射方式为自定义一个TypeHandler，实现TypeHandler的setParameter()和getResult()接口方法。TypeHandler有两个作用，一是完成从javaType至jdbcType的转换，二是完成jdbcType至javaType的转换，体现为setParameter()和getResult()两个方法，分别代表设置sql问号占位符参数和获取列查询结果。

**二十七、如何获取自动生成的(主)键值？**
配置文件设置usegeneratedkeys 为true
```
<insert id="insertTest" useGeneratedKeys="true" keyProperty="id" 
　parameterType="com.kq.domain.IdentityTest">
        insert into identity_test(name)
        values(#{name,jdbcType=VARCHAR})
</insert>
```
执行此条插入语句以后，实体类IdentityTest中的Id会被当前插入数据的主键自动填充。
https://blog.csdn.net/qq_29663071/article/details/79486048

**二十八、在mapper中如何传递多个参数？**
* 直接在方法中传递参数，xml文件用#{0} #{1}来获取
* 使用 @param 注解:这样可以直接在xml文件中通过#{name}来获取
https://blog.csdn.net/bangrenzhuce/article/details/52574879

**二十九、resultType resultMap的区别？**
resultType:
使用resultType进行输出映射，只有查询出来的列名和pojo中的属性名一致，该列才可以映射成功。
如果查询出来的列名和pojo中的属性名全部不一致，没有创建pojo对象。
只要查询出来的列名和pojo中的属性有一个一致，就会创建pojo对象。
resultMap:
如果查询出来的列名和pojo的属性名不一致，通过定义一个resultMap对列名和pojo属性名之间作一个映射关系。
定义resultMap
使用resultMap作为statement的输出映射类型

**三十、使用MyBatis的mapper接口调用时有哪些要求？**
* Mapper接口方法名和mapper.xml中定义的每个sql的id相同
* Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同
* Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同
* Mapper.xml文件中的namespace即是mapper接口的类路径。

**OGNL**
 OGNL是Object-Graph Navigation Language（对象图导航语言）的缩写，它是一种功能强大的表达式语言，通过简单一致的表达式语法，可以存取对象的任何属性，调用对象的方法，遍历整个对象的结构图，实现字段类型转化等功能、它使用相同的表达式去存取对象的属性。
 https://blog.csdn.net/isea533/article/details/50061705