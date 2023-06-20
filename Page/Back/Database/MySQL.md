# MySQL

## 事物：

MySQL 事务主要用于处理操作量大，复杂度高的数据。比如说，在人员管理系统中，你删除一个人员，你既需要删除人员的基本资料，也要删除和该人员相关的信息，如信箱，文章等等，这样，这些数据库操作语句就构成一个事务！

- 在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务。
- 事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。
- 事务用来管理 insert,update,delete 语句

一般来说，事务是必须满足4个条件（ACID）：：原子性（**A**tomicity，或称不可分割性）、一致性（**C**onsistency）、隔离性（**I**solation，又称独立性）、持久性（**D**urability）。

- **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
- **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
- **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
- **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

## sql库自带的saklia库

![image-20230620105535732](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230620105535732.png)

[saklia库简单介绍](https://zhuanlan.zhihu.com/p/50682992)

## 建表

```mysql
CREATE TABLE sakila.actor (      #创建表格  "sakila"是数据库的名称，"actor"是表格的名称
  actor_id smallint UNSIGNED NOT NULL AUTO_INCREMENT,  #smallint属性类型  UNSIGNED"表示只允许非负数值  "NOT NULL"表示该列不能包含空值    AUTO_INCREMENT从1自增
  first_name varchar(45) NOT NULL,   
  last_name varchar(45) NOT NULL,
  last_update timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, #是一个时间戳列  "DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP"表示默认值为当前时间，并在每次更新行时更新为当前时间
  PRIMARY KEY (actor_id) #设置主键
)
ENGINE = INNODB,   #使用InnoDB存储引擎。InnoDB是一种支持事务和行级锁定的存储引擎
AUTO_INCREMENT = 201,
AVG_ROW_LENGTH = 81,
CHARACTER SET utf8,  #设置字符集为utf8
COLLATE utf8_general_ci;  #设置排序规则为utf8_general_ci，其中"ci"表示不区分大小写，而bin则区分大小写

ALTER TABLE sakila.actor   #alter修改内容
ADD INDEX idx_actor_last_name (last_name);   #last_name列上添加了一个索引。索引可以提高对该列的查询性能，加快检索速度。
```

**补充：**

GBK:中文每个字符占用2个字节,英文1个字节

UTF-8:中文每个字符占用3个字节.英文1个字节

## 连接查询

```sql
SELECT *
FROM customer c
INNER JOIN address a ON c.address_id = a.address_id  #INNER JOIN（内连接）来将两个表连接起来

#补，第一次建立连接inner join ,之后可以用left join 

#多表连接
SELECT *
FROM film f
INNER JOIN film_category fc ON f.film_id = fc.film_id
INNER JOIN category c ON fc.category_id = c.category_id
ORDER BY fc.film_id;
```

**补充：**

1. INNER JOIN（内连接）：
   - INNER JOIN返回两个表之间的匹配行。只有在连接条件满足时，才会返回结果集中的数据。
   - 它基于连接条件从两个表中选择满足条件的行，将它们合并成一个结果集。
   - 如果两个表中的某行在另一个表中没有匹配项，那么该行将被忽略，不包含在结果集中。
2. LEFT JOIN（左连接）：
   - LEFT JOIN返回左表（在查询语句中指定在左侧）的所有行以及右表（在查询语句中指定在右侧）中匹配行的组合。
   - 它返回左表中的所有行，无论右表中是否有匹配行。
   - 如果左表中的某行在右表中没有匹配项，那么结果集中将包含左表的行以及右表的列为NULL的值。

```sql
#用于添加全文索引的关键字和指令
ALTER TABLE sakila.film_text   
ADD FULLTEXT INDEX idx_title_description (title, description);

#idx_title_description是索引的名称。括号中的(title, description)指定了要在哪些列上创建全文索引。
```

**`FULLTEXT`补充：**

FULLTEXT索引使用一种称为[倒排索引](https://blog.csdn.net/qq_43403025/article/details/114779166)（简单来说就是根据单词也就是关键字来插找对应的文档）的数据结构。它会将文本字段中的单词进行分词，并将这些单词与它们所在的记录相关联。这使得在进行搜索时，可以根据关键词快速找到匹配的记录。

FULLTEXT索引提供了以下功能和特点：

1. 关键词搜索：可以使用关键词或短语在文本字段上进行搜索，而不需要完全匹配。它支持基于关键词的模糊匹配和部分匹配。
2. 排序和相关性：FULLTEXT索引可以根据匹配的相关性对搜索结果进行排序，使得与搜索条件最相关的记录排在前面。
3. 停用词过滤：FULLTEXT索引可以排除常见的停用词（如"and"、"the"等），以提高搜索结果的质量和准确性。
4. 分词支持：FULLTEXT索引可以根据特定的分词规则对文本进行分词，例如根据空格、标点符号等进行分割。

要使用FULLTEXT索引，需要在表的指定列上创建全文索引。在创建索引时，需要注意以下几点：

- 只有MyISAM和InnoDB存储引擎支持FULLTEXT索引，其他存储引擎（如MEMORY或CSV）不支持。

- 表必须具有字符类型的列（如VARCHAR或TEXT），才能在其上创建FULLTEXT索引。

- FULLTEXT索引不能同时包含太短或常见的单词，例如单个字符或频繁出现的词。

- FULLTEXT搜索功能的性能取决于数据的大小和索引的构建方式。



## 1:n或n:m

### 1. FK用于解决 1对多关系：FK在多

例如： 一个city有多个address;  外键在address

### 2. 多对多场景：用1对多和多对一的中间表设计

例如： 电影和类别；  



### 3. 用标记字段解决多对多

 例如： 学生和爱好。不适合拆分

### 4.  自关联：

例如：多级菜单，权限

![image-20230522201327400](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230522201327400.png)

## SQL语法

### 结构化查询

[详细内容](https://blog.csdn.net/promsing/article/details/112793260)

- DDL： 数据定义语言：  

  - create , alter , drop , truncate

    ```SQL
    # DDL
    CREATE TABLE emp(
      eno int NOT NULL PRIMARY KEY AUTO_INCREMENT,
      ename varchar(16),
      hiredate date,
      hiredate2 varchar(20)
    );
    ALTER TABLE emp ADD job varchar(20) COMMENT '工作';
    DROP TABLE emp;
    -- 截断表 相当于DML: delete from  + commit;
    TRUNCATE TABLE emp;  #无法后悔撤销
    ```

    

- DML:  数据操纵语言

  - select (DQL)、 Insert、update、 delete :: CURD;

  - DQL:select

    ```sql
    SELECT *
    FROM emp e;
    WHERE 条件
    GROUP BY 分组字段
    HAVING 分组条件
    ORDER BY 排序字段
    LIMIT  n， m
    ```

  

- DCL :  数据控制语言：

  - grant ..to  ,    revoke..from..

    

- TCL:  事务控制语言

  - commit,   rollback,  savepoint



### mysql函数:

[详细内容](https://blog.csdn.net/sundna/article/details/89926586)

#### 分组函数：

- avg,sum,min,max, count, gorup..concat

※ null+ 1 结果是null.   sum，avg不算null值。

※ count(null); 不算null值

#### 字符函数：

- concat:  拼接
- trim:    去除前后空格
- length:  字符个数

#### 日期格式化

- date_format

- str_to_date
  - 模式问题`%Y-%m-%d %H:%i:%s`

```sql
SELECT NOW(),DATE_FORMAT(NOW(),'%Y-%m-%d %H:%i:%s'),STR_TO_DATE('2023-06-1 18:57:17','%Y-%m-%d %H:%i:%s') 
```

#### 空值判断急分支语句

- ifnull(字段，值)

- `case when then  when then ... else .. end`

- ```sql
  -- 空值转换
  SELECT IFNULL(NULL, 1);
  -- 分支语句
  SELECT IFNULL(description, '没有描述'), f.title, f.film_id, f.description, f.length,
      CASE WHEN f.length > 60 AND f.length <= 100 THEN '长'
      WHEN f.length > 100 THEN '超长'
      ELSE '正常'
      END AS "时长"
  FROM film f;
  
  ```

### 内连接和左外链接

关联条件完全一致的数据

> 隐式内连接：使用where条件消除无用数据

```sql
  -- 查询员工表的名称，性别。部门表的名称
  SELECT emp.name,emp.gender,dept.name FROM emp,dept WHERE emp.`dept_id` = dept.`id`;

  SELECT 
      t1.name, -- 员工表的姓名
      t1.gender,-- 员工表的性别
      t2.name -- 部门表的名称
  FROM
      emp t1,
      dept t2
  WHERE 
      t1.`dept_id` = t2.`id`;
```

> 显式内连接

```sql
  -- 语法： 
  select 字段列表 from 表名1 [inner] join 表名2 on 条件
  -- 例如：
  SELECT * FROM emp INNER JOIN dept ON emp.`dept_id` = dept.`id`;   
  SELECT * FROM emp JOIN dept ON emp.`dept_id` = dept.`id`; 
```

> 左外链接，以坐标为主，关联条件没有的也显示。关联表中数据显示null

```sql
-- 语法：select 字段列表 from 表1 left [outer] join 表2 on 条件；
-- 例子：
-- 查询所有员工信息，如果员工有部门，则查询部门名称，没有部门，则不显示部门名称
SELECT  t1.*,t2.`name` FROM emp t1 LEFT JOIN dept t2 ON t1.`dept_id` = t2.`id`;
```



## Triggers触发器

Triggers（触发器）是一种特殊的数据库对象，用于在数据库表上定义一些自动执行的操作。它们是响应特定事件发生时自动触发的一系列SQL语句。

触发器通常与数据库表相关联，当插入（INSERT）、更新（UPDATE）或删除（DELETE）操作在表上执行时，触发器会自动触发并执行定义的操作。触发器可以在事务提交前或提交后执行，具体取决于触发器的定义。

例 删除：

```sql
CREATE 
	DEFINER = 'root'@'localhost'
TRIGGER sakila.del_film 
	AFTER DELETE #触发器将在执行DELETE操作后触发。当从film表中删除一条记录时，触发器将被激活
	ON sakila.film
	FOR EACH ROW  #触发器将针对每个受影响的行（被删除的行）执行操作。
BEGIN
  DELETE
    FROM film_text
  WHERE film_id = old.film_id;    #old.film_id代表 film表 的film_id，film_id代表film_text表里的film_id，也就是通过这样相同匹配，将两个表按照某种意义的关联，来构建触发器
END
```

例 插入：

```sql
CREATE 
	DEFINER = 'root'@'localhost'
TRIGGER sakila.ins_film
	AFTER INSERT
	ON sakila.film
	FOR EACH ROW
BEGIN
  #将新插入的行的film_id、title和description值插入到film_text表中
  INSERT INTO film_text (film_id, title, description)
    VALUES (new.film_id, new.title, new.description);  
END
```

> **如何让从数据库里查到的电影对应的演员可以合并起来并以，分割**

最开始是无法将演员列表跟到film后面

```sql
SELECT *
FROM film f
INNER JOIN film_actor fa ON f.film_id = fa.film_id
INNER JOIN actor a ON fa.actor_id = a.actor_id
ORDER BY fa.film_id;
```

要制作成类似于这种格式

![image-20230522210221717](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230522210221717.png)

只不过是电影名对应演员列表，需要用到concat  字符串拼接

```sql
SELECT  f.film_id,
        f.title, 
        f.description, 
        CONCAT(a.first_name,' ',a.last_name) AS fullname  #将名字拼接并以空格分开，并以fullname命名

FROM film f
INNER JOIN film_actor fa ON f.film_id = fa.film_id
INNER JOIN actor a ON fa.actor_id = a.actor_id
-- GROUP BY f.film_id
ORDER BY f.film_id;
```

![image-20230523112016223](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230523112016223.png)

在修改先以film_id分组，在` GROUP_CONCAT`将每组下演员组合起来

```sql
SELECT  f.film_id,
        f.title, 
        f.description, 
        GROUP_CONCAT(CONCAT(a.first_name,' ',a.last_name)) AS fullname

FROM film f
INNER JOIN film_actor fa ON f.film_id = fa.film_id
INNER JOIN actor a ON fa.actor_id = a.actor_id
GROUP BY f.film_id
ORDER BY f.film_id;
```

![image-20230523112614022](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230523112614022.png)

## 倒排索引补充

创建倒排索引是对正向索引的一种特殊处理，流程如下：

将每一个文档的数据利用算法分词，得到一个个词条
创建表，每行数据包括词条、词条所在文档id、位置等信息
因为词条唯一性，可以给词条创建索引，例如hash表结构索引
如图：

![image-20230522204917210](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230522204917210.png)

倒排索引的搜索流程如下（以搜索"华为手机"为例）：

1）用户输入条件"华为手机"进行搜索。

2）对用户输入内容分词，得到词条：华为、手机。

3）拿着词条在倒排索引中查找，可以得到包含词条的文档id：1、2、3。

4）拿着文档id到正向索引中查找具体文档。

![image-20230522204933324](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230522204933324.png)

### 启动

kibana是elasticsearch的可视化界面，elasticsearch默认是本地端口9200

![image-20230613140611555](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230613140611555.png)

![image-20230613140617300](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230613140617300.png)

![image-20230613143436729](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230613143436729.png)

![image-20230613143349568](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230613143349568.png)

默认地址http://localhost:5601

![image-20230613143804005](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230613143804005.png)

[使用参考](https://www.jianshu.com/p/92e49c18ac96)

[elasticsearch官方文档](https://www.elastic.co/guide/cn/elasticsearch/guide/current)

```json
# 添加数据 /索引名称/类型名称/文档id
PUT /index_test1/doc/1
{
  "name": "小明",
  "age": 10
}

# 获取数据
GET /index_test 1/doc/1

PUT /index_test1/doc/2
{
  "name": "小花",
  "age": 10
}

# POST # 修改数据 /索引名称/类型名称/文档id
PUT /index_test1/doc/2
{
  "name": "小花花",
  "age": 10
}

# 获取所有数据
GET /index_test1/_search

# 条件查询 使用分词来查   其他带小的也会被查出来，但我们可以根据得分来判断匹配度，其他则是模糊查询
GET /index_test1/doc/_search
{
  "query": {
    "match": {
      "name": "小明"
    }
  }
}
```

### logstash

可以连接数据库，做到不同与数据库的查询。

![image-20230613200546447](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230613200546447.png)

根据自己的数据库修改，我将我的配置放在mysql.conf

```json
# 将刚刚配置连接的数据库信息查询一下
#查询全部
GET /wx_test/_search
{
    "query":{
        "match_all":{}
    }
}
#查询单个内容
GET /wx_test/_search
{
    "query":{
        "match":{
            "description": "Robot"
        }
    }
}
```

![image-20230613164704394](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/5/image-20230613164704394.png)

## 数据库索引(B+Tree)

索引是为了提高数据的查询速度，相当于给数据进行编号，在查找数据的时候就可以通过编号快速找到对应的数据。

### 二叉查找树

左子树的键值小于根的键值，右子树的键值大于根的键值。 

![索引](https://img-blog.csdn.net/20160202203355523)

### 平衡二叉树（AVL Tree）

平衡二叉树（AVL树）在符合二叉查找树的条件下，还满足任何节点的两个子树的高度最大差为1。（最低和最高子树之间高度不能超过1）

![索引](https://img-blog.csdn.net/20160202203554663)

平衡二叉树（AVL树）在符合二叉查找树的条件下，还满足任何节点的两个子树的高度最大差为1。

![索引](https://img-blog.csdn.net/20160202203648148)

**左边多则第一个字母就是L反之为R,最低子树在右边则是R,反之是L**

### 平衡多路查找树（B-Tree）

B-Tree结构的数据可以让系统高效的找到数据所在的磁盘块。

B-Tree中的每个节点根据实际情况可以包含大量的关键字信息和分支，如下图所示为一个3阶的B-Tree： 
![索引](https://img-blog.csdn.net/20160202204827368)

每个节点占用一个盘块的磁盘空间，一个节点上有两个升序排序的关键字和三个指向子树根节点的指针，指针存储的是子节点所在磁盘块的地址。两个关键词划分成的三个范围域对应三个指针指向的子树的数据的范围域。以根节点为例，关键字为17和35，P1指针指向的子树的数据范围为小于17，P2指针指向的子树的数据范围为17~35，P3指针指向的子树的数据范围为大于35。

模拟查找关键字29的过程：

1. 根据根节点找到磁盘块1，读入内存。【磁盘I/O操作第1次】
2. 比较关键字29在区间（17,35），找到磁盘块1的指针P2。
3. 根据P2指针找到磁盘块3，读入内存。【磁盘I/O操作第2次】
4. 比较关键字29在区间（26,30），找到磁盘块3的指针P2。
5. 根据P2指针找到磁盘块8，读入内存。【磁盘I/O操作第3次】
6. 在磁盘块8中的关键字列表中找到关键字29。

分析上面过程，发现需要3次磁盘I/O操作，和3次内存查找操作。由于内存中的关键字是一个有序表结构，可以利用二分法查找提高效率。而3次磁盘I/O操作是影响整个B-Tree查找效率的决定因素。B-Tree相对于AVLTree缩减了节点个数，使每次磁盘I/O取到内存的数据都发挥了作用，从而提高了查询效率。



### B+Tree

B+Tree是在B-Tree基础上的一种优化，使其更适合实现外存储索引结构，InnoDB存储引擎就是用B+Tree实现其索引结构。

从上一节中的B-Tree结构图中可以看到每个节点中不仅包含数据的key值，还有data值。而每一个页的存储空间是有限的，如果data数据较大时将会导致每个节点（即一个页）能存储的key的数量很小，当存储的数据量很大时同样会导致B-Tree的深度较大，增大查询时的磁盘I/O次数，进而影响查询效率。在B+Tree中，所有数据记录节点都是按照键值大小顺序存放在同一层的叶子节点上，而非叶子节点上只存储key值信息，这样可以大大加大每个节点存储的key值数量，降低B+Tree的高度。

**B+Tree相对于B-Tree有几点不同：**

1. 非叶子节点只存储键值信息。
2. 所有叶子节点之间都有一个链指针。
3. 数据记录都存放在叶子节点中。

主键自带索引

```mysql
insert into user(id,name) values(1, ' a ');
insert into user(id,name) values(7, ' g ');
insert into user(id,name) values(6, ' f ');
insert into user(id,name) values(4, ' d ');
insert into user(id,name) values(3, ' c ');
insert into user(id,name) values(2, ' b ');
insert into user(id,name) values(5, ' e ');
```

![image-20230522184958491](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/3/image-20230522184958491.png)

乱序插入，会自动按照Id进行升序排列，这是因为主键自带索引，数据存储的内部结构类似于链表的形式，通过指针关联不同数据。

![image-20230522185604439](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/3/image-20230522185604439.png)

数据量大的时候，查询还是很慢。

MySQL中有一个page的概念，相当于给数据进行分页，把一部分数据存入一个page 中，如下所示

![image-20230522190101926](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/3/image-20230522190101926.png)

每个page可以存储16kb的数据，这样就相当于给数据建立了上层目录，	先找大目录，在找具体的数据。

![image-20230522190833347](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/3/image-20230522190833347.png)

把每个page 中的第一条数据的id+指针存入page目录中，查询数据的时候,先找到它的page，再进入page中查找具体的数据。
一个page目录中也可以存储16KB的数据，如果是海量数据，page目录也会有很多层，为了提高查询效率，MySQL给page目录再加一层目录。

![image-20230522191433889](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/3/image-20230522191433889.png)

这种结构就是**B+Tree** 

假设一条记录的空间32个byte，最底层一个单元可以存储数据`16*1024/32=512`
第二层只需要记录id+p，假设是6个byte，则可以保存的数据是`16*1024/6=2730`
第三次存储的数据`16*1024/6=2730`
总共可以存储的数据是`512*2730*2730=38亿`

所以一般不会超过三层



**参考学习：**

[楠哥讲B+Tree](https://www.bilibili.com/video/BV1HX4y1N7v6/?spm_id_from=333.337.search-card.all.click&vd_source=d352b184fac32756b0364294c288a22f)