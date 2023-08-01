# SQL

## Tips

### NO SELECT *

select语句查询要配合有需要的字段

反例子：

```sql
select * from employee;
```

正例子：

```sql
select id，name from employee;
```

理由：

- 只取需要的字段，节省资源、减少网络开销。
- select * 进行查询时，很可能就不会使用到覆盖索引了，就会造成[回表查询](https://www.zhihu.com/search?q=回表查询&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})。



### 如果结果确定只有一条，加上Limit

理由：

- 加上limit语句之后，如果已经查找到符合条件的结果，就不会继续向下扫描，扫描效率将大大提高。
- 当然，如果name是唯一索引的话，是不必要加上limit 1了，因为limit的存在主要就是为了防止全表扫描，从而提高性能,如果一个语句本身可以预知不用[全表扫描](https://www.zhihu.com/search?q=全表扫描&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})，有没有limit ，性能的差别并不大。



### 避免在where中使用or

假设现在需要查询[userid](https://www.zhihu.com/search?q=userid&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})为1或者年龄为18岁的用户，很容易有以下sql

反例:

```sql
select * from user where userid=1 or age =18
```

正例：

```sql
//使用union all
select * from user where userid=1 
union all 
select * from user where age = 18

//或者分开两条sql写：
select * from user where userid=1
select * from user where age = 18
```

理由：

使用or可能会使索引失效，从而全表扫描。



### 避免向客户端返回大量数据

假设业务需求是，用户请求查看自己最近一年观看过的直播数据。

反例：

```sql
//一次性查询所有数据回来
select * from LivingInfo where watchId =useId and watchTime >= Date_sub(now(),Interval 1 Y)
```

正例：

```sql
//分页查询
select * from LivingInfo where watchId =useId and watchTime>= Date_sub(now(),Interval 1 Y) limit offset，pageSize

//如果是前端分页，可以先查询前两百条记录，因为一般用户应该也不会往下翻太多页，
select * from LivingInfo where watchId =useId and watchTime>= Date_sub(now(),Interval 1 Y) limit 200;
```







### 优化limit的分页

我们日常做分页需求时，一般会用 limit 实现，但是当偏移量特别大的时候，查询效率就变得低下。

反例：

```sql
select id，name，age from employee limit 10000，10
```

正例：

```sql
//方案一 ：返回上次查询的最大记录(偏移量)
select id，name from employee where id>10000 limit 10.

//方案二：order by + 索引
select id，name from employee order by id  limit 10000，10

//方案三：在业务允许的情况下限制页数：
```

理由：

- 因为limit并非直接定位到某个位置，而是要先把偏移量+要取得的条数然后再把前面的数据舍弃掉。
- 如果使用优化方案一，返回上次最大查询记录（偏移量），这样可以跳过偏移量，效率提升不少。

- 方案二使用order by+索引，也是可以提高查询效率的。
- 方案三的话，建议跟业务讨论，有没有必要查这么后的分页啦。因为绝大多数用户都不会往后翻太多页。



### 优化like模糊查询

like的模糊查询也有可能导致索引失效。

反例：

```sql
select userId，name from user where userId like '%123';
```

正例：

```sql
select userId，name from user where userId like '123%';
```

理由：

- 把%放前面，并不走索引，
- 把% 放关键字后面，还是会走索引的。



### 优化where查询，避免多余的行返回值

反例：

```sql
List<Long> userIds = sqlMap.queryList("select userId from user where isVip=1");
boolean isVip = userIds.contains(userId);
```

正例：

```sql
Long userId = sqlMap.queryObject("select userId from user where userId='userId' and isVip='1' ")
boolean isVip = userId！=null;
```

理由：

- 需要什么数据，就去查什么数据，避免返回不必要的数据，节省开销。



### 优化where查询，避免在where子句中进行表达式

在 where 子句中对字段进行表达式操作，这将导致系统放弃使用索引而进行全表扫

反例：

```sql
select * from user where age-1 =10；
```

正例：

```sql
select * from user where age =11；
```

理由：

- 虽然age加了索引，但是因为对它进行运算，索引直接迷路了



### 优化where查询，避免使用!=或者<>

反例：

```sql
select age,name  from user where age <>18;
```

正例：

```sql
//可以考虑分开两条sql写
select age,name  from user where age <18;
select age,name  from user where age >18;
```

理由：

- 使用!=和<>很可能会让索引失效





### 避免在索引列上使用内置函数

业务需求：查询最近七天内登陆过的用户（假设loginTime加了索引）

反例：

```sql
select userId,loginTime 
from loginuser 
where Date_ADD(loginTime,Interval 7 DAY) >=now();
```

正例：

```sql
explain  
select userId,loginTime 
from loginuser 
where  loginTime >= Date_ADD(NOW(),INTERVAL - 7 DAY);
```

理由：

- 索引列loginTime上使用了Date_ADD(loginTime, Interval 7 Day)内置函数，索引失效了
- 如果索引列不加内置函数，索引还是会走的。



### 使用联合索引要符合最左原则

表结构：（有一个联合索引idx_userid_age，userId在前，age在后）

```sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `userId` int(11) NOT NULL,
  `age` int(11) DEFAULT NULL,
  `name` varchar(255) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_userid_age` (`userId`,`age`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

反例：

```sql
select * from user where age = 10;
```

正例：

```sql
//符合最左匹配原则
select * from user where userid=10 and age =10；
//符合最左匹配原则
select * from user where userid =10;
```

理由：

- 当我们创建一个联合索引的时候，如(k1,k2,k3)，相当于创建了（k1）、(k1,k2)和(k1,k2,k3)三个索引，这就是最左匹配原则。
- 联合索引不满足最左原则，索引一般会失效，但是这个还跟MySQL优化器有关的。



### 避免冗余索引

反例：

```sql
KEY `idx_userId` (`userId`)
  KEY `idx_userId_age` (`userId`,`age`)
```

正例:

```sql
//删除userId索引，因为组合索引（A，B）相当于创建了（A）和（A，B）索引
  KEY `idx_userId_age` (`userId`,`age`)
```

理由：

- 重复的索引需要维护，并且优化器在优化查询的时候也需要逐个地进行考虑，这会影响性能的。



### inner join、left join、right join

优先使用inner join，如果使用left join左边选用较小的表。

inner join内连接，在两张表进行连接查询的时候，只保留两个表中完全匹配的结果集

left join左连接，会返回左表中的所有的行，即使在右表中没有匹配的记录

反例:

```sql
select * 
from tab1 t1 
left join 
tab2 t2  
on t1.size = t2.size 
where t1.id>2;
```

正例：

```sql
select * 
from (select * from tab1 where id >2) t1 
left join 
tab2 t2 
on t1.size = t2.size;
```

理由：

- inner join是等值连接，或许返回的行数比较少，所以性能会相对好一些。
- 同理因为使用了左连接，应该把限制条件的处理放在左表处理，让左表的记录数量尽可能少，返回的结果可能会更少。



### 表连接尽量使用别名

反例：

```sql
select * from A 
inner join B 
on A.deptId = B.deptId;
```

正例：

```sql
select memeber.name,deptment.deptName 
from A member 
inner join B deptment 
on member.deptId = deptment.deptId;
```







### 在where和order by的列上进行条件优化

反例：

```sql
select * from user where address ='深圳' order by age ;
```

正例：

```sql
添加索引
alter table user add index idx_address_age (address,age)
```



### 在group by之前过滤不需要的记录

反例：

```sql
select job，avg(salary) 
from employee  
group by job 
having job ='president' or job = 'managent'
```

正例：

```sql
select job，avg(salary) 
from employee 
where job ='president'or job = 'managent' 
group by job；
```



### 大量插入时使用批插入

反例：

```sql
for(User u :list){
 INSERT into user(name,age) values(#name#,#age#)   
}
```

正例：

```sql
//一次500批量插入，分批进行
insert into user(name,age) values
<foreach collection="list" item="item" index="index" separator=",">
    (#{item.name},#{item.age})
</foreach>
```

理由：

- 批量插入性能好，更加省时间

> 打个比喻:假如你需要搬一万块砖到楼顶,你有一个电梯,电梯一次可以放适量的砖（最多放500）,你可以选择一次运送一块砖,也可以一次运送500,你觉得哪个时间消耗大?



### 大量数据时优化删除和更新

避免同时修改或删除过多数据，因为会造成cpu利用率过高，从而影响别人对数据库的访问。

反例：

```sql
//一次删除10万或者100万+？
delete from user where id <100000;
//或者采用单一循环操作，效率低，时间漫长
for（User user：list）{
   delete from user；
}
```

正例：

```sql
//分批进行删除,如每次500
delete user where id<500
delete product where id>=500 and id<1000；
```

理由：

- 一次性删除太多数据，可能会有lock wait timeout exceed的错误，所以建议分批操作。



### where子句中使用默认值

反例：

```sql
select * from user where age is not null;
```

正例：

```sql
//设置0为默认值
select * from user where age>0;
```

理由：

- 并不是说使用了is null 或者 is not null 就会不走索引了，这个跟mysql版本以及查询[成本](https://www.zhihu.com/search?q=成本&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})都有关。

> 如果mysql优化器发现，走索引比不走索引成本还要高，肯定会放弃索引，这些条件`！=，>is null，is not null`经常被认为让索引失效，其实是因为一般情况下，查询的成本高，优化器自动放弃的。

- 如果把[null值](https://www.zhihu.com/search?q=null值&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})，换成默认值，很多时候让走索引成为可能，同时，表达意思会相对清晰一点。



### 避免超过5个表连接

- 连表越多，编译的时间和开销也就越大。
- 把[连接表](https://www.zhihu.com/search?q=连接表&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})拆开成较小的几个执行，可读性更高。
- 如果一定需要连接很多表才能得到数据，那么意味着糟糕的设计了。



### 避免超过5个索引

- 索引并不是越多越好，索引虽然提高了查询的效率，但是也降低了插入和更新的效率。
- insert或update时有可能会重建索引，所以建索引需要慎重考虑，视具体情况来定。
- 一个表的索引数最好不要超过5个，若太多需要考虑一些索引是否没有存在的必要。



### 避免建索引在重复型字段上

因为SQL优化器是根据表中数据量来进行查询优化的，如果索引列有大量重复数据，Mysql查询优化器推算发现不走索引的成本更低，很可能就放弃索引了。



### **exist & in的合理利用**

假设表A表示某企业的员工表，表B表示部门表，查询所有部门的所有员工，很容易有以下SQL:

```sql
select * from A where deptId in (select deptId from B);
```

这样写等价于：

> 先查询部门表B
> select deptId from B
> 再由部门deptId，查询A的员工
> select * from A where A.deptId = B.deptId

可以抽象成这样的一个循环：

```sql
List<> resultSet ;
    for(int i=0;i<B.length;i++) {
          for(int j=0;j<A.length;j++) {
          if(A[i].id==B[j].id) {
             resultSet.add(A[i]);
             break;
          }
       }
    }
```

显然，除了使用in，我们也可以用exists实现一样的查询功能，如下：

```sql
select * from A where exists (select 1 from B where A.deptId = B.deptId);
```

因为exists查询的理解就是，先执行主查询，获得数据后，再放到子查询中做条件验证，根据验证结果（true或者false），来决定主查询的数据结果是否得意保留。

那么，这样写就等价于：

> select * from A,先从A表做循环
> select * from B where A.deptId = B.deptId,再从B表做循环.

同理，可以抽象成这样一个循环：

```sql
List<> resultSet ;
    for(int i=0;i<A.length;i++) {
          for(int j=0;j<B.length;j++) {
          if(A[i].deptId==B[j].deptId) {
             resultSet.add(A[i]);
             break;
          }
       }
    }
```

数据库最费劲的就是跟程序链接释放。假设链接了两次，每次做上百万次的数据集查询，查完就走，这样就只做了两次；相反建立了上百万次链接，申请链接释放反复重复，这样系统就受不了了。即mysql优化原则，就是小表驱动大表，小的数据集驱动大的数据集，从而让性能更优。

因此，我们要选择最外层循环小的，也就是，如果**B的数据量小于A，适合使用in，如果B的数据量大于A，即适合选择exist**。



### 尽量使用union all

如果检索结果中不会有重复的记录，推荐union all 替换 union。

反例：

```sql
select * from user where userid=1 
union
select * from user where age = 10
```

正例：

```sql
select * from user where userid=1 
union all
select * from user where age = 10
```

理由：

- 如果使用union，不管检索结果有没有重复，都会尝试进行合并，然后在输出最终结果前进行排序。如果已知检索结果没有重复记录，使用union all 代替union，这样会提高效率。



### 尽量使用数字型而不是字符型

反例：

```text
king_id` varchar（20） NOT NULL COMMENT '守护者Id'
```

正例：

```text
`king_id` int(11) NOT NULL COMMENT '守护者Id'`
```

理由：

- 相对于数字型字段，字符型会降低查询和连接的性能，并会增加存储开销。



### 尽可能使用varchar/nvarchar而不是char/nchar

反例：

```sql
`deptName` char(100) DEFAULT NULL COMMENT '部门名称'
```

正例：

```sql
`deptName` varchar(100) DEFAULT NULL COMMENT '部门名称'
```

理由：

- 因为首先变长字段存储空间小，可以节省存储空间。
- 其次对于查询来说，在一个相对较小的字段内搜索，效率更高。



### 字符串类型使用where要加括号

反例：

```sql
select * from user where userid =123;
```

正例：

```sql
select * from user where userid ='123';
```

理由：

- 为什么第一条语句未加[单引号](https://www.zhihu.com/search?q=单引号&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})就不走索引了呢？这是因为不加单引号时，是字符串跟数字的比较，它们类型不匹配，MySQL会做隐式的类型转换，把它们转换为浮点数再做比较。



### 适当使用覆盖索引

覆盖索引能够使得你的SQL语句不需要回表，仅仅访问索引就能够得到所有需要的数据，大大提高了查询效率。

反例：

```sql
// like模糊查询，不走索引了
select * from user where userid like '%123%'
```

正例：

```sql
//id为主键，那么为普通索引，即覆盖索引登场了。
select id,name from user where userid like '%123%';
```



### 适当使用Distinct

distinct 关键字一般用来过滤重复记录，以返回不重复的记录。在查询一个字段或者很少字段的情况下使用时，给查询带来优化效果。但是在字段很多的时候使用，却会大大降低查询效率。

反例：

```sql
SELECT DISTINCT * from  user;
```

正例：

```sql
select DISTINCT name from user;
```

理由：

- 带distinct的语句cpu时间和占用时间都高于不带distinct的语句。因为当查询很多字段时，如果使用distinct，数据库引擎就会对数据进行比较，[过滤](https://www.zhihu.com/search?q=过滤&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"260536848"})掉重复数据，然而这个比较，过滤的过程会占用系统资源，cpu时间。



### 使用explain分析SQL语句

日常开发写SQL的时候，尽量养成一个习惯吧。用explain分析一下你写的SQL，尤其是走不走索引这一块。

```sql
explain select * from user where userid =10086 or age =18;
```



### MySQL中的日期查询

MYsql 针对where条件时间范围的查询效率方式对比

end_rec表，游戏记录表 时间字段 time_str 表示每局游戏结束时间，已经针对 time_str加了 B-tree 索引，一共68w条数据

需求：

查询今日的组局数量

1. 方式一，用 TO_DAYS() 函数

   ```sql
   select count(*) from end_rec where TO_DAYS(time_str) = TO_DAYS(NOW());
   ```

   耗时 0.15 sec

2. 方式二，用date_format 函数

   ```sql
   select count(*) from end_rec where date_format(time_str,'%Y%m%d')='20200229';
   ```

   耗时0.27sec

3. 方式三，用 > < 查询当天时间范围

   ```sql
   select count(*) from end_rec where time_str >= '2020-02-29' AND time_str <='2020-03-01';
   ```

   耗时0.02 sec 查询非常快

4. 方式四，用 SUBSTING函数

   ```sql
   select count(*) from end_rec where SUBSTRING(time_str,1,10)='2020-02-29';
   ```

   耗时 0.22秒基本和方式二差不多。

5. 方式五，用 between and 语句

   耗时0.02秒，非常快。 发现 between and 语句非常高效 和 方式3一样。

6. 方式六， 用UNIX_TIMESTAMP转换时间字符串为时间戳后查询

   2020-02-29 时间戳对应 1582904600

   2020-03-01 时间戳对应 1582992000;

   SQL语句：

   select count(*) from end_rec where UNIX_TIMESTAMP(time_str) > 1582904600 AND UNIX_TIMESTAMP(time_str) < 1582992000;耗时达到了惊人的 0.99，是方式3和方式5的接近一百倍。这个方式是性能最差的查询方案。

   总结，对有关时间范围的SQL查询，最好使用between AND, 或者 >, < 的在一个时间范围类的方式，这样可以有效利用索引。其他方式会使查询索引失效。
   





## MySQL慢查询优化

### Explain参数





## Leetcode例题

### SELECT

#### [595. 大的国家](https://leetcode.cn/problems/big-countries/)

World 表：

| Column Name | Type    |
| -- | -- |
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | int     |


name 是这张表的主键。
这张表的每一行提供：国家名称、所属大陆、面积、人口和 GDP 值。

如果一个国家满足下述两个条件之一，则认为该国是 大国 ：

面积至少为 300 万平方公里（即，3000000 km2），或者
人口至少为 2500 万（即 25000000）
编写一个 SQL 查询以报告 大国 的国家名称、人口和面积。

按 任意顺序 返回结果表。

```SQL
SELECT name, population, area 
FROM World 
WHERE area >= 3000000
OR population >= 25000000;
```





#### [1757. 可回收且低脂的产品](https://leetcode.cn/problems/recyclable-and-low-fat-products/)

表：`Products`

| Column Name | Type    |
| -- | -- |
| product_id  | int     |
| low_fats    | enum    |
| recyclable  | enum    |

product_id 是这个表的主键。
low_fats 是枚举类型，取值为以下两种 ('Y', 'N')，其中 'Y' 表示该产品是低脂产品，'N' 表示不是低脂产品。
recyclable 是枚举类型，取值为以下两种 ('Y', 'N')，其中 'Y' 表示该产品可回收，而 'N' 表示不可回收。

写出 SQL 语句，查找既是低脂又是可回收的产品编号。

```SQL
SELECT product_id
FROM Products
WHERE low_fats = 'Y'
AND recyclable = 'Y';
```





#### [584. 寻找用户推荐人](https://leetcode.cn/problems/find-customer-referee/)

给定表 `customer` ，里面保存了所有客户信息和他们的推荐人。

| id   | name | referee_id|
|--| --| --|
|    1 | Will |      NULL |
|    2 | Jane |      NULL |
|    3 | Alex |         2 |
|    4 | Bill |      NULL |
|    5 | Zack |         1 |
|    6 | Mark |         2 |

写一个查询语句，返回一个客户列表，列表中客户的推荐人的编号都**不是** 2。

```sql
SELECT name
FROM customer
WHERE referee_id IS NULL
OR referee_id != 2;
```





#### [183. 从不订购的客户](https://leetcode.cn/problems/customers-who-never-order/)

某网站包含两个表，`Customers` 表和 `Orders` 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

`Customers` 表：

| Id | Name  |
| -- | --|
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |

`Orders` 表：


| Id | CustomerId |
| -- | --|
| 1  | 3          |
| 2  | 1          |

例如给定上述表格，你的查询应返回：
| Customers |
| -- |
| Henry     |
| Max       |

```SQL
SELECT name AS Customers
FROM Customers
LEFT JOIN Orders 
ON Customers.Id = Orders.CustomerId
WHERE Orders.customerID IS NULL;
```







### ORDER & UPDATE

#### [1873. 计算特殊奖金](https://leetcode.cn/problems/calculate-special-bonus/)

表: `Employees`

| 列名        | 类型     |
|--|--|
| employee_id | int     |
| name        | varchar |
| salary      | int     |

写出一个SQL 查询语句，计算每个雇员的奖金。如果一个雇员的id是奇数并且他的名字不是以'M'开头，那么他的奖金是他工资的100%，否则奖金为0。

返回的结果集请按照`employee_id`排序。

```SQL
SELECT employee_id, 
IF(employee_id%2=1 AND LEFT(name, 1) != 'M', salary, 0) AS Bonus
FROM Employees;
```





#### [627. 变更性别](https://leetcode.cn/problems/swap-salary/)

`Salary` 表：

| Column Name | Type     |
| -- | -- |
| id          | int      |
| name        | varchar  |
| sex         | ENUM     |
| salary      | int      |

id 是这个表的主键。
sex 这一列的值是 ENUM 类型，只能从 ('m', 'f') 中取。
本表包含公司雇员的信息。

请你编写一个 SQL 查询来交换所有的 'f' 和 'm' （即，将所有 'f' 变为 'm' ，反之亦然），仅使用 单个 update 语句 ，且不产生中间临时表。

注意，你必须仅使用一条 update 语句，且 不能 使用 select 语句。

solution 1:

```SQL
UPDATE Salary
SET 
	sex = CASE sex 
		WHEN 'm' THEN 'f'
		ELSE 'm'
	END;
```

solution 2:

```SQL
UPDATE Salary
SET sex = CHAR(ASCII('m') + ASCII('f') - ASCII(sex));
```



#### [196. 删除重复的电子邮箱](https://leetcode.cn/problems/delete-duplicate-emails/)

表: `Person`

| Column Name | Type    |
| -- | -- |
| id          | int     |
| email       | varchar |

编写一个 SQL 删除语句来 删除 所有重复的电子邮件，只保留一个id最小的唯一电子邮件。

以 任意顺序 返回结果表。 （注意： 仅需要写删除语句，将自动对剩余结果进行查询）

```SQL
DELETE P2 
FROM Person p1, Person p2
WHERE  P1.ID < P2.ID AND P1.EMAIL = P2.EMAIL;
```





### STRING & REGEX

#### [1667. 修复表中的名字](https://leetcode.cn/problems/fix-names-in-a-table/)

表： `Users`

| Column Name    | Type    |
|-- | -- |
| user_id        | int     |
| name           | varchar |

编写一个 SQL 查询来修复名字，使得只有第一个字符是大写的，其余都是小写的。

返回按 `user_id` 排序的结果表。

```SQL
SELECT user_id, 
CONCAT(UCASE(LEFT(NAME, 1)), LCASE(STRING(NAME, 2))) AS name
FROM Users
ORDER BY user_id;
```





#### [1484. 按日期分组销售产品](https://leetcode.cn/problems/group-sold-products-by-the-date/)

表 `Activities`：

| 列名         | 类型    |
| -- | -- |
| sell_date   | date    |
| product     | varchar |

编写一个 SQL 查询来查找每个日期、销售的不同产品的数量及其名称。
每个日期的销售产品名称应按词典序排列。
返回按 sell_date 排序的结果表。

```SQL
SELECT sell_date,
	COUNT(DISTINCT PRODUCT) num_sold,
	GROUP_CONCAT(DISTINCT PRODUCT) products
FROM Activities
ORDER BY sell_date
GROUP BY sell_date;
```





#### [1527. 患某种疾病的患者](https://leetcode.cn/problems/patients-with-a-condition/)

患者信息表： `Patients`

| Column Name  | Type    |
|-- | -- |
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |

写一条 SQL 语句，查询患有 I 类糖尿病的患者 ID （patient_id）、患者姓名（patient_name）以及其患有的所有疾病代码（conditions）。I 类糖尿病的代码总是包含前缀 DIAB1 。

```sqL
SELECT * FROM PATIENTS
WHERE CONDITIONS REGEXP '^DIAB1|\\sDIAB1'
```





### GROUP & UNION SELECT

#### [1965. 丢失信息的雇员](https://leetcode.cn/problems/employees-with-missing-information/)

表: `Employees`

| Column Name | Type    |
| -- | -- |
| employee_id | int     |
| name        | varchar |

表: `Salaries`

| Column Name | Type    |
| -- | -- |
| employee_id | int     |
| salary      | int     |

写出一个查询语句，找到所有 丢失信息 的雇员id。当满足下面一个条件时，就被认为是雇员的信息丢失：

雇员的 姓名 丢失了，或者
雇员的 薪水信息 丢失了，或者
返回这些雇员的id  employee_id ， 从小到大排序 。

```SQL
# Write your MySQL query statement below
SELECT
    employee_id 
FROM 
    (
    SELECT employee_id FROM employees
    UNION ALL
    SELECT employee_id FROM salaries
) AS t
GROUP BY
    employee_id
HAVING
    count(employee_id) = 1
ORDER BY
    employee_id;
```





#### [1795. 每个产品在不同商店的价格](https://leetcode.cn/problems/rearrange-products-table/)

表：`Products`

| Column Name | Type    |
| -- | -- |
| product_id  | int     |
| store1      | int     |
| store2      | int     |
| store3      | int     |


每行存储了这一产品在不同商店store1, store2, store3的价格。
如果这一产品在商店里没有出售，则值将为null。

请你重构 Products 表，查询每个产品在不同商店的价格，使得输出的格式变为(product_id, store, price) 。如果这一产品在商店里没有出售，则不输出这一行。

```SQL
SELECT product_id AS `product_id`, 
'store1' AS `store`,
store1 AS `price` FROM Products
WHERE store1 IS NOT NULL

UNION ALL

SELECT product_id AS `product_id`, 
'store2' AS `store`,
store2 AS `price` FROM Products
WHERE store2 IS NOT NULL

UNION ALL

SELECT product_id AS `product_id`, 
'store3' AS `store`,
store3 AS `price` FROM Products
WHERE store3 IS NOT NULL;
```



#### [176. 第二高的薪水](https://leetcode.cn/problems/second-highest-salary/)

| Column Name | Type |
| -- | -- |
| id          | int  |
| salary      | int  |

编写一个 SQL 查询，获取并返回 `Employee` 表中第二高的薪水 。如果不存在第二高的薪水，查询应该返回 `null` 。

```sql
SELECT (
    SELECT DISTINCT salary AS `SecondHighestSalary`
    FROM Employee
    ORDER BY Salary DESC
    LIMIT 1 OFFSET 1)
AS `SecondHighestSalary`;
```



### MERGE

#### [175. 组合两个表](https://leetcode.cn/problems/combine-two-tables/)

表: `Person`

| 列名         | 类型     |
|-- | --| 
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |

表: `Address`

| 列名         | 类型    |
| -- | -- |
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |

编写一个SQL查询来报告 `Person` 表中每个人的姓、名、城市和州。如果 `personId` 的地址不在 `Address` 表中，则报告为空  `null` 。

```SQL
SELECT FIRSTNAME AS `firstName`,
LASTNAME AS `lastName`,
CITY AS `city`,
STATE AS `state`
FROM PERSON
LEFT JOIN ADDRESS ON PERSON.PERSONID = ADDRESS.PERSONID;
```





#### [1581. 进店却未进行过交易的顾客](https://leetcode.cn/problems/customer-who-visited-but-did-not-make-any-transactions/)

表：`Visits`

| Column Name | Type    |
|--| --| 
| visit_id    | int     |
| customer_id | int     |

表：`Transactions`

| Column Name    | Type    |
| -- | -- |
| transaction_id | int     |
| visit_id       | int     |
| amount         | int     |

有一些顾客可能光顾了购物中心但没有进行交易。请你编写一个 SQL 查询，来查找这些顾客的 ID ，以及他们只光顾不交易的次数。

返回以 任何顺序 排序的结果表。

```SQL
SELECT CUSTOMER_ID AS `customer_id`,
COUNT(CUSToMER_ID) AS `count_no_trans`
FROM Visits
LEFT JOIN TRANSACTION ON Visits.VISIT_ID = TRANSACTIONS.VISIT_ID
WHERE AMOUNT IS NULL
GROUP BY CUSTOMER_ID;
```





#### [1148. 文章浏览 I](https://leetcode.cn/problems/article-views-i/)

`Views` 表：

| Column Name   | Type    |
| -- | -- | 
| article_id    | int     |
| author_id     | int     |
| viewer_id     | int     |
| view_date     | date    |

此表无主键，因此可能会存在重复行。
此表的每一行都表示某人在某天浏览了某位作者的某篇文章。

请编写一条 SQL 查询以找出所有浏览过自己文章的作者，结果按照 id 升序排列。

```sql
SELECT DISTINCT AUTHOR_ID AS `id`
FROM VIEWS
WHERE AUTHOR_ID = VIEWER_ID
ORDER BY AUTHOR_ID ASC;
```



#### [197. 上升的温度](https://leetcode.cn/problems/rising-temperature/)

表： `Weather`

| Column Name   | Type    |
| -- | -- |
| id            | int     |
| recordDate    | date    |
| temperature   | int     |

编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 `id` 。

返回结果 **不要求顺序** 。

```SQL
SELECT B.ID
FROM WEATHER AS A
INNER JOIN WEATHER AS B
WHERE DATEDIFF(B.recordDate, A.recordDate) = 1
AND B.TEMPERATURE > A.TEMPERATURE
GROUP BY B.ID;
```





#### [607. 销售员](https://leetcode.cn/problems/sales-person/)

表: `SalesPerson`

| Column Name     | Type    |
| -- | -- |
| sales_id        | int     |
| name            | varchar |
| salary          | int     |
| commission_rate | int     |
| hire_date       | date    |



表: `Orders`
| Column Name     | Type    |
| -- | -- |
| order_id    | int  |
| order_date  | date |
| com_id      | int  |
| sales_id    | int  |
| amount      | int  |



表: `Company`
| Column Name     | Type    |
| -- | -- |
| com_id      | int     |
| name        | varchar |
| city        | varchar 

编写一个SQL查询，报告没有任何与名为 **“RED”** 的公司相关的订单的所有销售人员的姓名。

以 **任意顺序**

```sql
SELECT `name` FROM SALESPERSON
WHERE SALES_ID NOT IN (
    SELECT O.SALES_ID FROM ORDERS AS O
    LEFT JOIN COMPANY AS C ON C.COM_ID = O.COM_ID
    WHERE C.NAME = 'RED'
);
```





### FUNCTION

#### [1141. 查询近30天活跃用户数](https://leetcode.cn/problems/user-activity-for-the-past-30-days-i/)

活动记录表：`Activity`

| Column Name   | Type    |
| -- | -- |
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |

该表是用户在社交网站的活动记录。
该表没有主键，可能包含重复数据。
activity_type 字段为以下四种值 ('open_session', 'end_session', 'scroll_down', 'send_message')。
每个 session_id 只属于一个用户。

请写SQL查询出截至 `2019-07-27`（包含2019-07-27），近 `30` 天的每日活跃用户数（当天只要有一条活动记录，即为活跃用户）。

```sql
SELECT ACTIVITY_DATE AS `day`,
COUNT(DISTINCT USER_ID) AS `active_users`
FROM Activity
WHERE DATEDIFF('2019-07-27', activity_date) >= 0 AND DATEDIFF('2019-07-27', activity_date) < 30
GROUP BY activity_date;
```





#### [1693. 每天的领导和合伙人](https://leetcode.cn/problems/daily-leads-and-partners/)

表：`DailySales`

| Column Name | Type    |
| -- | -- |
| date_id     | date    |
| make_name   | varchar |
| lead_id     | int     |
| partner_id  | int     |

写一条 SQL 语句，使得对于每一个 `date_id` 和 `make_name`，返回**不同**的 `lead_id` 以及**不同**的 `partner_id` 的数量。

```SQL
SELECT date_id,
make_name,
COUNT(DISTINCT lead_id) AS `unique_leads`,
COUNT(DISTINCT partner_id) AS `unique_partners`
FROM DailySales
GROUP BY date_id, make_name;
```





#### [1729. 求关注者的数量](https://leetcode.cn/problems/find-followers-count/)

表： `Followers`

| Column Name | Type |
| -- | -- | 
| user_id     | int  |
| follower_id | int  |

写出 SQL 语句，对于每一个用户，返回该用户的关注者数量。

按 `user_id` 的顺序返回结果表。

```SQL
SELECT DISTINCT USER_ID AS `user_id`,
COUNT(*) AS `followers_count`
FROM FOLLOWERS
GROUP BY USER_ID
ORDER BY USER_ID;
```







## Keyword

### DENSE_RANK()

`DENSE_RANK()` is a window function in SQL that assigns a  rank to each distinct row within a result set, based on the values of  one or more specified columns. It differs from the traditional `RANK()` function by assigning the same rank to rows with the same values, causing no gaps in the ranking sequence.

![image-20230731171004430](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20230731171004430.png)

DENSE_RANK()是连续的

### RANK()

![image-20230731170939534](C:\Users\Gatsby\AppData\Roaming\Typora\typora-user-images\image-20230731170939534.png)

RANK()是会跳过相同的名次

