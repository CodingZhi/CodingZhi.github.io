# SPringMVC

[代码详情](https://gitee.com/floatingdream1001/isses-pt-repo/tree/master/springbootmvc)

什么是MVC？
MVC 是 Model、View 和 Controller 的缩写，分别代表 Web 应用程序中的 3 种职责，MVC是一种软件设计规范。它将业务逻辑、数据、显示分离的方法来组织代码，降低了视图与业务逻辑之间的双向耦合。

Model(模型)：数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据Dao） 和 服务层（行为Service）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。
View(视图)：负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。
Controller(控制器)：接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。也就是说控制器做了个调度员的工作。
最典型的MVC模式就是JSP+Servlet+JavaBean的模式。

## 什么是MVC？

MVC 是 Model、View 和 Controller 的缩写，分别代表 Web 应用程序中的 3 种职责，MVC是一种软件设计规范。它将业务逻辑、数据、显示分离的方法来组织代码，降低了视图与业务逻辑之间的双向耦合。

- **Model(模型)**：数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据Dao） 和 服务层（行为Service）。也就是**Model提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。**
- **View(视图)**：负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。
- **Controller(控制器)**：接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。也就是说控制器做了个调度员的工作。

最典型的MVC模式就是JSP+Servlet+JavaBean的模式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200901164438483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI0NjIxNQ==,size_16,color_FFFFFF,t_70#pic_center)

## 什么是SpringMVC？

SpringMVC是一个基于MVC模式的Web框架，是Spring框架的一个模块 。
它以SpringIOC容器为基础，并利用容器的特性来简化它的配置，所以 SpringMVC 和 Spring 可直接整合使用。
SpringMVC 对 MVC 流程进行了封装，屏蔽掉很多底层代码，让开发者可以更加轻松快捷的完成基于 MVC 模式的 Web 开发。
总的来说：Spring MVC是Spring Framework的一部分，是基于Java实现MVC的轻量级Web框架。

### SpringMVC的特点：

轻量级，简单易学
高效 , 基于请求响应的MVC框架
与Spring兼容性好，无缝结合
约定优于配置
功能强大：RESTful、数据验证、格式化、本地化、主题等
简洁灵活

### SpringMVC中的核心组件

（1）前端控制器：DispactherServlet
（2）处理器映射器：HandlerMapping
（3）处理器适配器：HandlerAdapter
（4）处理器：Handler，
（5）视图解析器：ViewResolver
（6）视图：View

### 组件的介绍

前端控制器：接收请求、响应结果，相当于转发器，它是SpringMVC框架最核心的组件，有了它就能减少其他组件之间的耦合度。（不需要程序员开发）
处理器映射器：根据配置的映射规则（根据请求的URL），找到对应的处理器。（不需要程序员开发）
处理器适配器：适配调用具体的处理器，并且执行处理器中处理请求的方法，执行完毕之后返回一个ModelAndView对象。
处理器：（需要程序员手动开发）。
视图解析器：会根据传递过来的ModelAndView对象进行视图解析，根据视图解析名解析称真正的视图View。（不需要程序员开发）
视图：View是一个接口，它的实现类支持不同类型的视图。比如：JSP、freemarker、Thymeleaf等等。

### SpringMVC的执行流程

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200902094756141.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI0NjIxNQ==,size_16,color_FFFFFF,t_70#pic_center)

（1）当用户通过浏览器发起一个HTTP请求，请求直接到前端控制器DispatcherServlet；
（2）前端控制器接收到请求以后调用处理器映射器HandlerMapping，处理器映射器根据请求的URL找到具体的Handler(处理者)，并将它返回给前端控制器；
（3）前端控制器调用处理器适配器HandlerAdapter去适配调用Handler；
（4）处理器适配器会根据Handler去调用真正的处理器去处理请求，并且处理对应的业务逻辑；
（5）当处理器处理完业务之后，会返回一个ModelAndView对象给处理器适配器，HandlerAdapter再将该对象返回给前端控制器；这里的Model是返回的数据对象，View是逻辑上的View。
（6）前端控制器DispatcherServlet将返回的ModelAndView对象传给视图解析器ViewResolver进行解析，解析完成之后就会返回一个具体的视图View给前端控制器。（ViewResolver根据逻辑的View查找具体的View）
（7）前端控制器DispatcherServlet将具体的视图进行渲染，渲染完成之后响应给用户（浏览器显示）。
例子

#### 流程：

若请求用axios只需要前7步

1. 请求过来， /hello
2. DispatcherServlet:  核心控制器
3. 根据请求获取HandlerMapping(RequestMappingHandlerMapping)  ， 获取到handler
4. 执行一套拦截器链chian  才能获取到handler
5. 通过HandlerAdpater(RequestMappingHandlerAdpater)执行。
6. 执行之前会入参处理，类型转换处理，国际化
7. handerl执行（hello），获取返回结果:ModelAndView
   - ModeAndView:  数据模型 model 
   - @ResponseBody直接把model序列化成json 封装进response返回给客户端
8. 找到合适的视图解析器list:  
9. 视图解析器要渲染 数据model和 view合成页面
10. 返回给客户端。

**Controller层**

```java
@Controller
public class HelloController {

    /**
     *  @RequestMapping("/hello")  //匹配请求
     *  @ResponseBody  使用该注解可以方便地将对象转换为响应的内容类型（如 JSON、XML 等），并将其写入响应体中
     */
    @RequestMapping("/hello")  //匹配请求
    @ResponseBody  //用JSON格式接收
    //这就相当于一个Handler
    public HashMap hello(String name) {

        HashMap map = new HashMap();
        map.put ( "code", 20000 );
        map.put ( "data", name );
        map.put ( "message", "hello world" );
        return map;
    }
}
```

**结果：**

![image-20230529192132305](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529192132305.png)

##  RestFul风格：给ajax,传送json返回json

/student  get/post/put

- get请求：   查询

- post请求：  新增

- put请求： 修改

- delete请求：  删除

- header(只发头数据)/option(只发验证数据)/...

- 返回结果：code,data,message

- 路径参数@PathVariable用于将 URL 中的参数绑定到方法的参数上

  GET与Post请求

  ```java
  @GetMapping("/hello")  //只匹配get请求   ==@RequestMapping(value = "/hello",method = RequestMethod.GET)
  @ResponseBody  //用JSON格式接收
  //这就相当于一个Handler
  public HashMap hello(String name) {
      HashMap map = new HashMap();
      map.put ( "code", 20000 );
      map.put ( "data", name );
      map.put ( "message", "hello world" );
      return map;
  }
  
  @PostMapping("/hello")  //接收post配请求
  @ResponseBody  //用JSON格式接收
  //这就相当于一个Handler
  public HashMap hello(@RequestBody HashMap param) {
      HashMap map = new HashMap();
      map.put ( "code", 20000 );
      map.put ( "data", param.get ( "name") );
      map.put ( "message", "hello world" );
      return map;
  }
  ```

![image-20230529201915484](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529201915484.png)

![image-20230529201942397](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529201942397.png)

![image-20230529202022738](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230529202022738.png)

## SpringMVC注解：

@RequestMapping();  万能匹配请求

@GetMapping(); 匹配get请求

@PostMapping();  匹配post请求

@PutMapping ：  匹配put请求

@DeleteMapping：  匹配delete请求

@Controller:   springMVC控制器

@RestController:  == @Contrller + @ResponseBody

@ResponseBody: 返回值对象 序列化成json

@RequestBody:  参数 把请求中的json数据 序列化成java对象

@Data ： 生成get,set,tostring，无参构造

```java
@Data  //生成get,set,tostring，无参构造
public class Data0 {
    private String name;
    private int age;

}
```

@RequestParam(“参数名”)：  指定参数名

```java
public HashMap hello(@RequestParam("name") String p_name) {

    HashMap map = new HashMap();
    map.put ( "code", 20000 );
    map.put ( "data", p_name );
    map.put ( "message", "hello world" );
    return map;
}
```

@RequestPart: 文件上传 ：必须是post请求，必须文件二进制流

@PathVariable:  请求路径参数(为了收录)

> 参数自动匹配数据模型。
>
> ```java
> @PutMapping("/hello")  //匹配请求  修改
> @ResponseBody  //用JSON格式接收
> //这就相当于一个Handler
> public HashMap hello(@RequestBody Data0 param) {
> 
>     HashMap map = new HashMap();
>     map.put ( "code", 20000 );
>     map.put ( "data", param );
>     map.put ( "message", "hello world" );
>     return map;
> }
> ```
>
> HttpServletRequest:  HttpSession: HttpServlertResponse:可以之间入参需要放后面



## 拦截器

chian结构; servlet/javaweb/jee中的过滤器。

chian结构:拦截器，获取handler要执行拦截器链

![image-20230530112258357](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530112258357.png)

拦截器要实现HandlerInterceptor接口：

```java
public class MyInterceptor implements HandlerInterceptor {
    //preHandle前置\postHandle正在执行\afterCompletion后置

    @Override
    public boolean preHandle ( HttpServletRequest request, HttpServletResponse response, Object handler ) throws Exception {
        System.out.println ("------------前置拦截器--------------" );
        return true;
    }

    @Override
    public void postHandle ( HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView ) throws Exception {
        System.out.println ("------------运行拦截器--------------" );
        HandlerInterceptor.super.postHandle ( request, response, handler, modelAndView );
    }

    @Override
    public void afterCompletion ( HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex ) throws Exception {
        System.out.println ("------------后置拦截器--------------" );
    }
}
```

![image-20230530145416013](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530145416013.png)

## 统一异常处理：

@ControllerAdvice()标记异常统一处理

@ExceptionHandler({ServiceException.class})//以什么类型触发异常，出现ServiceException下的类的异常，运行方法

![image-20230530112104782](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530112104782.png)

**ServiceException下**

```java
//common异常处理包
//ServiceException自定义异常
public class ServiceException  extends Exception {
    //重写所有的构造方法，去继承
    public ServiceException ( ) {
    }

    public ServiceException ( String message ) {
        super ( message );
    }

    public ServiceException ( String message, Throwable cause ) {
        super ( message, cause );
    }

    public ServiceException ( Throwable cause ) {
        super ( cause );
    }

    public ServiceException ( String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace ) {
        super ( message, cause, enableSuppression, writableStackTrace );
    }
}
```
 **ControllrExceptionHandler下**

- @ControllerAdvice:  修饰统一异常处理类
- @ExceptiionHandler({要处理异常类反射})；  修饰异常处理方法

```java
@ControllerAdvice()//指定哪个包下的异常
//统一的异常处理
public class ControllrExceptionHandler {

    @ExceptionHandler({ServiceException.class})
    @ResponseBody
    public R serviceException(Exception e){

        return R.error ( e.getMessage ( ) );

    }
}
```

**异常数据模型  R**

```java
//异常数据模型
@Data
public class R {
    String code;
    Object data;
    String message;

    //静态的方法切换
    public static R error(String message){
        R r=new R();
        r.code = "200001";
        r.message=message;
        return r;
    }
}
```

**控制器中加异常判断**

```java
    //@DeleteMapping ("{id}/hello")
    @DeleteMapping ("{id}/hello")  //匹配请求  修改
    @ResponseBody  //用JSON格式接收
    //这就相当于一个Handler
    public HashMap hello( @PathVariable("id") int id, HttpServletRequest request) throws ServiceException {

        HashMap map = new HashMap();
        map.put ( "code", 20000 );
        map.put ( "data", id );
        map.put ( "message", "hello world" );

        if (id<100){  //若ID<100触发ServiceException异常
            throw new ServiceException ( "id不能小于100" );
        }
//        System.out.println ("####################" );
//        System.out.println ( request.getRealPath ( "/" ) );
        return map;
    }
```

## Hibernate-Validator验证框架

pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

它只能在有数据模型上实现

数据模型

- @Validated：  修饰参数模型，激活验证注解
- BindingResult ：  持有验证结果，所有error存在对象中
- 自定义了一个异常： [ServiceException](#统一异常处理：)

```java
@Data  //生成get,set,tostring，无参构造
public class Data0 {
    //数据验证
    @Pattern ( regexp = "^[a-zA-Z]\\w{5,16}$",message="name必须输入6-16位字母数字下划线,首位是字母")  //里面输入正则表达式\w[0-9a-zA-Z_]
    @NotBlank(message = "name必须输入")//为null或者空串都不行
    //@Length(min=6,max=16,message="name必须输入6-16位")//限制
    private String name;
    private int age;

}
```

Controller层

```java
@PutMapping("/hello")  //匹配请求  修改
@ResponseBody  //用JSON格式接收
//数据模型
public HashMap hello( @RequestBody @Valid Data0 param, BindingResult result) throws ServiceException {//参数必须是数据模型，BindingResult所有的错误信息都会获取
//需要有一个异常处理类，ServiceException（自己设置）
    if(result.hasErrors()){
        for(int i = 0; i < result.getFieldErrors (  ).size ();i++){ //用循环的方式得到item错误
            FieldError item=result.getFieldErrors ().get (i);
            throw new ServiceException ( item.getDefaultMessage () ); //将异常message传给ServiceException
        }
    }

    HashMap map = new HashMap();
    map.put ( "code", 20000 );
    map.put ( "data", param );
    map.put ( "message", "hello world" );
    return map;
}
```

![image-20230530145208916](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530145208916.png)

![image-20230530145318093](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530145318093.png)

![image-20230530145342499](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530145342499.png)

[Hibernate validator 常用的注解汇总](https://blog.csdn.net/mynamepg/article/details/106236021)

### 分组验证

组是A、B，但前提是一个接口

```jav
public interface GroupA {
}
```
接着把刚刚的组加到请求controller中

```java
    @PutMapping("/hello")  //匹配请求  修改
    @ResponseBody  //用JSON格式接收
    //数据模型
    public HashMap hello( @RequestBody @Validated({GroupB.class}) Data0 param, BindingResult result) throws ServiceException {//参数必须是数据模型，BindingResult所有的错误信息都会获取

        if(result.hasErrors()){
            for(int i = 0; i < result.getFieldErrors (  ).size ();i++){
                FieldError item=result.getFieldErrors ().get (i);
                throw new ServiceException ( item.getDefaultMessage () );
            }
        }

        HashMap map = new HashMap();
        map.put ( "code", 20000 );
        map.put ( "data", param );
        map.put ( "message", "hello world" );
        return map;
    }

    @PutMapping("/hello1")  //匹配请求  修改
    @ResponseBody  //用JSON格式接收
    //数据模型
    public HashMap hello1( @RequestBody @Validated({GroupA.class}) Data0 param, BindingResult result) throws ServiceException {//参数必须是数据模型，BindingResult所有的错误信息都会获取

        if(result.hasErrors()){
            for(int i = 0; i < result.getFieldErrors (  ).size ();i++){
                FieldError item=result.getFieldErrors ().get (i);
                throw new ServiceException ( item.getDefaultMessage () );
            }
        }

        HashMap map = new HashMap();
        map.put ( "code", 20000 );
        map.put ( "data", param );
        map.put ( "message", "hello world" );
        return map;
    }
```

A与B组对数据模型处理

```java
@Data  //生成get,set,tostring，无参构造
public class Data0 {
    //数据验证
    @Pattern ( regexp = "^[a-zA-Z]\\w{5,16}$",message="name必须输入6-16位字母数字下划线,首位是字母",groups = {GroupA.class})  //里面输入正则表达式\w[0-9a-zA-Z_]       Class<?>[] groups() default {};
    @NotBlank(message = "name必须输入",groups = {GroupA.class, GroupB.class})
//    @Length(min=6,max=16,message="name必须输入6-16位")
    private String name;
    private int age;

}
```

此时两个请求的结果就不同

A组/hello1![image-20230530153230219](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530153230219.png)

B组/hello

![image-20230530153311945](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530153311945.png)

因为分组，所以一个会触发@Pattern一个则不会。

### 结合redis

```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.61</version>
        </dependency>

        <!-- Spring Data Redis 默认使用 Jackson 作为 JSON 序列化的工具 -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
        <!-- 实现对 Spring Data Redis 的自动化配置 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <exclusions>
                <!-- 去掉对 Lettuce 的依赖，因为 Spring Boot 优先使用 Lettuce 作为 Redis 客户端 -->
                <exclusion>
                    <groupId>io.lettuce</groupId>
                    <artifactId>lettuce-core</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- 引入 Jedis 的依赖，这样 Spring Boot 实现对 Jedis 的自动化配置 -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
        </dependency>
```

yml里面配置，连接自己在Linux上的系统

```yml
redis:
  host: 192.168.80.
  port: 6379
  # password: Redis 服务器密码，默认为空。生产中，一定要设置 Redis 密码！
  database: 0 # Redis 数据库号，默认为 0 。
  timeout: 0 # Redis 连接超时时间，单位：毫秒。
  # 对应 RedisProperties.Jedis 内部类
  jedis:
    pool:
      max-active: 8 # 连接池最大连接数，默认为 8 。使用负数表示没有限制。
      max-idle: 8 # 默认连接数最小空闲的连接数，默认为 8 。使用负数表示没有限制。
      min-idle: 0 # 默认连接池最小空闲的连接数，默认为 0 。允许设置 0 和 正数。
      max-wait: -1 # 连接池最大阻塞等待时间，单位：毫秒。默认为 -1 ，表示不限制。
```

根据前面的在cityControll里做一个测试

```java
@Autowired
RedisTemplate redisTemplate;  //根据RedisTemplate一个通用类，声明了一个名为 redisTemplate 的变量

public static final String KEY01 = "key01";  //设Key值

@GetMapping("/getAllCityR")
public R getCityByName3(){

    List< Citypojie > clist = null;
    if ( redisTemplate.hasKey ( KEY01 ) ){  //判断有没有KEY01redisTemplate.hasKey ( KEY01 )
        clist = (List< Citypojie >)redisTemplate.opsForValue ( ).get ( KEY01 );  //如果有，就从redis里取
    }else {
        clist=mapper.findAllData ();  //否则从数据库里取
        redisTemplate.opsForValue ().set ( KEY01,clist );  //把查到的值，保存到key
    }


    return R.success ( clist );
}
```

![image-20230601205310103](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230601205310103.png)



## 补充

### 热部署教程

后端修改，不用经常重启循行

[idea2020配置springboot热部署](https://blog.csdn.net/YXWik/article/details/109119198)

