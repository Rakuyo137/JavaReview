###### Spring 核心 

IOC :控制反转

AOP:面向切面编程

容器: 包含并管理应用对象的生命周期



##### prepareRefresh

![1662108412534](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662108412534.png)

###### 1.创建准备 Environment对象

Environment 管理各种键值信息 如 SystemProperties / systemEnvironment / 自定义的propertySource

为后续 @Value ,值注入时提供键值



###### 2.obtainFreshBeanFactory

获取(或创建)BeanFactory   

BeanFactory 的作用是负责bean的创建, 依赖注入和初始化

BeanDefinition 作为bean的设计蓝图 ,规定了bean的特征 ,如单例多例、依赖关系、初始化销毁方法等

BeanDefinition可以通过xml 、 配置类 、组件扫描等方式获得

```java
DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();

```



![1662104918192](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662104918192.png)

![1662105528718](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662105528718.png)

3. ###### prepareBeanFactory

   

    ![1662105913395](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662105913395.png)

StandardBeanExpressResolve 解析SpEL

ResourceEditorRegistrar 注释类型转换器 ,应用ApplicationContext 提供的 Environment完成${} 解析

registerResolvableDependency注册 特殊类 beanFactory 以及 ApplicationContext 

ApplicationContextAwareProcessor  来解析Aware 接口

beanPostProcessors 



###### 4.PostProcessBeanFactor

空实现类, 留给子类扩展

用于子类扩展, 一般web环境的ApplicationContext都利用它注册新的Scope 完善Web下的BeanFactory

体现的是 模板方法设计模式



###### 5.invokeBeanFactoryPostProcessors

BeanFactory 后处理器 ,充当BeanFactory的扩展点 ,可以来补充修改beanDefinition

ConfigurationClassPostProcessor - 解析@Configuration @Bean @Import @ProperSource

PropertySourcesPlaceHolderConfigurer 替换beanDefinition中的${}



###### 6.registerBeanPostProcessors

bean的后处理器注册 ,充当bean扩展点 ,可以在bean的实例化\依赖注入\初始化阶段.

 ![1662107252350](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662107252350.png)



###### 7.initMessageSource 实现国际化

实现国际化, 容器中一个名为messageSource的bean ,如果没有 ,则提供空的messageSource实现



###### 8. intiApplicationEventMulticaster 事件广播器

事件广播器 , 发布事件给监听器,  容器中一个名为ApplicationEventMulticaster的bean作为事件广播器 ,如果没有 ,则新建默认的事件广播器

调用ApplicationContext.publishEvent(事件对象)来发布事件



###### 9.onRefresh

空实现,留给子类扩展

SpringBoot中的子类可以在这里准备WebServer ,即内嵌web内容



###### 10.registerListeners 事件监听器

事件监听器, 接收事件

监听器可以来自事先编程添加\容器中的bean \@EventListener的解析

实现ApplicationListener接口,重写其中onApplicationEvent(E e)方法即可

![1662107788410](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662107788410.png)

###### 11.finishBeanFactoryInitialization

conversionService 转换机制,作为对PropertyEditor的补充

内嵌值解析器来解析@value 中的 ${},借用的是Environment的功能

singletonObjects  单例池 缓存单例对象,对象的创建分为创建依赖注入 初始化,每个阶段都有不同的bean后处理器参与扩展功能

###### 12 finishRefresh

创建lifecycleProcessor 生命周期处理器

用来控制容器内需要声明周期管理的bean

调用context的start 和 stop ,即可触发所有实现LifeCycle接口bean的start 和stop

![1662108236533](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662108236533.png)

##### Spring Bean 的生命周期

![1663501132841](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1663501132841.png)







protected <T>  T doGetBean

###### 阶段一 处理名称,检查缓存 

###### ![1663490037983](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1663490037983.png)

三级缓存处理循环依赖



###### 阶段二 处理父子容器

![1663490147992](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1663490147992.png)



###### 阶段三 dependsOn

dependsOn用非显示依赖的bean的创建顺序控制



###### 阶段四 按Scope创建bean 

 在创建bean的时候可以带上scope属性，scope有下面几种类型。 

> **Singleton**

这也是Spring默认的scope，表示Spring容器只创建一个bean的实例，Spring在创建第一次后会缓存起来，之后不再创建，就是设计模式中的单例模式。

> **Prototype**

代表线程每次调用这个bean都新创建一个实例。

> **Request**

表示每个request作用域内的请求只创建一个实例。

> **Session**

表示每个session作用域内的请求只创建一个实例。

> **GlobalSession**

这个只在porlet的web应用程序中才有意义，它映射到porlet的global范围的session，如果普通的web应用使用了这个scope，容器会把它作为普通的session作用域的scope创建。



###### 阶段五 创建bean

![1663490691496](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1663490691496.png)



5-1创建阶段 

![1663492909271](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1663492909271.png)

AutowireAnnotationBeanPostProcessor 选择构造    - 优先选择带@Autowired注解的构造 / 有唯一的带参构造

采用默认构造                                                                    -



5-2 依赖注入

AutowireAnnotationBeanPostProcessor(注解匹配)     识别@Autowired 和 @value

CommonAnnotationBeanPostProcessor(注解匹配)     识别@Resource 

AUTOWIRE_BY_NAME                                                       根据名字匹配

AUTOWIRE_BY_TYPE                                                         根据类型匹配

applyPropertyValues  (即xml中<property name ref|value/>) (精准指定)



优先级    xml > BY_NAME > @Autowired



5-3 初始化    ( 调用AWare接口- 调用初始化方法 - 创建Aop代理)

内置Aware 接口的装配       BeanNameAware , BeanFactoryAware

扩展Aware 接口的装配

初始化方法:

1. @PostConstruct                               Bean后处理器

2. InitializingBean                                 通过接口回调执行初始化

3. initMethod (@Bean(initMethod))   根据BeanDefinition的到的初始化方法执行初始化

创建Aop代理                                              AnnotationAwareAspectJAutoProxyCreator创建 



###### 5-4 注册可销毁的bean

判断依据  :

​	实现了DisposableBean 或 AutoCloseable 接口

​	自定义了destoryMethod

​	自动推断方式获取销毁方法

​    @PreDestroy

存储位置  

​	![1663500525257](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1663500525257.png)



###### 阶段6 类型转换

如果getBean的 requiredType参数与实际得到的对象类型不同,就会尝试类型转换



###### 阶段7 销毁bean

![1663501113711](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1663501113711.png)









##### spring_annottation

###### spring注解

事务 :  @Transactional

​			@EnableTransactionManagement

核心:   @Order   多个bean控制顺序

事件，调度，异步：![1662528876335](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662528876335.png)



切面： **@EnableAspectAutoProxy**   

![1662529776917](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662529776917.png)  

bean后处理器，帮助产生代理，协助aop增强

​			@EnableLoadTimeWeaving

​			@Configurable

（around 、before切面通知注解不属于spring，属于aspect第三方库）

组件扫描与配置类：

@Component

@Controller

@Service

@Repository      ---组件扫描注解，标注该注解的类，组件扫描到它们纳入spring容器管理

@Filter   @ComponentScan 

@ComponentScans  ---包组件扫描

@Conditional    在组件扫描时做条件装配，扫描是判断条件是否成立



@Configuration 配置类

@Bean       配置类中哪些方法作为bean定义

@Import    导入其他配置类于当前容器中

@Lazy       懒加载，加在类上延迟实例化、初始化，加在方法参数上解决循环依赖，



@PropertySource  读取自定义的property文件加载键值信息



缓存：

![1662530468645](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662530468645.png)





依赖注入：

@Autowired

@Qualifier

@value



###### SpringMvc

mapping：  

@RequestMapping  

建立请求路径和控制器方法间的映射关系，请求路径与RequestMapping中的路径匹配

​				@GetMapping等是requestMapping延伸注解

​								---@RequestMapping（method ==RequestMethod.get）



rest:  

@RequestBody  处理请求体中的JSON数据 ,将请求体中的JSON数据转化为JAVA的对象

@ResponseBOdy  将控制器方法返回的JAVA对象转换为JSON数据写入到响应体

@ResponseStatus

@RestController  ----    @ResponseBody + @Controller



统一处理:

@ControllerAdvice  

@ExceptionHandler

@RestControllerAdvice   @ControllerAdvice  + @ResponseBody



获取参数信息:

![1662531500800](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662531500800.png)

转换和格式化:

![1662531546375](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662531546375.png)

validation:  @validated 数据校验

scope: 在spring容器中控制作用域

![1662531641317](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1662531641317.png)

ajax :

@CrossOrigin  跨域



###### SpringBoot

properties:

@ConfigurationProperties    将properties中的键值信息与配置文件绑定,简化配置文件赋值操作

@EnableConfigurationProperties 



condition:实现常用判断条件

@ConditionOnClass   检查是否存在类

@ConditionalOnMissingBean  容器内是否缺失bean, 自动候补bean

@ConditionalOnProperty  包含是否Property键值信息



auto:

@SpringBootConfiguration   组合注解

 @SpringBootConfiguration + @EnableAutoConfiguration  + @ComponentScan



@EnableAutoConfiguration

```
@Configuration  

注意点1:配置类其实相当于一个工厂,标注@bean注解的方法相当于工厂方法

注意点2:@Bean 不支持方法重载,如果有多个重载方法,仅有一个能入选为工厂方法

注意点3:@Configuration 默认会标注的类生成代理,其目的是保证@Bean方法互相调用是,仍然能保证单例特性

bean工厂后处理器  
```

