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































