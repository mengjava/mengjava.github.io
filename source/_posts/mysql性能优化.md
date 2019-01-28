---
title: mysql
date: 2019-01-28 18:38:06
categories: 
- mysql性能优化
tags:
- mysql
- 索引
---

# 1.一般应急调优的思路：

```
1、show processlist

SHOW PROCESSLIST显示哪些线程正在运行

show processlist 详细介绍

2、查看执行计划 查看索引

3、通过执行计划判断，索引问题（有没有、合不合理）或者语句本身问题

4、show status  like '%lock%';    # 查询锁状态

　　kill SESSION_ID;   # 杀掉有问题的session
```

<!--more-->
# 2.常规调优思路：
针对业务周期性的卡顿，例如在每天10-11点业务特别慢，但是还能够使用，过了这段时间就好了。
```
1、查看slowlog，分析slowlog，分析出查询慢的语句。

慢查询具体操作

2、按照一定优先级，进行一个一个的排查所有慢语句。

3、分析top sql，进行explain调试，查看语句执行时间。

4、调整索引或语句本身。
```
# 3.什么情况下不走索引
```

1.正则表达式不使用索引,这应该很好理解,所以为什么在SQL中很难看到regexp关键字的原因 

 
2. 字符串与数字比较不使用索引; 
CREATE TABLE `a` (`a` char(10)); 
EXPLAIN SELECT * FROM `a` WHERE `a`="1" -- 走索引 
EXPLAIN SELECT * FROM `a` WHERE `a`=1 -- 不走索引 
 
select * from dept where dname='xxx' or loc='xx' or deptno=45 --如果条件中有or,即使其中有条件带索引也不会使用。换言之,就是要求使用的所有字段,都必须建立索引, 我们建议大家尽量避免使用or 关键字 
 

3. 如果mysql估计使用全表扫描要比使用索引快,则不使用索引


4.前导模糊查询不能使用索引
如:

select name from user where name like '%zhangsan'
非前导则可以:

select name from user where name like 'zhangsan%'
建议可以考虑使用 Lucene 等全文索引工具来代替频繁的模糊查询。


5.负向查询不能使用索引
select name from user where id not in (1,3,4);
应该修改为:

select name from user where id in (2,5,6);


6.在字段上进行计算不能命中索引

select name from user where FROM_UNIXTIME(create_time) < CURDATE();

应该修改为:

select name from user where create_time < FROM_UNIXTIME(CURDATE());


7.最左前缀问题

如果给 user 表中的 username pwd 字段创建了复合索引那么使用以下SQL 都是可以命中索引:

select username from user where username='zhangsan' and pwd ='axsedf1sd'

select username from user where pwd ='axsedf1sd' and username='zhangsan'

select username from user where username='zhangsan'

但是使用

select username from user where pwd ='axsedf1sd'

是不能命中索引的。


8. 如果明确知道只有一条记录返回

select name from user where username='zhangsan' limit 1
可以提高效率，可以让数据库停止游标移动。


9.不要让数据库帮我们做强制类型转换

select name from user where telno=18722222222
这样虽然可以查出数据，但是会导致全表扫描。
需要修改为

select name from user where telno='18722222222'


10.如果需要进行 join 的字段两表的字段类型要相同

不然也不会命中索引。

```
# 4.索引选择原则
```
1. 较频繁的作为查询条件的字段应该创建索引
2. 唯一性太差的字段不适合单独创建索引，即使频繁作为查询条件
3. 更新非常频繁的字段不适合创建索引
4. 不会出现在 WHERE 子句中的字段不该创建索引
5. 数据区分不明显的不建议创建索引
如 user 表中的性别字段，可以明显区分的才建议创建索引，如身份证等字段。
字段的默认值不要为 null
这样会带来和预期不一致的查询结果。
```