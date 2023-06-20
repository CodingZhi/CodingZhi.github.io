# ruoyi-cloud启动

[代码地址](https://gitee.com/floatingdream1001/isses-pt-repo/tree/master/RuoYi-Cloud-master)

先搭建mysql、redis和nacos

接着mysql创建数据库ry-cloud,接着按照下面顺序依次导入sql文件(**如果启动nacos先导入ry-config后直接docker启动nacos接着在导入其他**)

![image-20230602104855257](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230602104855257.png)

接着将所有的booststrap.yml 127.0.0.1换成自己虚拟机IP

![image-20230602105018974](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230602105018974.png)

然后按Alt+8 点击+springboot项目，接着全部启动。



## 转发

### 直接链接转发

test1是主要请求，转发给test2

**test1**

controller层

```java
@RestController
public class StoreController {

    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/client1/getobj")
    public Object client12(){
        String url = "http://test2/getobj";
        Person p = restTemplate.getForObject ( url, Person.class );
        return p;
    }

    @GetMapping("/client1/getobj1")
    public Object client13(){
        String url = "http://test2/getobj1";
        ResponseEntity<Person> responseEntity = restTemplate.getForEntity(url, Person.class);
        Person body = responseEntity.getBody();
        System.out.println("body:" + body);
        HttpStatus statusCode = responseEntity.getStatusCode();
        System.out.println("HttpStatus:" + statusCode);
        int statusCodeValue = responseEntity.getStatusCodeValue();
        System.out.println("statusCodeValue:" + statusCodeValue);
        System.out.println("----------------header------------------");
        HttpHeaders headers = responseEntity.getHeaders();
        Set<Map.Entry<String, List<String>>> entries = headers.entrySet();
        for (Map.Entry<String, List<String>> entry:entries) {
            System.out.println(entry.getKey());
            System.out.println(entry.getValue());
        }
        return body;
    }
}
```

数据模型

```java
@Data
public class Person {
    private Integer id;
    private String name;

    public Person ( Integer id, String name ) {
        this.id   = id;
        this.name = name;
    }

    public Person ( ) {
    }
}
```

yml配置

```yml
# Tomcat
server:
  port: 20000

# Spring
spring: 
  application:
    # 应用名称
    name: test1
  mvc:
      pathmatch:
        matching-strategy: ant_path_matcher
  profiles:
    # 环境配置
    active: dev  #必须用config
  cloud:
    nacos:
      discovery:
        # 服务注册地址
        server-addr: 192.168.80.228:8848
        namespace: myconf
        group: myconf
      config:
        # 配置中心地址
        server-addr: 192.168.80.228:8848
        # 配置文件格式
        file-extension: yml
        # 共享配置
        shared-configs:
          - application-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}
        namespace: myconf  #命名空间
        group: myconf    #组
```

**tets2**

controller

```java
@RestController
public class Service2StoreController {
    @GetMapping("/getobj")
    public Person Client12(){
        Person p = new Person (100,"coding" );
        return p;

    }

    @GetMapping("/getobj1")
    public Person Client13(){
        Person p = new Person (100,"coding" );
        return p;

    }
}
```

**结果**

实现方法转发请求

![image-20230606103035105](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606103035105.png)

## 负载

此时创建test21和test2,里面所有配置和方法一样，需要注意开启端口，和一个方法反馈的区别，便于区别不同工程，注意的是，这里的组一样，也是靠nacos根据组名相同轮询进入不同方法。

## Sentinel

### 熔断

#### 继承

引入jar包

```xml
<!--sentinel启动器-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
<!--Springboot-Actuator-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果负载的某一结点出现问题，需要一个异常处理

在之前@FeignClient配置参数，错误处理，转向Hello1ServiceFeignCallback类

```java
//标记你调的微服务下的请求
@FeignClient(value = "test2",fallback = Hello1ServiceFeignCallback.class)  //微服务地址
public interface Hello1ServiceFeign {
    @GetMapping("echo/{string}")
    public String hell1(@PathVariable(value = "string") String string);
}
```

将类容器化，并简单返回一个错误

```java
@Component
public class Hello1ServiceFeignCallback implements Hello1ServiceFeign{
    @Override
    public String hell1 ( String string ) {
        return "有问题";
    }
}
```

开启sentinel配置

```yml
feign:
  sentinel:
    enabled: true
```

在test2上面加一个假错误

```java
@RestController
public class EchoController {
//    @RequestMapping ( value = "/echo/{string}", method = RequestMethod.GET)
    @GetMapping("/echo/{string}")
    public String echo(@PathVariable String string) throws Exception {
        if ( 1==1 ){
            throw new Exception ( "service:test1异常了" );
        }

        return "这是服务test2提供数据： " + string;
    }
}
```

![image-20230606093341208](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606093341208.png)、

### 控制台

启用sentinel-dashboard 在目录cmd中

```shell
java -Dserver.port=8718 -Dcsp.sentinel.dashboard.server=localhost:8718 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```

需要的工程下yml引用

```yml
sentinel:
  # 取消控制台懒加载
  eager: true
  transport:
    # 控制台地址
    dashboard: 127.0.0.1:8718
  # nacos配置持久化
  datasource:
    ds1:
      nacos:
        server-addr: 192.168.80.228:8848
        dataId: test1
        groupId: myconf
        data-type: json
        rule-type: gw-flow
        
feign:
  sentinel:
    enabled: true
```

所有的jar包最好直接在父中引用，方便

```xml
<!-- Sentinel Datasource Nacos -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

### 流控规则

资源名是请求名

![image-20230606112208236](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606112208236.png)

限制成功

![image-20230606140009875](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606140009875.png)

## 压力测试

点击jmeter.bat

![image-20230620110521664](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230620110521664.png)

![image-20230606111149578](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606111149578.png)

![image-20230606111402006](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606111402006.png)![image-20230606111404973](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606111404973.png)

**结果视图添加**

![image-20230606112737195](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230606112737195.png)

## gateway

作为后端转发路由，但组只能在ruoyi下面，不能是自己项目下面，pom配置参考ruoyi-gateway

在nacos下配置test3-gateway-dev.yml

```yml
spring:
  cloud:
    gateway:
      discovery:
        locator:
        #允许大小写
          lowerCaseServiceId: true
          enabled: true
      routes:
        # 认证中心
        - id: test1
          uri: lb://teat1 #对应的就是组的名字
          predicates:
            - Path=/test1/**
          filters:
            - StripPrefix=1 #切一层目录
        # 代码生成
        - id: test2  #名字可以随便起
          uri: lb://test2
          predicates:
            - Path=/test2/**
          filters:
            - StripPrefix=1
```

> 此时`http://localhost:20003/test1/hell1/hellocloud`就会被getway转成`http://localhost:20000/hell1/hellocloud`可以添加多组来通过配置负载转发

**本次ruoyi-cloud采用先用nginx代理`http://localhost:80/prod-api`代理`http://localhost:8080`接着用gateway代理**

**例如：**

http://localhost:81/dev-api/system/user/list?pageNum=1&pageSize=10

**先改为**

**nginx**下：

http://localhost:80/prod-api/

**gateway**下

`system/`相当于通过gateway代理微服务ruoyi-system

接着在寻求工程ruoyi-system下controller层，`@RequestMapping("/user")`在拼接上`@GetMapping("/list")`
