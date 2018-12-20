#Springboot
## 什么是Springboot
Spring Boot 是所有基于 Spring 开发的项目的起点。Spring Boot 的设计是为了尽可能快的跑起来 Spring 应用程序并且尽可能减少你配置文件。它使用 “习惯优于配置” （项目中存在大量的配置，此外还内置一个习惯性的配置）的理念让你的项目快速运行起来。它并不是什么新的框架，而是默认配置了很多框架的使用方式，就像 Maven 整合了所有的 jar 包一样，Spring Boot 整合了很多常用框架。
## Springboot好处
简单、快速、方便地搭建项目；对主流开发框架的无配置集成；极大提高了开发、部署效率。

@SpringBootApplication 是 Spring Boot 的核心注解，它是一个组合注解，该注解组合了：@Configuration、@EnableAutoConfiguration、@ComponentScan； 若不是用 @SpringBootApplication 注解也可以使用这三个注解代替。
其中，@EnableAutoConfiguration 让 Spring Boot 根据类路径中的 jar 包依赖为当前项目进行自动配置，例如，添加了 spring-boot-starter-web 依赖，会自动添加 Tomcat 和 Spring MVC 的依赖，那么 Spring Boot 会对 Tomcat 和 Spring MVC 进行自动配置。
**Spring Boot 还会自动扫描 @SpringBootApplication 所在类的同级包以及下级包里的 Bean** ，所以入口类建议就配置在 grounpID + arctifactID 组合的包名下（这里为 cn.wmyskxz.springboot 包）

### Jackson
属性注解：
@JsonIgnore：不返回给前端
@JsonInclude(Include.NON_NULL)：值为null的属性不返回

### Springboot异常
* 页面跳转请求发生异常

* ajax请求发生异常

* 同一返回异常形式


@GetMapping = @RequestMapping(method = 
RequestMethod.GET)