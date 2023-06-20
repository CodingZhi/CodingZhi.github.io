# Mybatis

[代码参考](https://gitee.com/floatingdream1001/isses-pt-repo/tree/master/springbootmvc)

MyBatis 是一款优秀的持久层(ORM)框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

## 配置

```xml
<dependency>
     <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
           <version>2.1.4</version>
        </dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.31</version> <!--选择自己mysql版本-->
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.8</version>
</dependency>
```

## Mybatis获取数据库数据

数据库，以及Mybatis配置

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/sakila?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai&useSSL=FALSE
    username: root
    password: wuzihan2@
    type: com.alibaba.druid.pool.DruidDataSource
    
mybatis:
  configuration:
    map-underscore-to-camel-case: true  #开启驼峰
    cache-enabled: true  #用于开启二级缓存
  mapper-locations: classpath:/mapper/*Mapper.xml  #配置本地方法对应的sql语句
```

**目录：**

![image-20230530192901051](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530192901051.png)

### 第一种在接口的方法上直接加sql语句查询

**Citypojie下**

根据本地数据库sakila.city里数据创建

![image-20230530193044107](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530193044107.png)

```java
@Data
public class Citypojie {
    private Integer cityId;
    private String city;
    private Integer countryId;
    @JsonFormat ( pattern = "YYYY-MM-dd HH:mm:ss")//传回来的JSON值格式化时间返回值
    private LocalDateTime lastUpdate;

}
```

**CityMapper接口**

```java
@Mapper
public interface CityMapper {

    //sql语句注解
    @Select ( "" + "SELECT c.city_id,\n" +     //第一种写法
              "       c.city,\n" +
              "       c.country_id,\n" +
              "       c.last_update   \n" +
              "FROM city c;" )
    List< Citypojie > findAllData ( );  //返回结果是集合，用Citypojie去接

}
```

**R 异常数据**

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
        r.code = "20001";
        r.message=message;
        return r;
    }

    public static R success( List< Citypojie > clist){
        R r=new R();
        r.code = "20000";
        r.message="Success";
        r.data = clist;
        return r;
    }
}
```

**CityController下**

```java
@RestController
public class CityController {

    @Autowired
    CityMapper mapper;  //接口代理

    @GetMapping("/getAllCity")
    public R getAllCity(){

        List< Citypojie > clist = mapper.findAllData ( );
        return R.success ( clist );
    }
}
```

**结果：访问到数据库的信息**

![image-20230530193349657](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530193349657.png)

### 第二种，根据mybatis官方建立一个mapper目录

#### ResultType

和上面yml配置时目录设立有关

![image-20230530204857628](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530204857628.png)

![image-20230530204801623](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530204801623.png)

cityMapper.xml下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.codingzhi.springbootmvc.dao.CityMapper">  <!--和查询接口对应-->         <!--resultType,返回类型找接收的数据模型-->
    <select id="findByCity" parameterType="String" resultType="com.codingzhi.springbootmvc.pojo.Citypojie">  <!--基本类型和单参类型可以不写parameterType="String"-->
        SELECT c.city_id,
        c.city,
        c.country_id,
        c.last_update
        FROM city c
        WHERE c.city= #{city} 
    </select>
</mapper>
```

CityController下   

```java
@GetMapping("/getCityByName")
public R getCityByName(String city){

    List< Citypojie > clist = mapper.findByCity (city );
    return R.success ( clist );
}
```

**CityMapper下**   此时不需要@select注解

```java
//方法上没有注解，就会找该类配置上映射的id
    List< Citypojie > findByCity ( String city);  //要和mybaties的mapper里id保持一致
```

#### resultMap

在Citymapper接口下创建方法

```java
List< Citypojie > findByCity0 ( Citypojie pojo);
```

在cityMapper.xml下

```xml
<resultMap id="aaa" type="com.codingzhi.springbootmvc.pojo.Citypojie">
    <id column="city_id" property="cityId"></id>   <!--唯一约束  column对应查询列sql里的    property对应Citypojie属性-->
    <result column="city" property="city"></result>  <!--id和result对应查询出来的列-->
    <result column="country_id" property="countryId"></result>
    <result column="last_update" property="lastUpdate"></result>
    <result column="country" property="country"></result>
</resultMap>
<select id="findByCity0" parameterType="com.codingzhi.springbootmvc.pojo.Citypojie" resultMap="aaa" >  <!--基本类型和单参类型可以不写parameterType="String"-->
    SELECT c.city_id,
    c.city,
    c.country_id,
    c.last_update ,
    c1.country
    FROM city c INNER JOIN country c1 ON c.country_id = c1.country_id
    WHERE c.city like concat('%',#{city},'%')  <!--模糊查询   concat('%',#{city},'%')前面任意个字符后面任意个字符，中间必须有你输入的字符串-->
    ORDER BY c1.country,c.city_id
</select>
```



接着Citycontroller控制器配置请求

```java
GetMapping("/getCityLikeName")
@Transactional (readOnly = true)  //创建事物，已只读方式，建立一级缓存
public R getCityByName0(String citylike){
    Citypojie param = new Citypojie ( );
    param.setCity ( citylike );
    List< Citypojie > clist = mapper.findByCity0 (param);
    return R.success ( clist );

}
```

**结果**

![image-20230531103810905](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531103810905.png)

### 

## 添加缓存

先把日志的DEBUG模式开开

### 一级缓存（基于sqlseccession）

此时我们有两次对同一数据的查询

![image-20230530212731186](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530212731186.png)

接着看日志，可以看到，我的一次put请求，方法两次对同一数据查询，sql了两次

![image-20230530212859722](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530212859722.png)

但当我在方法上添加了

![image-20230530213555011](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530213555011.png)

![image-20230530213205192](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530213205192.png)

### 二级缓存(基于mapper)

在cityMapper.xml下

![image-20230530220942111](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530220942111.png)

接着在数据模型Citypojie下添加序列化

![image-20230530221058701](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530221058701.png)

**结果**

![image-20230530221342270](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530221342270.png)

## ResultMap映射

### 一对一映射

在Citymapper接口下创建方法

```java
List< Citypojie > findByCity1 ( Citypojie pojo);
```

在cityMapper.xml下

```xml
<resultMap id="bbb" type="com.codingzhi.springbootmvc.pojo.Citypojie">
    <id column="city_id" property="cityId"></id>   <!--唯一约束  column对应查询列sql里的    property对应Citypojie属性-->
    <result column="city" property="city"></result>  <!--id和result对应查询出来的列-->
    <result column="country_id" property="countryId"></result>
    <result column="last_update" property="lastUpdate"></result>
    <result column="country" property="country"></result>
    <association property="countrypojie" javaType="com.codingzhi.springbootmvc.pojo.Countrypojie">
        <id column="country_id" property="countryId"></id>   <!--唯一约束  column对应查询列sql里的    property对应Citypojie属性-->
        <result column="country" property="country"></result>  <!--id和result对应查询出来的列-->
    </association>
</resultMap>
<select id="findByCity1" parameterType="com.codingzhi.springbootmvc.pojo.Citypojie" resultMap="bbb" >  <!--基本类型和单参类型可以不写parameterType="String"-->
    SELECT c.city_id,
    c.city,
    c.country_id,
    c.last_update ,
    c1.country
    FROM city c INNER JOIN country c1 ON c.country_id = c1.country_id
    WHERE c.city like concat('%',#{city},'%')  <!--模糊查询   concat('%',#{city},'%')前面任意个字符后面任意个字符，中间必须有你输入的字符串-->
    ORDER BY c1.country,c.city_id
</select>
```

**被映射Countrypojie的**

```java
@Data
public class Countrypojie implements Serializable {
    private Integer countryId;
    private String country;
    @JsonFormat(pattern = "YYYY-MM-dd HH:mm:ss")
    private LocalDateTime updateDate;


}
```

**Citypojie的**

```java
@Data
public class Citypojie implements Serializable {
    private Integer cityId;
    private String city;
    private Integer countryId;
    @JsonFormat ( pattern = "YYYY-MM-dd HH:mm:ss")//传回来的JSON值格式化时间返回值
    private LocalDateTime lastUpdate;
    private String country;
    private Countrypojie countrypojie;


}
```

**接着Citycontroller控制器配置请求**

```java
@GetMapping("/getCityLikeName2")
@Transactional (readOnly = true)  //创建事物，已只读方式，建立一级缓存
public R getCityByName1(String citylike){
    Citypojie param = new Citypojie ( );
    param.setCity ( citylike );
    List< Citypojie > clist = mapper.findByCity1 (param);
    return R.success ( clist );

}
```

**结果：**

![image-20230531110430664](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531110430664.png)

### 一对多映射

根据数据库country与city库特性1对多

**在Citymapper接口下创建方法**

```java
List< Countrypojie > findByCity2 ( Citypojie pojo);
```

**在cityMapper.xml下**

```xml
<resultMap id="ccc" type="com.codingzhi.springbootmvc.pojo.Countrypojie">
    <id column="country_id" property="countryId"></id>   <!--唯一约束  column对应查询列sql里的    property对应Citypojie属性-->
    <result column="country" property="country"></result>  <!--id和result对应查询出来的列-->
    <collection property="clist" ofType="com.codingzhi.springbootmvc.pojo.Citypojie">
        <id column="city_id" property="cityId"></id>   <!--唯一约束  column对应查询列sql里的    property对应Citypojie属性-->
        <result column="city" property="city"></result>  <!--id和result对应查询出来的列-->
        <result column="country_id" property="countryId"></result>
        <result column="last_update" property="lastUpdate"></result>
        <result column="country" property="country"></result>
    </collection>
</resultMap>
<select id="findByCity2" parameterType="com.codingzhi.springbootmvc.pojo.Citypojie" resultMap="ccc" >  <!--基本类型和单参类型可以不写parameterType="String"-->
    SELECT c.city_id,
    c.city,
    c.country_id,
    c.last_update ,
    c1.country
    FROM city c INNER JOIN country c1 ON c.country_id = c1.country_id
    WHERE c.city like concat('%',#{city},'%')  <!--模糊查询   concat('%',#{city},'%')前面任意个字符后面任意个字符，中间必须有你输入的字符串-->
    ORDER BY c1.country,c.city_id
</select>
```

**R下新增**

```java
public static R success1( List< Countrypojie > clist){
    R r=new R();
    r.code = "20000";
    r.message="Success";
    r.data = clist;
    return r;
}
```

**接着Citycontroller控制器配置请求**

```java
@GetMapping("/getCityLikeName3")
@Transactional (readOnly = true)  //创建事物，已只读方式，建立一级缓存
public R getCityByName2(String citylike){
    Citypojie param = new Citypojie ( );
    param.setCity ( citylike );
    List< Countrypojie > clist = mapper.findByCity2 (param);
    return R.success1 ( clist );

}
```

**countryjie新增**

```java
private List<Citypojie> clist;  //一对多检索
```

**结果**

![image-20230531112158818](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531112158818.png)



## 向数据库添加信息

因为我们添加数据不知到此时信息表里id排多少，所以我们要在添加信息后返回此时信息表里新添加的数据和id

**数据库创建**

```sql
CREATE TABLE sakila.student_info (
  id int UNSIGNED NOT NULL AUTO_INCREMENT,
  name varchar(255) DEFAULT NULL,
  age int DEFAULT NULL,
  email varchar(50) DEFAULT '17',
  PRIMARY KEY (id)
)
ENGINE = INNODB,
CHARACTER SET utf8mb4,
COLLATE utf8mb4_0900_ai_ci;
```

**mapper.xml下**

```xml
<insert id="addStudentinfo" parameterType="com.codingzhi.springbootmvc.pojo.Studentinfopojie" >
    <!--keyProperty会把结果扔给Studentinfopojie下的id  ，resultType="java.lang.Integer"查询类型-->
    <selectKey resultType="java.lang.Integer" keyProperty="id" order="AFTER">  <!--selectKey查询变得id,order="AFTER"是并在INSERT之后执行-->
        select last_insert_id() <!--last_insert_id()查之后修改的id-->
    </selectKey>
    INSERT INTO student_info(name,email,age) VALUES(#{name},#{email},#{age})   <!--#{}会有一个数值转换-->
 <!--INSERT INTO student_info(name,email,age) VALUES(${name},${email},${age});-->
</insert>
```

**添加数据模型Studentinfopojie**

```java
@Data
public class Studentinfopojie {
    //Integer与int区别：Integer不仅有数字还有null
    private Integer id;
    private String name;
    private Integer age;
    private String email;
}
```

**添加接口类方法StudentinfoMapper**

```java
@Mapper
public interface StudentinfoMapper {

    int addStudentinfo ( Studentinfopojie pojo );  //int代表变更的数据
}
```

**新建测试类：**

```java
@SpringBootTest
public class Test2 {

    @Autowired
    StudentinfoMapper mapper;

    @Test
    public void test20() {

        Studentinfopojie param = new Studentinfopojie ();
        param.setName("Jerry");
        param.setAge(20);
        param.setEmail("email1");

        int r = mapper.addStudentinfo (param);
        System.out.println("变更条数" + r);
        System.out.println(param);

    }
}
```

**结果：**

没加Id变更查询时

![image-20230531131233497](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531131233497.png)

加了之后

![image-20230531131252841](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531131252841.png)

## 动态查询

根据输入的值来查，如果一条都没输入，就全部查询

```xml
<!--参数类型parameterType-->
<select id="findByCondition" parameterType="com.codingzhi.springbootmvc.pojo.Studentinfopojie" resultType="com.codingzhi.springbootmvc.pojo.Studentinfopojie">
    SELECT si.id,
    si.name,
    si.email,
    si.age
    FROM student_info si
    <where>
        <if test="name != null and name != ''">   <!--判断是否是空值还是null-->    <!--ojll表达式-->
            AND name = #{name}
        </if>
        <if test="email != null and email != ''">
            AND si.email = #{email}
        </if>
        <if test="age != null">
            AND si.age = #{age}
        </if>
    </where>
</select>
```

接着在dao下的studentinfoMapper

```java
List<Studentinfopojie> findByCondition ( Studentinfopojie pojo);
```

Test下测试

```java
    @Test
    public void test21() {
        Studentinfopojie param = new Studentinfopojie();
        param.setName("Jerry");
//        param.setAge(20);
//        param.setEmail("email1");
        List<Studentinfopojie> byCondition = mapper.findByCondition(param);
        byCondition.forEach(System.out::println);

    }
```

查询结果

![image-20230531142205268](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531142205268.png)

## Mapper生成工具

![image-20230531152921816](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531152921816.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN" "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--    <properties resource="config/application.properties"/>-->
    <!-- mysql驱动的位置 这个是MySQL连接的jar包，你需要指定你自己计算机上的jar包的位置-->
    <classPathEntry location="C:\Users\****\.m2\repository\com\mysql\mysql-connector-j\8.0.31\mysql-connector-j-8.0.31.jar" />  <!--根据自己的mysql包-->

    <context id="Tables" targetRuntime="MyBatis3">
        <!-- 是否生成注释 -->
        <commentGenerator>
            <!-- 是否生成注释代时间戳 -->
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!-- JDBC连接 其中connectionURL后面的newtest改为你创建的数据库，紧跟在后面是数据库连接的账户和密码-->
        <jdbcConnection
                driverClass="com.mysql.cj.jdbc.Driver"
                connectionURL="jdbc:mysql://localhost:3306/sakila?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai&amp;useSSL=FALSE"
                userId="root"  
                password="">
        </jdbcConnection>

        <!-- 非必需，类型处理器，在数据库类型和java类型之间的转换控制-->
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
         NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <!-- 是否使用bigDecimal， false可自动转化以下类型（Long, Integer, Short, etc.） -->
            <property name="forceBigDecimals" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaTypeResolver>

        <!-- 生成实体类model地址 这里需要你改动，其中targetPackage需要根据你自己创建的目录进行改动 -->
        <javaModelGenerator targetPackage="com.codingzhi.springbootmvc.pojo" targetProject="src/main/java">
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true" />
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaModelGenerator>
        <!-- model生成 -->


        <!-- 生成mapper xml文件 这里不需要改动 -->
        <sqlMapGenerator targetPackage="mapper"  targetProject="src/main/resources">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>

        <!-- 生成mapper xml对应Client   这里需要改动targetPackage，依据你自己的工程-->
        <javaClientGenerator targetPackage="com.codingzhi.springbootmvc.dao" targetProject="src/main/java" type="XMLMAPPER">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>

        <!-- 配置表信息 -->
        <!-- schema即为数据库名 tableName为对应的数据库表 domainObjectName是要生成的实体类 enable*ByExample
            是否生成 example类 -->
        <table schema="sakila" tableName="actor" domainObjectName="Actor" enableCountByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false">
            <generatedKey column="actor_id" sqlStatement="MySql" identity="true"></generatedKey>
        </table>
    </context>
</generatorConfiguration>
```

启动：

![image-20230531153321229](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531153321229.png)

![image-20230531153445524](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230531153445524.png)

> 记住给生成的mapper上加注解@Mapper

## 补充

### druid

在我们Java程序中有很大一部分要操作数据库，为了提高性能操作数据库的时候，又不得不使用**数据库连接池**。而Druid 就是阿里巴巴开源平台上一个数据库连接池实现，**结合了 C3P0、DBCP 等 DB 池的优点，同时加入了日志监控。**

#### 功能

> 监控数据库访问性能

**Druid内置提供了一个功能强大的StatFilter插件，能够详细统计SQL的执行性能，可以很好的监控 DB 池连接和 SQL 的执行情况**，天生就是针对监控而生的 DB 连接池。这对于线上分析数据库访问性能有帮助。

> 替换传统的DBCP和C3P0连接池中间件

Druid提供了一个高效、功能强大、可扩展性好的数据库连接池。

> 数据库密码加密

直接把数据库密码写在配置文件中，容易导致安全问题。DruidDruiver和DruidDataSource都支持PasswordCallback。

> SQL执行日志

Druid提供了不同的LogFilter，能够支持Common-Logging、Log4j和JdkLog，你可以按需要选择相应的LogFilter，监控你应用的数据库访问情况。

#### 配置

在pom.xml下引入包

```xml
<!--druid-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.6</version>
</dependency>
<dependency> <!--引日志-->
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

application.yml下

```yml
spring:
  datasource:
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver   #启动类
      url: jdbc:mysql://localhost:3306/sakila?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai&useSSL=FALSE
      username: root
      password: wuzihan2@
      type: com.alibaba.druid.pool.DruidDataSource   #只需要在原有基础上配置数据源
      filters: stat,wall,log4j
      test-on-borrow: false
      test-while-idle: false
      #配置监控统计拦截的filters，stat：监控统计、log4j：日志记录、wall：防御sql注入
      #如果允许报错，java.lang.ClassNotFoundException: org.apache.Log4j.Properity
      #则导入log4j 依赖就行
      initialSize: 5
      minIdle: 5
      maxActive: 20
      maxWait: 60000
      timeBetweenEvictionRunsMillis: 60000
      minEvictableIdleTimeMillis: 300000
      validationQuery: SELECT 1 FROM DUAL
      testWhileIdle: true
      testOnBorrow: false
      testOnReturn: false
      poolPreparedStatements: true

      #配置监控统计拦截的filters，stat：监控统计、log4j：日志记录、wall：防御sql注入
      #如果允许报错，java.lang.ClassNotFoundException: org.apache.Log4j.Properity
      #则导入log4j 依赖就行

      #！！！初始显示不了的位置！！！
      #filters: stat,wall,log4j
      maxPoolPreparedStatementPerConnectionSize: 20
      useGlobalDataSourceStat: true
      connectionoProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

#### 配置Druid数据源监控

```java
@Configuration
public class DruidConfig {

    //配置 Druid 监控管理后台的Servlet；
//内置 Servlet 容器时没有web.xml文件，所以使用 Spring Boot 的注册 Servlet 方式
    @Bean
    public ServletRegistrationBean statViewServlet() {    //ServletRegistrationBean注册器
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*"); ///druid/*地址请求
        // 这些参数可以在 com.alibaba.druid.support.http.StatViewServlet
        // 的父类 com.alibaba.druid.support.http.ResourceServlet 中找到
        Map<String, String> initParams = new HashMap<>();
        initParams.put("loginUsername", "root"); //后台管理界面的登录账号
        initParams.put("loginPassword", "wuzihan2@"); //后台管理界面的登录密码
        //后台允许谁可以访问
        //initParams.put("allow", "localhost")：表示只有本机可以访问
//        initParams.put("allow", "")：为空或者为null时，表示允许所有访问
        initParams.put("allow", "");
        //deny：Druid 后台拒绝谁访问
        //initParams.put("test", "服务器地址");表示禁止此ip访问
        //设置初始化参数
        bean.setInitParameters(initParams);
        return bean;
    }
    //2、配置一个web监控的filter
    @Bean
    //过滤器
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        Map<String,String> initParams = new HashMap<>();
        initParams.put("exclusions","*.js,*.css,/druid/*");  //什么样的请求来触发

        bean.setInitParameters(initParams);

        bean.setUrlPatterns(Arrays.asList("/*"));

        return  bean;
    }
}

```

配置完后访问http://localhost:8080/druid/login.html验证是否配置成功

![image-20230530201719775](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530201719775.png)



详细见[Spring Boot——整合Druid](https://blog.csdn.net/Huang_ZX_259/article/details/122218607)

### Mybatis日志文件配置

路径src/main/resources/logback.xml



```xml
<configuration>
    <!-- %m输出的信息,%p日志级别,%t线程名,%d日期,%c类的全名,,,, -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d %p (%file:%line\)- %m%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>
    <appender name="baselog"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>log/base.log</File>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>log/rccp_demo.log.%d.%i</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy  class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!-- or whenever the file size reaches 64 MB -->
                <maxFileSize>64 MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <encoder>
            <pattern>
                %d %p (%file:%line\)- %m%n
            </pattern>
            <charset>UTF-8</charset> <!-- 此处设置字符集 -->
        </encoder>
    </appender>
    <root level="OFF" >
        <appender-ref ref="STDOUT" />
        <!--           <appender-ref ref="baselog" />-->
    </root>
    <logger name="com.codingzhi.springbootmvc.dao" level="DEBUG" additivity="false">      <!--这个name要改成自己的组名加查询时mapper  , DEBUG会出现一个DEBU检查-->
        <appender-ref ref="STDOUT" />
    </logger>
    <!--  <logger name="com.etc" level="DEBUG" additivity="false">
          <appender-ref ref="STDOUT" />
     </logger>
      <logger name="java.sql" level="DEBUG">
         <appender-ref ref="STDOUT" />
     </logger> -->
</configuration>
```

如果烦警告的话，在同目录下配置

log4j.properties

```properties
log4j.rootLogger = OFF,stdout
 
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

### 序列化

当我想将一个内容存进去，需要将所存的数据模型类序列化，才能存入，把序列化后的数据存入a.dat

序列化后的结果

![image-20230530220407182](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530220407182.png)

接着我们通过反序列化，读取a.dat，把类型转到最开始数据模型的类型，打印到控制台

![image-20230530220521097](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530220521097.png)

```java
//序列化
public class Test1 {
    @Test

    public void test10() throws FileNotFoundException {
        List<Data0> dlist = new ArrayList<>();

        Data0 d1 = new Data0();
        //创建两个数据
        d1.setName("tom");
        d1.setAge(10);
        dlist.add(d1);
        Data0 d2 = new Data0();
        d2.setName("jerry");
        d2.setAge(20);
        dlist.add(d2);

        ObjectOutputStream oos = null;  //输出流
        try {
            oos = new ObjectOutputStream(new FileOutputStream("D:/a.dat"));
            oos.writeObject(dlist);
            oos.flush();
            oos.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
    //反序列化
    @Test
    public void test11() {
        
        try {
            ObjectInputStream oos =  new ObjectInputStream(new FileInputStream("D:/a.dat"));  //FileInputStream输入流

            List< Data0 > dlist = (List<Data0>) oos.readObject();

            dlist.forEach(System.out::println);

        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
    }
}
```

\> [!WARNING]

\> 需要在数据模型下加

![image-20230530220729613](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230530220729613.png)

