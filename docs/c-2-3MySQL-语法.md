### 窗口函数

在日常工作中，经常会遇到需要**在每组内排名**，比如下面的业务需求：

> 排名问题：每个部门按业绩来排名
> topN问题：找出每个部门排名前N的员工进行奖励

用法：

```sql
<窗口函数> over (partition by <用于分组的列名>
                order by <用于排序的列名>)
```



例子：

```sql
select *,
   rank() over (order by 成绩 desc) as ranking,
   dense_rank() over (order by 成绩 desc) as dese_rank,
   row_number() over (order by 成绩 desc) as row_num
from 班级表
```

![img](../images/c-2-2-3MsSQL语法/v2-ad1d86f5a5b9f0ef684907b20b341099_720w.jpg)

rank()：同分并列，会占用排名

dense_rank()：同分并列，不会占用排名

row_num()：不考虑并列情况



### 行列互转

```
Products table:
+------------+--------+--------+--------+
| product_id | store1 | store2 | store3 |
+------------+--------+--------+--------+
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |
+------------+--------+--------+--------+
输出：
+------------+--------+-------+
| product_id | store  | price |
+------------+--------+-------+
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |
+------------+--------+-------+
```



#### 列转行

上表转下表为列转行

分别查询商店的所有商品价格，然后使用union求并集

```sql
select product_id,'store1' as store,store1 as price from products where store1 is not null 
union 
select product_id,'store2' as store,store2 as price from products where store2 is not null 
union
select product_id,'store3' as store,store3 as price from products where store3 is not null;
```



#### 行转列

- 由于筛选结果中每个商品是一个记录 因此GROUP BY product_id.
- 每个商店是一列，因此筛选每个商店时使用CASE [when..then..] END只取当前商店.
- 需要使用SUM()聚合函数 因为如果没有聚合函数 筛选出来的是GROUP BY、CASE...END之后的第一行



```sql
select product_id,
sum(case store when 'store1' then store else null end ) as store1,
sum(case store when 'store2' then store else null end ) as store2,
sum(case store when 'store3' then store else null end ) as store3
from products 
group by 
product_id;
```



### union和union all

union all:效率高，合并后直接返回，会有重复值

union all:效率低，合并后，会去除重复值，并按字段默认规则排序后返回



### 分组拼接函数

Group_Concat()

```sql
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



```sql
SELECT
	sell_date,
	count( DISTINCT product ) AS num_sold,
	GROUP_CONCAT( DISTINCT product ) AS products 
FROM
	Activities 
GROUP BY
	sell_date 
ORDER BY
	sell_date
```

