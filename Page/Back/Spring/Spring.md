# Spring

[代码详情上](https://gitee.com/floatingdream1001/isses-pt-repo/tree/master/springboot0)

[代码详情下](https://gitee.com/floatingdream1001/isses-pt-repo/tree/master/springboot1)

## spring是什么

Spring是一个开源框架，2003年兴起的一个轻量级的Java开发框架，作者:Rod Johnson 。
**Spring是为了解决企业级应用开发的复杂性而创建的，简化开发。**

## Spring是如何简化Java开发的

为了降低Java开发的复杂性，Spring采用了以下4种关键策略:
1、基于POJO的轻量级和最小侵入性编程;
2、通过IOC，依赖注入(DI)和面向接口实现松耦合;
3、基于切面(AOP)和惯例进行声明式编程;
4、通过切面和模版减少样式代码;



自动装配： 加载META-INF/spring.factories, "META-INF/spring/%s.imports


@SpringBootAppliction: 启动类

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)  //存活在运行时
@Documented  //文档化
@Inherited   //继承
@SpringBootConfiguration  //并一下
@EnableAutoConfiguration  //自动装配注解
@ComponentScan(   //扫描Component注解，凡有Component注解的类，都会加到容器里
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
```

## @Target

```java
@Target(ElementType.TYPE)  //Target用于修饰内容
public @interface AAA {
}

public enum ElementType {
    /**用于描述类、接口(包括注解类型) 或enum声明 Class, interface (including annotation type), or enum declaration */
    TYPE,
 
    /** 用于描述域 Field declaration (includes enum constants) */
    FIELD,
 
    /**用于描述方法 Method declaration */
    METHOD,
 
    /**用于描述参数 Formal parameter declaration */
    PARAMETER,
 
    /**用于描述构造器 Constructor declaration */
    CONSTRUCTOR,
 
    /**用于描述局部变量 Local variable declaration */
    LOCAL_VARIABLE,
 
    /** Annotation type declaration */
    ANNOTATION_TYPE,
 
    /**用于描述包 Package declaration */
    PACKAGE,
 
    /**
     * 用来标注类型参数 Type parameter declaration
     * @since 1.8
     */
    TYPE_PARAMETER,
 
    /**
     *能标注任何类型名称 Use of a type
     * @since 1.8
     */
    TYPE_USE
```

在Springboot0Application下

```java
//项目入口
@SpringBootApplication  //启动库

@AAA//自定义注解，只能修饰类
public class Springboot0Application {

    public static void main ( String[] args ) {
        SpringApplication.run ( Springboot0Application.class, args );  //SpringApplication应用运行  Springboot0Application当前类
    }

}
```


@Configuration   与@Component 并起来会使类容器化去放对象，只是Configuration会形成代理容器

@Controller与@Service@Repository（居于业务层和数据层之间）@Component只是语义不同，一个是控制器一个是服务，一个是数据仓库

## IOC: 控制反转：

创建对象的过程交给容器：

**@Configuration:  bean一下（对当前对象实例化并管理且加入代理）,   自动cglib代理（不需要接口）对象。**

**@Component:  组件： 允许在Spring IOC对当前对象实例化并管理**

+ **@Controller:  控制器**

+ **@Service:  服务类**

+ **@Repository ：  仓库**  数据的持久层

  >以上类里是@Target({ElementType.TYPE})是修饰类的

+ **@Bean：  用来自定义容器，返回值放入容器， 如果有参数可以自动注入，如果是方法就实例化一下**

  > 它下面是@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})，用来修饰方法或者ANNOTATION类
  >
  
  用方法通过返回值实例化
  
  ```java
  @Configuration  //实例化一下
  public class GGG {
  
      @Bean
      public EEE netinstanc(DDD ddd){   //返回一个netinstanc(容器名字)
          System.out.println ("++++++++++"+ddd );
          return new EEE();
      }
  }
  ```

E什么也没有

![image-20230526163856697](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230526163856697.png)

![image-20230526164725227](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230526164725227.png)

@Scope: @Scope("prototype")取消默认单例

※：容器中（ApplicationContext）对象默认是单例。可以通过Scope("prototype");每次获取的时候，重新实例化。

```java
@Configuration  //代理容器
@Scope("prototype")
public class AAA {
}
```

没取之前：

![image-20230526170139062](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230526170139062.png)

取之后：

![image-20230526170245235](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230526170245235.png)

```java
//项目入口
@SpringBootApplication  //启动库

//自定义注解，只能修饰类
public class Springboot0Application implements CommandLineRunner {  //生成 void run 用来回调

    public static void main ( String[] args ) {
        SpringApplication.run ( Springboot0Application.class, args );  //SpringApplication应用运行  Springboot0Application当前类
    }

    //DI注入
    @Autowired
    //从容器中把内容取出来
        ApplicationContext context;

    @Override  //启动完回调
    public void run ( String... args ) throws Exception {

        String[] beannames = context.getBeanDefinitionNames ( );
        Stream.of ( beannames ).forEach  ( item -> {
            //把容器中的内容取出来
            System.out.println ( "name:" + item + "@@@" + context.getBean ( item ) + "@@@@@" + context.getBean ( item ).getClass ( ) );
            System.out.println ( );
        } );

        System.out.println ("---------------------------------------------" );
        AAA a1 = context.getBean ( AAA.class ); //取类
        AAA a2 = context.getBean ( AAA.class ); //取类
        System.out.println (a1 );
        System.out.println (a2 );

    }
}
```

### DI:  依赖注入

- @Autowired:  bytype根据类型  注入属性  (a1,a2,a3)
- @Resource:  jdk注解 byname   (a4,a5)
- @Qualifier("myhhh0"):  @Autowired+@Qualifier等同于byname   (a6)   是spring注解
- @Value("");注入基本类型，或者通过${"配置的值"}

```java
//项目入口
@SpringBootApplication  //启动库

//自定义注解，只能修饰类
public class Springboot0Application implements CommandLineRunner {  //生成 void run 用来回调

    public static void main ( String[] args ) {
        SpringApplication.run ( Springboot0Application.class, args );  //SpringApplication应用运行  Springboot0Application当前类
    }

    //DI注入
    @Autowired
    //从容器中把内容取出来
    ApplicationContext context;

    @Autowired
    AAA a3;

    @Resource(name = "AAA")
    AAA a5;

    @Autowired
    @Qualifier("AAA")
    AAA a6;

    @Override  //启动完回调
    public void run ( String... args ) throws Exception {

        String[] beannames = context.getBeanDefinitionNames ( );
        Stream.of ( beannames ).forEach  ( item -> {
            //把容器中的内容取出来
            System.out.println ( "name:" + item + "@@@" + context.getBean ( item ) + "@@@@@" + context.getBean ( item ).getClass ( ) );
            System.out.println ( );
        } );
        System.out.println ("---------------------------------------------" );
        AAA a1 = context.getBean ( AAA.class ); //取类
        AAA a2 = context.getBean ( AAA.class ); //取类
        System.out.println (a1 );
        System.out.println (a2 );
        System.out.println (a3 );
        Object a4 = context.getBean ( "AAA" );
        System.out.println (a4 );
        System.out.println (a5 );
        System.out.println (a6 );
    }
}
```

![image-20230526171747247](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230526171747247.png)

**by_type和by_name的区别：**

如果一个父类被多个子类继承，子类被并入容器，而父类通过by_type是无法注入属性，只能通过by_name 来

**示例**

**父类HHH:**

```java
public class HHH {
}
```

**子类HHH0：**

```java
@Component(value = "hhh0")
public class HHH0 extends HHH {  //类继承

}
```

**子类HHH1：**

```java
@Component
public class HHH1  extends HHH{

}
```

**属性注入：**

```java
//项目入口
@SpringBootApplication  //启动库

//自定义注解，只能修饰类
public class Springboot0Application implements CommandLineRunner {  //生成 void run 用来回调

    public static void main ( String[] args ) {
        SpringApplication.run ( Springboot0Application.class, args );  //SpringApplication应用运行  Springboot0Application当前类
    }

    @Autowired
    @Qualifier("hhh0")
    HHH a7;

    @Override  //启动完回调
    public void run ( String... args ) throws Exception {

        String[] beannames = context.getBeanDefinitionNames ( );
        Stream.of ( beannames ).forEach  ( item -> {
            //把容器中的内容取出来
            System.out.println ( "name:" + item + "@@@" + context.getBean ( item ) + "@@@@@" + context.getBean ( item ).getClass ( ) );
            System.out.println ( );
        } );
        System.out.println (a7 );
    }
}
```

![image-20230526214234982](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230526214234982.png)

## AOP: 切面编程：

- 切面： 一个面，面上有切点
- 切点： 要做处理的点
- 处理（通知）：
  - 前置   @Before
  - 后置    @After
  - 环绕    @Around
  - 异常    @AfterRetruning
  - finally  @AfterThrowing

### 代理模式：
目标是为了在执行之前，之后做处理。

####   **静态代理：**

（个人理解，一个类代理另一个类的方法）

   如果目标Target加入新的方法，那么静态代理也要添加新的方法

##### 代码

**Target:目标**

```java
public class Target {
    public void aaa(){
        System.out.println ("_________@aaa()___________" );
        System.out.println (1/0 );
    }
}
```

**Statictargetproxy静态代理**

```java
public class Statictargetproxy {

    private Target target;  //私有化

    public Statictargetproxy ( Target target ) {  //构造方法，初始化属性（有参构造）
        this.target = target;
    }

    public Statictargetproxy(){  //无参构造，有参构造出现必须先有无参构造，这叫规约

    }

    public void aaa(){
        try{
            System.out.println ("———————————before———————————" );
            target.aaa();
            System.out.println ("———————————after———————————" );
        }catch (Exception e){
            System.out.println ("———————————exception———————————" );
        }finally{
            System.out.println ("———————————finally———————————" );
        }
    }
}
```

**main:**

```java
public class Targetmain {
    public static void main ( String[] args ) {
        Target t=new Target();  //目标
        Statictargetproxy sp=new Statictargetproxy(t);//静态代理
        sp.aaa ();


    }
}
```

#### **动态代理：**

（一个类代理另一个类的方法，并且添加新方法时不需要像静态代理一样，在从新调用）

+ JDK动态代理，要有接口， InvocationHandler接口。

+ CGLIB动态代理。不需要接口    EnhancerBySpringCGLIB


##### 代码

**定义一个接口：Itarget**

```java
//接口
public interface Itarget {

    void aaa();
    void bbb();
    void ccc();
}
```

**Target实现类**

```java
//实现类，实现接口的抽象方法
public class Target implements Itarget{

    public void aaa ( ) {
        System.out.println ("--------Target@AAAA-----------" );
    }


    public void bbb ( ) {
        System.out.println ("--------Target@BBBB-----------" );
    }

    public void ccc ( ) {
        System.out.println ("--------Target@CCCC-----------" );

    }
}
```

**动态代理：**

通过将Itarget实例化，执行 执行句柄，句柄里method反射target里方法，来实现动态代理

```java
//动态代理
public class JDKDynamicProxy implements InvocationHandler {  //实现一个接口，InvocationHandler(执行句柄,每一次执行方法的时候都会运行它)



    Itarget itarget;

    public JDKDynamicProxy ( Itarget itarget ) {  //构造器有参，初始化属性
        this.itarget = itarget;
    }

    public JDKDynamicProxy ( ) {
    }
    //执行句柄：每次执行的时候都会调用共同的部分
    @Override
    public Object invoke ( Object proxy, Method method, Object[] args ) throws Throwable {
        //method反射方法
        Object o=null;
        try{
            System.out.println ("———————————before———————————" );
            //执行下去

            //相当于itarget.aaa(args)；
            // itarget.bbb(args)；
            o = method.invoke ( itarget, args );
            System.out.println ("———————————after———————————" );
        }catch (Exception e){
            System.out.println ("———————————exception———————————" );
        }finally{
            System.out.println ("———————————finally———————————" );
        }
        return o;
    }

    public Itarget newInstance ( ){  //实例化
        return (Itarget) Proxy.newProxyInstance ( JDKDynamicProxy.class.getClassLoader (), new Class[]{Itarget.class},this);
        //（当前类加载器，接口的反射，this执行它）
        //当前类的加载器，去返回接口中的所有方法，this执行这个执行句柄
    }
}
```

**main**

虽然在动态代理中是用接口Itarget,但其实真这个的还是通过target，只不过用多态的方式将他呈现为Itarget.

```java
public class DynamicMain {
    public static void main ( String[] args ) {
        Itarget itarget = new Target ( );   //多态
        JDKDynamicProxy proxyfactory = new JDKDynamicProxy ( itarget );

        Itarget proxy = proxyfactory.newInstance();  //代理对象
        proxy.aaa ();
        proxy.bbb ();
        proxy.ccc ();
        System.out.println (proxy.getClass () );  //打印类名

        //proxy的类是com.sun.proxy.$Proxy0而并不是Itarget，只是将它代理
    }
}
```

### AOP:

@Aspect: 定义切面类

@Pointcut：切点

五种通知：@Before/@After/@Around/@AfterRetruning/AfterThrowing

1. @Before：在目标方法执行之前执行切面逻辑。可以在@Before注解标注的方法中编写需要在目标方法执行前执行的代码。
2. @After：在目标方法执行之后（包括正常返回和抛出异常）执行切面逻辑。可以在@After注解标注的方法中编写需要在目标方法执行后执行的代码。
3. @Around：在目标方法执行前后都执行切面逻辑。@Around注解标注的方法具有最大的灵活性，可以完全控制目标方法的执行，包括在方法执行前、执行后和异常发生时执行不同的逻辑。
4. @AfterReturning：在目标方法成功返回之后执行切面逻辑。该注解只在目标方法成功返回时才会执行，可以在@AfterReturning注解标注的方法中编写需要在目标方法成功返回后执行的代码。
5. @AfterThrowing：在目标方法抛出异常时执行切面逻辑。该注解只在目标方法抛出异常时才会执行，可以在@AfterThrowing注解标注的方法中编写需要在目标方法抛出异常后执行的代码。

**目录结构**

![image-20230527192725838](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230527192725838.png)

**首先创建一个接口和实现类**

```java
//接口
public interface Orderserviceinf {

    public void aaa();
}
```

```java
@Service
//实现类
public class OrderserviceImp implements Orderserviceinf {


    @Override
    public void aaa ( ) {
        System.out.println ("_________目标____________" );
    }
}
```

**接着定义切面和切点，首先是前置通知**

```java
@Component
@Aspect //声明这个类是切面
public class Myaspect {

    /**
     *   *代表一个  ..代表多个
     *   任意返回值
     */

    //execution(* com.codingzhi.springboot0.service.imp.*.*(..))   （方法的返回值 把imp包下的所有类作为切点）
    @Pointcut("execution(* com.codingzhi.springboot0.service.imp.*.*(..))")  //切点,需要写通配
    public void Sign(){  //标记方法

    }

    @Before (  "Sign()") //@Before声明它是这个before切点
    //Before (  "Sign()")要在这个切点上做before通知
    public void before() {
        System.out.println ("-------Befor前置通知--------" );
    }
}
```

**最后在Application执行一下（因为我是在springboot项目下写的）**

```java
@Autowired
Orderserviceinf serviceImp;

@Override  //启动完回调
    public void run ( String... args ) throws Exception {
        System.out.println ("_________________________________________" );
        System.out.println (serviceImp );
        System.out.println (serviceImp.getClass () );
        serviceImp.aaa ();

    }
```

**会发现它在目标之前加了一个前置操作**

![image-20230527193817484](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230527193817484.png)

**Around**

```java
@Component
@Aspect //声明这个类是切面
public class Myaspect {

        //execution(* com.codingzhi.springboot0.service.imp.*.*(..))   （方法的返回值 把imp包下的所有类作为切点）
    @Pointcut("execution(* com.codingzhi.springboot0.service.imp.*.*(..))")  //切点,需要写通配
    public void Sign(){  //标记方法

    }
    @Around("Sign()")//@Around
    public Object around( ProceedingJoinPoint joinPoint) {
        Object o = null;
        try {
            System.out.println("---Around befor 加1块-----");
            o = joinPoint.proceed();
            System.out.println("---Around after 加1块-----");
        } catch (Throwable e) {
            System.out.println("---Around exception 加1块-----");
        } finally {
            System.out.println("---Around finally 加1块-----");
        }
        return o;
    }
}
```

### 开启JDK动态代理

OrderserviceImp是有接口的，UserServiceImp没有使用接口，除此之外其他一样

根据结果，我们发现，使用接口和没使用接口的在加上[以下操纵](#如何在springboot下开启JDK动态代理)一个是JDK动态代理，一个则是00CGLIB动态代理

![image-20230527201109681](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230527201109681.png)

## 自定义注解

参考官方定义的注解，来自定义一个

**官方注解**

![image-20230527203928977](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230527203928977.png)

**自定义**

![image-20230528181120031](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230528181120031.png)

**接着在切面类下**

```java
@Component
@Aspect //声明这个类是切面
@Slf4j  //用来给后面log。debug
public class Myaspect {

    @Pointcut("@annotation(com.codingzhi.springboot0.aspect.Logeee)")  //切点,需要写通配
    public void Sign2(){  //标记方法   //joinPoint连接点的执行方法

    }

    @Around("Sign2()")
    public Object aroundLog(ProceedingJoinPoint joinPoint) throws Throwable {
        long st=new Date().getTime();  //计算此时系统时间
        //执行目标方法，获取方法返回值
        Object proceed = joinPoint.proceed ( );
        long et = new Date ( ).getTime ( );

        ObjectMapper mapper = new ObjectMapper();  //import com.fasterxml.jackson.databind.ObjectMapper;
        //获取joinPoint参数并将其JSON序列化
        String josnPram = mapper.writeValueAsString ( joinPoint.getArgs ( ) );
//        将返回结果JSON序列化
        String jsonResult = null;
        if ( proceed != null ) {
            //将目标返回值包装JSON
            jsonResult = mapper.writeValueAsString ( proceed );
        }
        else {
            jsonResult = "null";

        }
        //模拟上报结果
        log.debug("正在上报过程:\ntarget:{}.{}()\nexecution:{}ms,\nparameter:{}\nresult:{}",
            joinPoint.getTarget ().getClass ().getSimpleName (),
            joinPoint.getSignature ().getName (),
            (et-st),
            josnPram,
            jsonResult);
        return proceed;

    }
}
```

接着就在方法上加上相应注解调取利用Around循环通知来实现其功能。

## 自动装配

### 原理

引入jar,配置一下property或yml

- @SpringBootApplication

  ```java
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Inherited
  @SpringBootConfiguration
  @EnableAutoConfiguration
  @ComponentScan(
      excludeFilters = {@Filter(
      type = FilterType.CUSTOM,
      classes = {TypeExcludeFilter.class}
  ), @Filter(
      type = FilterType.CUSTOM,
      classes = {AutoConfigurationExcludeFilter.class}
  )}
  )
  public @interface SpringBootApplication {
  ```

- EnableAutoConfiguration)

  import(selector):   

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```

- AutoConfigurationImportSelector

- 中SpringFactoriesLoader

  ![image-20230529161438396](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529161438396.png)

- 可以自定义starter

META-INF项目的版本和说明

### 打包，再其他项目引入后，自动装配

前期准备在pom.xml下

![image-20230529144942225](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529144942225.png)

```xml
<build>  <!--用来打包-->
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <classifier>exec</classifier>  //引入这个
                <excludes>
                    <exclude>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```

打包

![image-20230529141517624](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529141517624.png)

接着发布

![image-20230529141543242](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529141543242.png)

接着会在本地的.m2下面找到和你项目对应的文件夹下，你的jar包

![image-20230529141856168](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529141856168.png)

![image-20230529141740408](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529141740408.png)

然后再你需要引用的项目pom.xml下引入

```xml
<dependency>
    <groupId>com.codingzhi</groupId>
    <artifactId>springboot1</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

接着更新一下，引入成功

![image-20230529145329831](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529145329831.png)

### **例子：**

假设springboot1是我要开源的工程，springboot0是我要引入开源工程的正在开发项目

#### Springboot1下

![image-20230529151926535](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529151926535.png)

spring.factories下

```properties
#指定这个启动类
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.codingzhi.springboot1.MyConfiguration
```

MyConfiguration下
```java
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Date;

@Configuration
@EnableConfigurationProperties({Student.class})  //加载MyConfiguration的时候加载Student类
@ComponentScan("com.codingzhi")  //用于指定要扫描的包路径，以查找并注册 Spring 组件（例如带有 @Component、@Service、@Repository 或 @Controller 等注解的类）。
public class MyConfiguration {

    @Bean
    public Date mystu(){
        return new Date ( );
    }
}
```

Student下

```java
package com.codingzhi.springboot1;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.beans.factory.annotation.Value;

@ConfigurationProperties("com.codingzhi")  //用于将外部配置文件中的属性值绑定到 Spring Boot 应用程序的配置类中。
public class Student {
    //属性注入
    @Value("${com.codingzhi.name}")
    private String name;
    @Value("${com.codingzhi.age}")

    private int age;

    public String getName ( ) {
        return name;
    }

    public void setName ( String name ) {
        this.name = name;
    }

    public int getAge ( ) {
        return age;
    }

    public void setAge ( int age ) {
        this.age = age;
    }
}
```

#### 引入项目SprIngboot0

application.properties下

```properties
com.codingzhi.age=10  #控制输出值
com.codingzhi.name="Tom"
```

再启动类下

```java
Student stu =(Student) context.getBean ( com.codingzhi.springboot1.Student.class);
System.out.println (stu.getName ()+" "+stu.getAge () );
```

结果：可以控制使用装配下方法属性等等

![image-20230529153047194](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529153047194.png)

>@ConditionalOnProperty(prefix = "com.codingzhi",value = "enabled",matchIfMissing = false)  
>
>//判断com.codingzhi组下，enabled的值，默认是否，再引入项目下application.properties配置值（true/false）
>
>//条件注解，用于根据配置属性的值来决定是否启用或禁用 Spring Bean 的加载和配置

## 补充

### **try{}catch{}finally{}介绍**

`try{}catch{}finally{}`是异常处理的一种机制。它允许你编写处理可能抛出异常的代码，并在异常发生时执行相应的操作。`try`块包含可能会引发异常的代码。当程序在`try`块中执行时，如果发生异常，它会被抛出并传递给相应的`catch`块进行处理。`catch`块用于捕获和处理特定类型的异常。你可以指定一个或多个`catch`块来处理不同类型的异常，或者使用通用的`catch(Exception e)`来处理所有类型的异常。当异常被捕获时，相应的处理代码将被执行。无论是否发生异常，`finally`块中的代码都会被执行。`finally`块通常用于释放资源或执行必要的清理操作，无论异常是否发生。即使在`try`块或`catch`块中有`return`语句，`finally`块也会在方法返回之前被执行。

**总结：try{}中一般包含异常代码，出问题后将异常抛给catch,catch根据设定异常类型接收相应的异常进行处理，而`catch(Exception e)`来处理所有类型的异常，finally是无论try和catch中是否有异常或者return，finally下的程序都会被执行，`finally`块通常用于释放资源或执行必要的清理操作。**

下面是一个简单的示例，演示了`try{}catch{}finally{}`的用法：

```java
try {
    // 可能引发异常的代码
    int result = 10 / 0; // 这里会抛出一个ArithmeticException异常
    System.out.println("这行代码不会被执行");
} catch (ArithmeticException e) {
    // 处理ArithmeticException异常
    System.out.println("发生了算术异常: " + e.getMessage());
} finally {
    // 无论异常是否发生，这里的代码都会被执行
    System.out.println("执行finally块中的代码");
}
```

在上面的示例中，由于除数为零，会抛出`ArithmeticException`异常。这个异常会被`catch`块捕获并处理，输出相应的错误消息。然后，无论异常是否发生，`finally`块中的代码都会被执行，输出"执行finally块中的代码"。



### **@Override：**

**简单来说：@Override：用来注释下方方法覆盖父类的方法，并提供了一种编译时的检查机制，提高代码的可读性和可维护性。但只能应用于方法。**

`@Override`是一个Java注解（Annotation），用于指示被注解的方法是覆盖（重写）了父类中的方法。它提供了一种编译时的检查机制，用于确保被注解的方法确实是有效地覆盖了父类的方法。

当你在子类中使用`@Override`注解来修饰一个方法时，编译器会检查该方法是否满足以下条件：

1. 方法签名必须与父类中的被覆盖方法完全一致，包括方法名、返回类型和参数列表。
2. 方法必须覆盖一个实际存在的父类方法，即在父类中有相同签名的方法。
3. 访问修饰符不能更严格，即子类方法的访问修饰符不能比父类方法的访问修饰符更加限制性（例如，如果父类方法是`public`，那么子类方法可以是`public`或`protected`，但不能是`private`）。
4. 子类方法不能抛出比父类方法更多的已检查异常，但可以不抛出异常或抛出更少的异常。
5. 方法不能被声明为`static`，因为静态方法不能被重写。

如果被注解的方法不满足以上任何一个条件，编译器将会产生一个编译错误，以提醒你进行修正。

使用`@Override`注解有以下几个好处：

1. 明确表明你的意图，即要覆盖父类的方法，提高代码的可读性和可维护性。
2. 在编译时提供了额外的检查，帮助你发现方法签名不一致等潜在错误。
3. 在使用IDE（集成开发环境）时，它可以为你自动生成`@Override`注解，减少手动编写的工作量。

注意：`@Override`注解只能应用于方法，而不能应用于类、字段或其他类型的成员。

### **implements**（实现接口里所有方法，可以额外加上其他别的方法）

`implements`关键字用于表示一个类实现了一个或多个接口，并确保该类提供了接口中定义的所有方法的具体实现。通过实现接口，可以实现接口的方法规范，实现类的多态性，并实现代码的灵活性和可扩展性。

关键字"implements"用于表示一个类实现（implements）一个接口。接口（interface）是一种抽象类型，它定义了一组方法的签名（方法名、参数列表和返回类型），但没有具体的实现。

当一个类声明它实现一个接口时，它必须提供接口中定义的所有方法的具体实现。必须实现接口中声明的所有方法，并提供它们的实际代码。这种实现是通过在类的声明中使用"implements"关键字后跟接口名来完成的。

### 多态

在面向对象编程中，类的多态性（Polymorphism）是指通过父类引用变量来引用子类对象，从而实现对不同子类对象的统一操作。多态性允许以一种通用的方式处理对象，而不必关心对象的具体类型。

多态性的核心思想是基于继承和方法重写。当一个类继承自另一个类（父类），它可以重写（覆盖）父类中的方法，并提供自己的实现。

```java
class Animal {
    public void makeSound() {
        System.out.println("Animal makes sound");
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Dog barks");
    }
}

class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Cat meows");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal animal1 = new Dog();
        Animal animal2 = new Cat();
        
        animal1.makeSound();  // 调用子类的方法，输出 "Dog barks"
        animal2.makeSound();  // 调用子类的方法，输出 "Cat meows"
    }
}

```

在上面的示例中，有一个父类"Animal"和两个子类"Dog"和"Cat"。父类中定义了一个"makeSound()"方法，而子类分别重写了该方法并提供了自己的实现。

在"Main"类的"main"方法中，通过父类"Animal"的引用变量分别引用了一个"Dog"对象和一个"Cat"对象。然后，通过这些父类引用变量调用"makeSound()"方法时，实际执行的是子类的实现。这就是多态性的体现。

**好处：**

父类引用变量可以根据实际引用的子类对象来调用适当的方法。这样可以在不修改父类引用变量的情况下，通过改变所引用的子类对象来实现不同的行为。多态性提高了代码的灵活性和可扩展性，使得代码更具可维护性和可复用性。

### **如何在springboot下开启JDK动态代理**

在pom.xml下配置

```xml
<dependency>  <!--实现JDK动态代理切面包-->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
<dependency>  <!--实现JDK动态代理切面包-->
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.7</version>
</dependency>
<dependency>   <!--实现JDK动态代理切面包-->
    <groupId>aopalliance</groupId>
    <artifactId>aopalliance</artifactId>
    <version>1.0</version>
</dependency>
```

在application下配置

```properties
#false开启JDK动态代理
#true开启CGLIB动态代理
spring.aop.proxy-target-class=false  
```

（JDK动态代理）结果：

![image-20230527195716965](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230527195716965.png)

（CGLIB动态代理）结果：

![image-20230527195812608](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230527195812608.png)

### 反射

#### 获取反射对象的三种方式：

```java
     // 获取反射对象的三种方式
     // 类名.clss
     Class stuclass1 = Student.class;
     // 对象.getClass();
     Student student = new Student();
     Class stuclass2 = student.getClass();
     // Class.forName("类全名")
     try {
         Class stuclass3 = Class.forName("com.etc.reflect.Student");
     } catch (ClassNotFoundException e) {
         throw new RuntimeException(e);
     }
```

**添加Declared和不添加Declared的区别：**

添加Declared的可以访问包括公共、保护、默认（包级别）和私有

不添加Declared只能访问,带public的


#### 获取构造参数：

```java
/*
        studentclass.getConstructor ();  //获取某一种构造方法
        studentclass.getConstructors ( );  //获取所有的构造方法
        studentclass.getDeclaredConstructor (  );//Declared获取能够访问到的所有的  //没有Declared只能访问public*/

        //取无参构造
        Constructor c1 = studentclass.getDeclaredConstructor ( );  //添加异常到方法签名
        System.out.println (c1 );

        //取有参构造
        Constructor c2 = studentclass.getDeclaredConstructor (new Class[] {String.class,int.class});  //根据类型添加反射参数
        System.out.println (c2 );

//构造方法初始化属性
        Student s1 = new Student ( "tom", 1 );

```

#### 反射对象实例化

```java
//反射对象实例化
Student stu3 = (Student) c2.newInstance ( new Object[]{"tom", 1} );
System.out.println (c2.getClass () );
System.out.println (stu3.getSno ()+" "+stu3.getName() );
```

#### 获取属性

```java
private String name;
private int sno;
public String aaa;
```

```java
		//获取属性
        //studentclass.getDeclaredFields ()//获取所有属性
        //studentclass.getFields()  //获取不到private属性

        //用输出流依次打印获取的属性
        Stream.of ( studentclass.getDeclaredFields ()).forEach ( System.out::println );
        System.out.println ("_________________" );
        Stream.of ( studentclass.getFields ()).forEach ( System.out::println );
        System.out.println ("---------------------" );
        //获取单个属性,并修改
        Field field = studentclass.getDeclaredField ( "name" );

        //开启私有属性访问
        field.setAccessible ( true );
        field.set ( stu3,"jerry" );
        System.out.println (stu3.getSno ()+" "+stu3.getName() );
```

![image-20230529102723798](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529102723798.png)

#### 获取方法

```java
//反射获取方法
//getDeclaredMethods ( )获取所有可以访问的方法
//getMethods ( )获取所有方法
Stream.of ( studentclass.getDeclaredMethods ( ) ).forEach ( System.out::println );

System.out.println ("-----------------------" );
//获取单个方法
Method method = studentclass.getDeclaredMethod ( "study", new Class[]{String.class});//参数：方法名，方法所需参数

//执行方法  相当于stu3.study("编程")
method.invoke ( stu3,"编程" );  //"编程"=new Object[]("编程")
```

![image-20230529103119430](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529103119430.png)

![image-20230529104949460](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529104949460.png)

**通过反射的方法获取方法上的注解**

```java
//method.getDeclaredAnnotations()获取这个方法上所有注解
        Stream.of(method.getDeclaredAnnotations()).forEach(item -> {
//            System.out.println(item);
            if (item instanceof Logeee ) {  //instanceof是哪个类注解判断
                System.out.println(((Logeee) item).value());  //打印注解里的内容
            }
        });
```

自定义注解加在方法上

![image-20230529110803131](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529110803131.png)

结果

![image-20230529110906570](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529110906570.png)

### 泛型

让类型泛化，就是，泛型后的类型，它可以让不是自身类型的来接收，省去了类型强转

使用泛型：

```java
//取有参构造
Constructor<Student> c2 = studentclass.getDeclaredConstructor (new Class[] {String.class,int.class});  //根据类型添加反射参数

 //反射对象实例化
Student stu3 = c2.newInstance ( new Object[]{"tom", 1} );
System.out.println (c2.getClass () );
System.out.println (stu3.getSno ()+" "+stu3.getName() );
```

![image-20230529100356929](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529100356929.png)

没使用泛型：

```java
//取有参构造
Constructor c2 = studentclass.getDeclaredConstructor (new Class[] {String.class,int.class});  //根据类型添加反射参数

 //反射对象实例化
Student stu3 = (Student) c2.newInstance ( new Object[]{"tom", 1} );
System.out.println (c2.getClass () );
System.out.println (stu3.getSno ()+" "+stu3.getName() );
```

![image-20230529100529175](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529100529175.png)