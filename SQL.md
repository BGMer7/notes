# SQL

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













































































































