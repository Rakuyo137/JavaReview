# MySQL

## SQL

###  [595. 大的国家](https://leetcode.cn/problems/big-countries/)

SQL架构

`World` 表：

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | int     |
+-------------+---------+
name 是这张表的主键。
这张表的每一行提供：国家名称、所属大陆、面积、人口和 GDP 值。
```

 

如果一个国家满足下述两个条件之一，则认为该国是 **大国** ：

- 面积至少为 300 万平方公里（即，`3000000 km2`），或者
- 人口至少为 2500 万（即 `25000000`）

编写一个 SQL 查询以报告 **大国** 的国家名称、人口和面积。

按 **任意顺序** 返回结果表。

查询结果格式如下例所示。 

**示例：**

```
输入：
World 表：
+-------------+-----------+---------+------------+--------------+
| name        | continent | area    | population | gdp          |
+-------------+-----------+---------+------------+--------------+
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
+-------------+-----------+---------+------------+--------------+
输出：
+-------------+------------+---------+
| name        | population | area    |
+-------------+------------+---------+
| Afghanistan | 25500100   | 652230  |
| Algeria     | 37100000   | 2381741 |
+-------------+------------+---------+
```



#### 题解

\# Write your MySQL query statement below

##### 方法1:(or)

select name, population, area 

from world 

where area >= 3000000 or population >= 25000000;

##### 方法2:(union)

select name, population, area 

from world 

where area >= 3000000 

union 

select name, population, area

from world 

where population >= 25000000;

**union**

对于单列来说，用or是没有任何问题的，但是or涉及到多个列的时候，每次select只能选取一个index，如果选择了area，population就需要进行table-scan，即全部扫描一遍，但是使用union就可以解决这个问题，分别使用area和population上面的index进行查询。 但是这里还会有一个问题就是，UNION会对结果进行排序去重，可能会降低一些performance，所以最佳的选择应该是两种方法都进行尝试比较。



### [1484. 按日期分组销售产品](https://leetcode.cn/problems/group-sold-products-by-the-date/)

SQL架构

表 `Activities`：

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| sell_date   | date    |
| product     | varchar |
+-------------+---------+
此表没有主键，它可能包含重复项。
此表的每一行都包含产品名称和在市场上销售的日期。
```

 

编写一个 SQL 查询来查找每个日期、销售的不同产品的数量及其名称。
每个日期的销售产品名称应按词典序排列。
返回按 `sell_date` 排序的结果表。
查询结果格式如下例所示。

 

**示例 1:**

```
输入：
Activities 表：
+------------+-------------+
| sell_date  | product     |
+------------+-------------+
| 2020-05-30 | Headphone   |
| 2020-06-01 | Pencil      |
| 2020-06-02 | Mask        |
| 2020-05-30 | Basketball  |
| 2020-06-01 | Bible       |
| 2020-06-02 | Mask        |
| 2020-05-30 | T-Shirt     |
+------------+-------------+
输出：
+------------+----------+------------------------------+
| sell_date  | num_sold | products                     |
+------------+----------+------------------------------+
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |
+------------+----------+------------------------------+
解释：
对于2020-05-30，出售的物品是 (Headphone, Basketball, T-shirt)，按词典序排列，并用逗号 ',' 分隔。
对于2020-06-01，出售的物品是 (Pencil, Bible)，按词典序排列，并用逗号分隔。
对于2020-06-02，出售的物品是 (Mask)，只需返回该物品名。
```

#### **题解**

mysql 分组拼接函数 group_concat()

思路和心得：

1.分组

2.排序

3.组内拼接
排序，分隔符

**group_concat**

group_concat([DISTINCT] 要连接的字段 [Order BY ASC/DESC 排序字段] [Separator '分隔符']) 

```
# Write your MySQL query statement below
SELECT sell_date AS 'sell_date',
    COUNT(DISTINCT product) AS 'num_sold',
    GROUP_CONCAT(DISTINCT product 
        ORDER BY product ASC    #按照字典序排列，升序
        SEPARATOR ',')          #用','分隔
        AS 'products'    #组内拼接
FROM Activities
GROUP BY sell_date
ORDER BY sell_date;
```

### [584. 寻找用户推荐人](https://leetcode.cn/problems/find-customer-referee/)

难度简单89

SQL架构

给定表 `customer` ，里面保存了所有客户信息和他们的推荐人。

```
+------+------+-----------+
| id   | name | referee_id|
+------+------+-----------+
|    1 | Will |      NULL |
|    2 | Jane |      NULL |
|    3 | Alex |         2 |
|    4 | Bill |      NULL |
|    5 | Zack |         1 |
|    6 | Mark |         2 |
+------+------+-----------+
```

写一个查询语句，返回一个客户列表，列表中客户的推荐人的编号都 **不是** 2。

对于上面的示例数据，结果为：

```
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
```

#### **题解**

```
SELECT name FROM customer WHERE referee_id != 2 OR referee_id IS NULL;
```

MySQL 使用三值逻辑 —— TRUE, FALSE 和 UNKNOWN。任何与 NULL 值进行的比较都会与第三种值 UNKNOWN 做比较。这个“任何值”包括 NULL 本身！这就是为什么 MySQL 提供 IS NULL 和 IS NOT NULL 两种操作来对 NULL 特殊判断。

因此，在 WHERE 语句中我们需要做一个额外的条件判断 `referee_id IS NULL'。

### [183. 从不订购的客户](https://leetcode.cn/problems/customers-who-never-order/)

SQL架构

某网站包含两个表，`Customers` 表和 `Orders` 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

`Customers` 表：

```
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

`Orders` 表：

```
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

例如给定上述表格，你的查询应返回：

```
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

#### **题解**

##### 方法1：使用子查询和 NOT IN 子句

算法

如果我们有一份曾经订购过的客户名单，就很容易知道谁从未订购过。

我们可以使用下面的代码来获得这样的列表。


select customerid from orders;
然后，我们可以使用 NOT IN 查询不在此列表中的客户。

MySQL

```
select customers.name as 'Customers'
from customers
where customers.id not in
(
    select customerid from orders
);
```

#####  方法2（左连接）

根据左连接和右连接的原理：

左连接：返回左表所有数据，右表不存在时为NULL。
右连接：返回右表所有数据，左表不存在时为NULL。
这种问题就适合使用左连接/右连接来解决，显然，这题需要使用左连接。首先在子查询（临时表）中使用左连接查询出结果，然后使用IS NULL筛选出想要的内容，最终从临时表中取出数据。

```
select name as Customers 
from Customers as Customers
left join Orders as Orders
on Customers.Id = Orders.CustomerId
where Orders.CustomerId is NULL;
```

![6.jpg](https://pic.leetcode-cn.com/2f5ff13e22b0494e19d327562d970016bbaa88569c0590fa86f9c7dde947bc71-6.jpg) 





## 索引

###  一. 索引  排好序的数据结构

B+ tree     hash

聚集索引(InnoDB) :节点页只包含了索引列，叶子页包含了行的全部数据。

非聚集索引(MyISAM):叶子节点保存的不是指行的物理位置的指针

 

为什么建议InnoDB表建主键,且推荐整形自增主键?

主键:数据库底层自建RowId隐藏列,来维护内联数据,浪费数据库资源性能.

整型:整型查找数据过程较快,且节省存储空间

自增:B+tree节点分裂概率低,效率较高

 

![img](file:///C:\Users\chen\AppData\Local\Temp\ksohtml19932\wps1.jpg) 

 ![img](file:///C:\Users\chen\AppData\Local\Temp\ksohtml19932\wps2.jpg) 

![img](file:///C:\Users\chen\AppData\Local\Temp\ksohtml19932\wps3.jpg) 

 

联合索引

底层数据结构

索引最左前缀原则,从最左边为起点开始连续匹配，遇到范围查询（

<、>、between、like）会停止匹配。 不能跳过左起的字段