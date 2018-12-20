# Spring-AOP
## AOP(Aspect-Oriented Programming)基本概念
- Aspect(切面):通常是一个类，里面可以定义切入点和通知
- JointPoint(连接点):程序执行过程中明确的点，一般是方法的调用
- Advice(通知):AOP在特定的切入点上执行的增强处理，有before,after,afterReturning,afterThrowing,around
- Pointcut(切入点):就是带有通知的连接点，在程序中主要体现为书写切入点表达式
- AOP代理：AOP框架创建的对象，代理就是目标对象的加强。Spring中的AOP代理默认使用JDK动态代理，也可以是CGLIB代理，基于反射技术的实现，后者是基于继承的机制实现。
- 织入（weave）:将切面应用到目标对象并导致代理对象创建的过程
对于织入这个概念，可以简单理解为aspect(切面)应用到目标函数(类)的过程。对于这个过程，一般分为动态织入和静态织入，动态织入的方式是在运行时动态将要增强的代码织入到目标类中，这样往往是通过动态代理技术完成的，如Java JDK的动态代理(Proxy，底层通过反射实现)或者CGLIB的动态代理(底层通过继承实现)，Spring AOP采用的就是基于运行时增强的代理技术，ApectJ采用的就是静态织入的方式。
## 配置
- 基于注解
方法一：
```
@Aspect
public class MyAspect {

    /**
     * 前置通知
     */
    @Before("execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
    public void before(){
        System.out.println("前置通知....");
    }

    /**
     * 后置通知
     * returnVal,切点方法执行后的返回值
     */
    @AfterReturning(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))",returning = "returnVal")
    public void AfterReturning(Object returnVal){
        System.out.println("后置通知...."+returnVal);
    }


    /**
     * 环绕通知
     * @param joinPoint 可用于执行切点的类
     * @return
     * @throws Throwable
     */
    @Around("execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("环绕通知前....");
        Object obj= (Object) joinPoint.proceed();
        System.out.println("环绕通知后....");
        return obj;
    }

    /**
     * 抛出通知
     * @param e
     */
    @AfterThrowing(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))",throwing = "e")
    public void afterThrowable(Throwable e){
        System.out.println("出现异常:msg="+e.getMessage());
    }

    /**
     * 无论什么情况下都会执行的方法
     */
    @After(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
    public void after(){
        System.out.println("最终通知....");
    }
}
```
方法二：
```
/**
 * 使用Pointcut定义切点
 */
@Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
private void myPointcut(){}
/**
 * 应用切入点函数
 */
@After(value="myPointcut()")
public void afterDemo(){
    System.out.println("最终通知....");
}
```
XML:
`<aop:aspectj-autoproxy />`



- 基于XML
切面类：
```
public class MyAspectXML {

    public void before(){
        System.out.println("MyAspectXML====前置通知");
    }

    public void afterReturn(Object returnVal){
        System.out.println("后置通知-->返回值:"+returnVal);
    }

    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("MyAspectXML=====环绕通知前");
        Object object= joinPoint.proceed();
        System.out.println("MyAspectXML=====环绕通知后");
        return object;
    }

    public void afterThrowing(Throwable throwable){
        System.out.println("MyAspectXML======异常通知:"+ throwable.getMessage());
    }

    public void after(){
        System.out.println("MyAspectXML=====最终通知..来了");
    }
}
```
XML:
```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--<context:component-scan base-package=""-->

    <!-- 定义目标对象 -->
    <bean name="productDao" class="com.zejian.spring.springAop.dao.daoimp.ProductDaoImpl" />

    <!-- 定义切面 -->
    <bean name="myAspectXML" class="com.zejian.spring.springAop.AspectJ.MyAspectXML" />
    <!-- 配置AOP 切面 -->
    <aop:config>
        <!-- 定义切点函数 -->
        <aop:pointcut id="pointcut" expression="execution(* com.zejian.spring.springAop.dao.ProductDao.add(..))" />

        <!-- 定义其他切点函数 -->
        <aop:pointcut id="delPointcut" expression="execution(* com.zejian.spring.springAop.dao.ProductDao.delete(..))" />

        <!-- 定义通知 order 定义优先级,值越小优先级越大-->
        <aop:aspect ref="myAspectXML" order="0">
            <!-- 定义通知
            method 指定通知方法名,必须与MyAspectXML中的相同
            pointcut 指定切点函数
            -->
            <aop:before method="before" pointcut-ref="pointcut" />

            <!-- 后置通知  returning="returnVal" 定义返回值 必须与类中声明的名称一样-->
            <aop:after-returning method="afterReturn" pointcut-ref="pointcut"  returning="returnVal" />

            <!-- 环绕通知 -->
            <aop:around method="around" pointcut-ref="pointcut"  />

            <!--异常通知 throwing="throwable" 指定异常通知错误信息变量,必须与类中声明的名称一样-->
            <aop:after-throwing method="afterThrowing" pointcut-ref="pointcut" throwing="throwable"/>

            <!--
                 method : 通知的方法(最终通知)
                 pointcut-ref : 通知应用到的切点方法
            -->
            <aop:after method="after" pointcut-ref="pointcut"/>
        </aop:aspect>
    </aop:config>
</beans>
```
## 通知类型
- Before
前置通知：在目标方法被调用之前做增强处理,@Before只需要指定切入点表达式即可
- AfterReturning
后置通知：在目标方法正常完成后做增强,@AfterReturning除了指定切入点表达式后，还可以指定一个返回值形参名returning,代表目标方法的返回值
- AfterThrowing
异常通知：主要用来处理程序中未处理的异常,@AfterThrowing除了指定切入点表达式后，还可以指定一个throwing的返回值形参名,可以通过该形参名来访问目标方法中所抛出的异常对象
- After
最终通知：在目标方法完成之后做增强，无论目标方法时候成功完成。@After可以指定一个切入点表达式
- Around
环绕通知：环绕通知,在目标方法完成前后做增强处理,环绕通知是最重要的通知类型,像事务,日志等都是环绕通知,注意编程中核心是一个ProceedingJoinPoint。做环绕通知的时候，调用ProceedingJoinPoint的proceed()方法才会执行目标方法。

- **通知执行的优先级**
进入目标方法时,先织入Around,再织入Before，退出目标方法时，先织入Around,再织入AfterReturning,最后才织入After。

- **通知传递参数**
在Spring AOP中，除了execution和bean指示符不能传递参数给通知方法，其他指示符都可以将匹配的方法相应参数或对象自动传递给通知方法。获取到匹配的方法参数后通过”argNames”属性指定参数名。如下，需要注意的是args(指示符)、argNames的参数名与before()方法中参数名 必须保持一致即param。
```
@Before(value="args(param)", argNames="param") //明确指定了    
public void before(int param) {    
    System.out.println("param:" + param);    
}  
```
当然也可以直接使用args指示符不带argNames声明参数，如下：
```
@Before("execution(public * com.zejian..*.addUser(..)) && args(userId,..)")  
public void before(int userId) {  
    //调用addUser的方法时如果与addUser的参数匹配则会传递进来会传递进来
    System.out.println("userId:" + userId);  
}  
```

## Spring AOP支持的切入点指示符：
#### 通配符
- .. ：匹配方法定义中的任意数量的参数，此外还匹配类定义中的任意数量包
```
//任意返回值，任意名称，任意参数的公共方法
execution(public * *(..))
//匹配com.zejian.dao包及其子包中所有类中的所有方法
within(com.zejian.dao..*)
```
- ＋ ：匹配给定类的任意子类
```
//匹配实现了DaoUser接口的所有子类的方法
within(com.zejian.dao.DaoUser+)
```
- ＊ ：匹配任意数量的字符
```
//匹配com.zejian.service包及其子包中所有类的所有方法
within(com.zejian.service..*)
//匹配以set开头，参数为int类型，任意返回值的方法
execution(* set*(int))
```

#### 类型签名表达式
`within(<type name>)`
type name 则使用包名或者类名替换即可
```
//匹配com.zejian.dao包及其子包中所有类中的所有方法
@Pointcut("within(com.zejian.dao..*)")

//匹配UserDaoImpl类中所有方法
@Pointcut("within(com.zejian.dao.UserDaoImpl)")

//匹配UserDaoImpl类及其子类中所有方法
@Pointcut("within(com.zejian.dao.UserDaoImpl+)")

//匹配所有实现UserDao接口的类的所有方法
@Pointcut("within(com.zejian.dao.UserDao+)")
```

#### 方法签名表达式
果想根据方法签名进行过滤，关键字execution可以帮到我们，语法表达式如下
```
//scope ：方法作用域，如public,private,protect
//returnt-type：方法返回值类型
//fully-qualified-class-name：方法所在类的完全限定名称
//parameters 方法参数
execution(<scope> <return-type> <fully-qualified-class-name>.*(parameters))
```
```
//匹配UserDaoImpl类中的所有方法
@Pointcut("execution(* com.zejian.dao.UserDaoImpl.*(..))")

//匹配UserDaoImpl类中的所有公共的方法
@Pointcut("execution(public * com.zejian.dao.UserDaoImpl.*(..))")

//匹配UserDaoImpl类中的所有公共方法并且返回值为int类型
@Pointcut("execution(public int com.zejian.dao.UserDaoImpl.*(..))")

//匹配UserDaoImpl类中第一个参数为int类型的所有公共的方法
@Pointcut("execution(public * com.zejian.dao.UserDaoImpl.*(int , ..))")
```

### 其他指示符
- bean：Spring AOP扩展的，AspectJ没有对于指示符，用于匹配特定名称的Bean对象的执行方法；
```
//匹配名称中带有后缀Service的Bean。
@Pointcut("bean(*Service)")
private void myPointcut1(){}
```
- this ：用于匹配当前AOP代理对象类型的执行方法；请注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配
```
//匹配了任意实现了UserDao接口的代理对象的方法进行过滤
@Pointcut("this(com.zejian.spring.springAop.dao.UserDao)")
private void myPointcut2(){}
```
- target ：用于匹配当前目标对象类型的执行方法；
```
//匹配了任意实现了UserDao接口的目标对象的方法进行过滤
@Pointcut("target(com.zejian.spring.springAop.dao.UserDao)")
private void myPointcut3(){}
```
- @within：用于匹配所以持有指定注解类型内的方法；请注意与within是有区别的， within是用于匹配指定类型内的方法执行；
```
//匹配使用了MarkerAnnotation注解的类(注意是类)
@Pointcut("@within(com.zejian.spring.annotation.MarkerAnnotation)")
private void myPointcut4(){}
```
- @annotation(com.zejian.spring.MarkerMethodAnnotation) : 根据所应用的注解进行方法过滤
```
//匹配使用了MarkerAnnotation注解的方法(注意是方法)
@Pointcut("@annotation(com.zejian.spring.annotation.MarkerAnnotation)")
private void myPointcut5(){}
```
**切点指示符可以使用运算符语法进行表达式的混编，如and、or、not（或者&&、||、！）**
```
//匹配了任意实现了UserDao接口的目标对象的方法并且该接口不在com.zejian.dao包及其子包下
@Pointcut("target(com.zejian.spring.springAop.dao.UserDao) ！within(com.zejian.dao..*)")
private void myPointcut6(){}
//匹配了任意实现了UserDao接口的目标对象的方法并且该方法名称为addUser
@Pointcut("target(com.zejian.spring.springAop.dao.UserDao)&&execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
private void myPointcut7(){}
```

## Aspect优先级
在不同的切面中，如果有多个通知需要在同一个切点函数指定的过滤目标方法上执行，那些在目标方法前执行(”进入”)的通知函数，最高优先级的通知将会先执行，在执行在目标方法后执行(“退出”)的通知函数，最高优先级会最后执行。而对于在同一个切面定义的通知函数将会根据在类中的声明顺序执行。如下：
```
@Aspect
public class AspectOne {

    /**
     * Pointcut定义切点函数
     */
    @Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.deleteUser(..))")
    private void myPointcut(){}

    @Before("myPointcut()")
    public void beforeOne(){
        System.out.println("前置通知....执行顺序1");
    }

    @Before("myPointcut()")
    public void beforeTwo(){
        System.out.println("前置通知....执行顺序2");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningThree(){
        System.out.println("后置通知....执行顺序3");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningFour(){
        System.out.println("后置通知....执行顺序4");
    }
}
```
在同一个切面中定义多个通知响应同一个切点函数，执行顺序为声明顺序：
!["执行顺序"](https://pic4.zhimg.com/80/v2-e675e3d76d07b08ca65711486cc11bd3_hd.png "执行顺序")
如果在不同的切面中定义多个通知响应同一个切点，进入时则优先级高的切面类中的通知函数优先执行，退出时则最后执行，如下定义AspectOne类和AspectTwo类并实现org.springframework.core.Ordered 接口，该接口用于控制切面类的优先级，同时重写getOrder方法，定制返回值，返回值(int 类型)越小优先级越大。其中AspectOne返回值为0，AspectTwo的返回值为3，显然AspectOne优先级高于AspectTwo。
```
@Aspect
public class AspectOne implements Ordered {

    /**
     * Pointcut定义切点函数
     */
    @Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.deleteUser(..))")
    private void myPointcut(){}

    @Before("myPointcut()")
    public void beforeOne(){
        System.out.println("前置通知..AspectOne..执行顺序1");
    }

    @Before("myPointcut()")
    public void beforeTwo(){
        System.out.println("前置通知..AspectOne..执行顺序2");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningThree(){
        System.out.println("后置通知..AspectOne..执行顺序3");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningFour(){
        System.out.println("后置通知..AspectOne..执行顺序4");
    }

    /**
     * 定义优先级,值越低,优先级越高
     * @return
     */
    @Override
    public int getOrder() {
        return 0;
    }
}


//切面类 AspectTwo.java
@Aspect
public class AspectTwo implements Ordered {

    /**
     * Pointcut定义切点函数
     */
    @Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.deleteUser(..))")
    private void myPointcut(){}

    @Before("myPointcut()")
    public void beforeOne(){
        System.out.println("前置通知....执行顺序1--AspectTwo");
    }

    @Before("myPointcut()")
    public void beforeTwo(){
        System.out.println("前置通知....执行顺序2--AspectTwo");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningThree(){
        System.out.println("后置通知....执行顺序3--AspectTwo");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningFour(){
        System.out.println("后置通知....执行顺序4--AspectTwo");
    }

    /**
     * 定义优先级,值越低,优先级越高
     * @return
     */
    @Override
    public int getOrder() {
        return 2;
    }
}
```
运行结果如下：
![运行结果](https://pic4.zhimg.com/80/v2-cabe31d72fc9b8ef8a3fee088e8b3e5f_hd.png "运行结果")
**其他类型通知遵循相同规则**
在配置文件中启动@Aspect支持后，Spring容器只会尝试自动识别带@Aspect的Bean，前提是任何定义的切面类都必须已在配置文件以Bean的形式声明。(类似于＜context:annotation-config/＞,如果用纯注解，还需要扫描，但是< context:component-scan base-package="">< /context:component-scan> 不包括 < aop:aspectj-autoproxy />)
```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--<context:component-scan base-package=""-->
    <!-- 启动@aspectj的自动代理支持-->
    <aop:aspectj-autoproxy />

    <!-- 定义目标对象 -->
    <bean id="userDaos" class="com.zejian.spring.springAop.dao.daoimp.UserDaoImp" />
    <!-- 定义aspect类 -->
    <bean name="myAspectJ" class="com.zejian.spring.springAop.AspectJ.MyAspect"/>

    <bean name="aspectOne" class="com.zejian.spring.springAop.AspectJ.AspectOne"  />

    <bean name="aspectTwo" class="com.zejian.spring.springAop.AspectJ.AspectTwo" />
</beans>
```

两者实现AOP的底层原理不太一样：
Spring AOP: 基于代理(Proxying)
AspectJ: 基于字节码操作(Bytecode Manipulation)