## MySQL



#### MyISAM和InnoDB的区别

MyISAM是5.5版本以前的默认引擎。后面版本引入了InnoDB

- 是否支持行级锁：MyISAM只支持表级锁，而InnoDB支持行级锁和表级锁，默认为行级锁
- 是否支持事务和崩溃后的安全恢复：MyISAM强调的是性能，每次查询都具有原子性，其执行速度比InnoDB快,但不支持事务
- 是否支持外键:MyISAM不支持，InnoDB支持
- 是否支持MVCC：InnoDB支持，MVCC可以使用乐观锁和悲观锁来实现。MVCC仅在Read Committed和Repeatable Read两个隔离级别下工作



#### 索引

- MyISAM：B+Tree叶子节点的data域存放的是数据记录的地址。在检索时，首先按照B+Tree搜索算法，搜索索引，如果存在，取出data域的值，然后以data域的值为地址读取相应的数据记录。被称为："非聚簇索引"
- InnoDB:数据本身就是索引结构。B+Tree叶子节点的data域存放的是完整的数据记录。这个索引的key是数据表的主键，称为聚簇索引。其余的索引称为辅助索引



#### 事务

事务：一组操作，要么都执行，要么都不执行



##### 四大特性

- 原子性：事务是最小的
- 一致性：事务执行前后，数据保持一致，多个事务对同一数据读取的结果都是相同的
- 隔离性：并发访问数据库时，一个事务不会被其他事务干扰。
- 持久性：一个事务提交后，对数据库的改变是永久的。



#### 带来的问题

- 脏读：
- 丢失更改：
- 不可重复读：
- 幻读：



#### SQL优化：

1. 避免全表扫描，考虑在 where 及 order by 涉及的列上建立索引。
2. 避免在 where 子句中使用!=或<>操作符、or、in/not in
3. 避免在where对字段进行Null值判断
4. 避免前后都模糊匹配like "%adb%"
5. 避免在 where 子句中对字段进行表达式操作
6. 避免在where子句中对字段进行函数操作
7. 不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算
8. .很多时候用 exists 代替 in 是一个好的选择
9. 索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率，因为 insert 或 update 时有可能会重建索引，所以怎样建索引需要慎重考虑
10. 任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段

#### 清空数据库SQL 

SELECT CONCAT('truncate table ',TABLE_NAME,';') AS a FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'db_member' ;