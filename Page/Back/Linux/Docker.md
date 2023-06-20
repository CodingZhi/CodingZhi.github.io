# **linux—docker：**

## docker

+ 安装docker所需的软件包

![image-20230423210331324](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423210331324.png)

+ 安装成功

![image-20230423210450182](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423210450182.png)

+ 设置yum源为阿里云 

![image-20230423210611305](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423210611305.png)

+ 更新yum软件包索引

![image-20230423210714222](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423210714222.png)

+ 安装docker CE(社区版)

  ![image-20230423211024030](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423211024030.png)

![image-20230423210959740](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423210959740.png)

+ 启动docker，为了方便加入到开机自启

![image-20230423211209665](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423211209665.png)

+ 查看信息docker![image-20230423211359651](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423211359651.png)

+ 设置阿里镜像加速器

  将阿里给自己配置的镜像加速器地址复制到/etc/docker/daemon.json文件中

  ![image-20230423212706953](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423212706953.png)

  重新加载daemon.json配置文件 并 重启docker服务

  ![image-20230423212845546](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423212845546.png)

  查看docker配置，看镜像是否配置成功

  ![image-20230423213047059](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423213047059.png)![image-20230423213049537](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423213049537.png)

## Portainer容器

账户：admin

密码：

+  拉取portainer镜像

![image-20230423213546874](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423213546874.png)

+ 创建并运行容器

  ![image-20230423213704520](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423213704520.png)

+ 在浏览器访问服务器地址+9000![image-20230423213945799](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423213945799.png)

![image-20230423214143980](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423214143980.png)

+ 进入Portainer管理页面

  ![image-20230423214333413](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423214333413.png)

3、使用Portainer管理Docker上的资源

+ 去dockerhub官网查看想要的镜像版本号

![image-20230423230248820](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423230248820.png)

![image-20230423230349102](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423230349102.png)

+ pull成功

![image-20230423230704161](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423230704161.png)

+ 添加容器

![image-20230423220216743](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423220216743.png)

![image-20230423231602424](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423231602424.png)

+ 部署容器

![image-20230423220603799](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423220603799.png)

+ 部署成功

![image-20230423231408471](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423231408471.png)

+ 访问nginx

![image-20230423231711812](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423231711812.png)

+ 容器的其他操作

![image-20230423232344453](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423232344453.png)

![image-20230423232551848](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230423232551848.png)

## Redis N0SQL数据库

**键值数据库**

+ 没有声明性查询语言

+ 没有预定义的模式

+ 键–值对存储，列存储，文档存储，图形数据库

+ 最终—致性，而非ACID(事物)性

+ 非结构化和不可预知的数据

### 特点

+ Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用

+ Redis不仅仅支持简单的key-value类型的数据，同时还提供List，set等数据类型

+ Redis支持数据的备份

### redis类型

Redis中数据是key-value形式，key为字符串类型，value可取类型:

+ String 字符串

+ Hash 哈希表
+ List列表
+ Set集合（去重）
+ Sorted Set有序集合(zset)（排序）
+ Bit arrays (或者说simply bitmaps)（统计）
+ HyperLogLogs  （pv页面访问量和uv用户访问量）
+ Streams

**docker容器建立和挂载本地**

```shell
docker run -itd --name redis-test1 -v /data/redis-data/redis-test1/redis.conf:/etc/redis/redis.conf -v /data/redis-data/redis-test1/data:/data --ip 127.0.0.1 -p 6379:6379 redis redis-server /etc/redis/redis.conf/redis.conf
```



### 常用命令

[redis命令](https://redis.com.cn/commands.html)

```shell
set k1 "hello redis"  #设置k值  若原key存在则覆盖
set k3 bb ex 30  #设置存放时间 k3存放30秒
SETNX k2 "hello "  #若k2存在则不改变，若k2不存在则添加

get k1   #获取命令
keys *   #查看所有的key

EXISTS key [key_value]   #判断key存不存在  
#存在(integer) 1   不存在"(nil)"

ttl k2 #查看k2还剩多少时间  
#(integer) -1表示没设置时间   #(integer) 33表示剩余时间  #(integer) -2表示过期

EXPIRE k2 5 #设置过期时间  5秒后过期

del k1 #删除key

GETRANGE key start end  #取value中某一区间  GETRANGE k1 1 5

INCR k2 #自增1    
DECR k2  #减1
INCRBY k2 100  #自增100
DECRBY k2 100   #自减100 
```

**Hash**

```shell
HSET k3 name tom age 10
HGET k3 name
HGETALL k3
HMGET k3 name age
```

**List**

```shell
LPUSH k4 aaa bbb  #push进  是按栈来的 先进后出
RPUSH k4 ccc ddd   #按队来的先进先出
LRANGE k4 0 -1    #遍历从头到尾  跟python一样
LPOP k4   #排最顶上
RPOP k4    #排最后
```

Set

```shell
ZADD k6  100 001 30 002 60 003 80 004

ZRANGE k6 0 -1
1) "002"
2) "003"
3) "004"
4) "001"

ZRANGE k6 0 -1 withscores
1) "002"
2) "30"
3) "003"
4) "60"
5) "004"
6) "80"
7) "001"
8) "100"

ZRANGEBYSCORE k6 60 100 withscores  #把60-100分数的取出来
1) "003"
2) "60"
3) "004"
4) "80"
5) "001"
6) "100"

ZREVRANGE k6  0 3 withscores  #按从到到小把下标0-3的数据打印出
1) "001"
2) "100"
3) "004"
4) "80"
5) "003"
6) "60"
7) "002"
8) "30"

ZINCRBY k6 21 xio  #新增
```

### 主从复制

启动slave1

```shell
docker run -p 6390:6379 --name redis-slave1 -v /data/redis-data/redis-slave1/redis.conf:/etc/redis/redis.conf -v /data/redis-data/redis-slave1/data:/data -d redis redis-server /etc/redis/redis.conf/redis.conf
```

以上步骤同建立slave2

docker inspect redis-test  根据这个命令看Ipadress

上传配置文件reids.conf

```shell
#bind 127.0.0.1
protected-mode no
appendonly yes
# replicaof <master ip> <master port>
replicaof 127.0.0.2 6379  #拷贝6379 根据自己修改
```

### 哨兵

假设主机挂了，从机变成主机

启动容器

```shell
[root@localhost ~]# docker run -p 16379:16379 --name sentinel1  -v /data/sentinel/redis-sentinel-1.conf:/etc/sentinel/redis-sentinel.conf -v  /data/sentinel/data1:/etc/sentinel/ -w /etc/sentinel/  -d redis redis-server  /etc/sentinel/redis-sentinel.conf/redis-sentinel-1.conf --sentinel
d0f56f946ccf0b586a03d4d5dc67d1744d307baa2b0e8037b49e293e352e9d95
[root@localhost ~]# docker run -p 16380:16379 --name sentinel2  -v /data/sentinel/redis-sentinel-2.conf:/etc/sentinel/redis-sentinel.conf -v  /data/sentinel/data2:/etc/sentinel/ -w  /etc/sentinel/  -d redis redis-server  /etc/sentinel/redis-sentinel.conf/redis-sentinel-2.conf --sentinel
74ff9543ccc154dcb537161622e775d2c69ccdd3ffa6cb548145ae15c215a656
[root@localhost ~]# docker run -p 16381:16379 --name sentinel3  -v /data/sentinel/redis-sentinel-3.conf:/etc/sentinel/redis-sentinel.conf -v /data/sentinel/data3:/etc/sentinel/ -w  /etc/sentinel/  -d redis redis-server  /etc/sentinel/redis-sentinel.conf/redis-sentinel-3.conf --sentinel
72c27ef1c7a87999da3b4b05b9531452a89421c468ab9dd5743aa066d2ed5ee0
```

配置

redis-sentinel-1.conf配置

```shell
# 哨兵的端口号
# 因为各个哨兵节点会运行在单独的Docker容器中
# 所以无需担心端口重复使用
# 如果需要在单机
port 26379
# 配置哨兵的监控参数
# 格式：sentinel monitor <master-name> <ip> <redis-port> <quorum>
# master-name是为这个被监控的master起的名字
# ip是被监控的master的IP或主机名。因为Docker容器之间可以使用容器名访问，所以这里写master节点的容器名
# redis-port是被监控节点所监听的端口号
# quorom设定了当几个哨兵判定这个节点失效后，才认为这个节点真的失效了
sentinel monitor local-master 172.17.0.2 6379 2
# master在连续多长时间无法响应PING指令后，就会主观判定节点下线，默认是30秒
# 格式：sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds local-master 30000
```

### redis集群

```shell
docker create --name redis-node1 --net host -v /data/redis-data/node1:/data redis  redis-server redis6380.conf --port 6380 --protected-mode no

docker create --name redis-node2 --net host -v /data/redis-data/node2:/data redis redis-server redis6381.conf --port 6381 --protected-mode no

docker create --name redis-node3 --net host -v /data/redis-data/node3:/data redis redis-server redis6382.conf --port 6382 --protected-mode no

docker create --name redis-node4 --net host -v /data/redis-data/node4:/data redis redis-server redis6383.conf --port 6383 --protected-mode no

docker create --name redis-node5 --net host -v /data/redis-data/node5:/data redis redis-server redis6384.conf --port 6384 --protected-mode no

docker create --name redis-node6 --net host -v /data/redis-data/node6:/data redis redis-server redis6385.conf --port 6385 --protected-mode no
```

```shell
docker start redis-node1 redis-node2 redis-node3 redis-node4 redis-node5 redis-node6
```

```shell
docker exec -it redis-node1 /bin/bash
```

```shell
redis-cli -p 6380 --cluster create 127.0.0.1:6380 127.0.0.1:6381 127.0.0.1:6382 127.0.0.1:6383 127.0.0.1:6384 127.0.0.1:6385 --cluster-replicas 1
```

## MySQL

[参考](https://blog.csdn.net/feiying0canglang/article/details/127776335)

mkdir -p /data/docker/mysql/conf
mkdir -p /data/docker/mysql/data
mkdir -p /data/docker/mysql/log

固定容器IP

```shell
 docker network create --subnet=172.20.0.0/24 dockernet  
```

创建容器

```
docker run -it --net dockernet --ip 172.20.0.9 -p 3306:3306 --name mysql8 --privileged=true -v /work/docker/mysql/log:/var/log/mysql -v /work/docker/mysql/data:/var/lib/mysql -v /work/docker/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --restart=always -d mysql
```

根据自己的配置，修改my.cnf

## NACOS

### Linux

动态服务发现、配置管理和服务管理平台

[教程](https://blog.csdn.net/m0_53151031/article/details/123118920)

```shell
docker run -it --net dockernet --ip 172.20.0.10 -e PREFER_HOST_MODE=ip -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=172.20.0.9 -e MYSQL_SERVICE_PORT=3306 -e MYSQL_SERVICE_DB_NAME=ry-config -e MYSQL_SERVICE_USER=root \
-e MYSQL_SERVICE_PASSWORD=wuzihan2@ -p 8848:8848 -p 9848:9848 --name nacos --restart=always -d nacos/nacos-server
```

重装

```shell
docker run -it --net dockernet --ip 172.20.0.11 -e PREFER_HOST_MODE=ip -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=mysql -e MYSQL_SERVICE_HOST=172.20.0.9 -e MYSQL_SERVICE_PORT=3306 -e MYSQL_SERVICE_DB_NAME=ry-config -e MYSQL_SERVICE_USER=root \
-e MYSQL_SERVICE_PASSWORD=wuzihan2@ -v /work/docker/nacos/logs:/home/nacos/logs -v /work/docker/nacos/conf:/home/nacos/conf -p 8849:8848 -p 9849:9848 --name nacos  -d nacos/nacos-server
```

账户：nacos

密码：nacos
