[TOC]

# 查询

## 语句顺序

顺序不能更改

```sql
select *
from customers c
left join order o on o.id = c.id
where c.id=1
order by c.firstname
limit 2，5
```



## AS	取别名

取10倍的的价格赋一个新的名字

```sql
select price*10 as new_price
from products
where id=1
```



## DISTINCT	查找项目单一的结果

多个 state 相同时，只会合并显示为一个

```sql
select distinct state
from customers
```



## NOT	否定条件

```sql
select *
from customers
where NOT (birth_date > '1990-01-01' or points > 1000)
```



## (NOT) IN	（不）在多个可能的条件中

查找在 VA、FL、GA 州之中的用户

```sql
select *
from customers
where state in ('VA','FL','GA');
```

查找不在这三个州的其他用户

```sql
select *
from customers
where state not in ('VA','FL','GA');
```



## BETWEEN AND	查找在给定范围之间的值

查找积分在1000到3000之间的用户，相当于 point>=1000 and point<=3000

```sql
select *
from customers
where points between 1000 and 3000;
```



## LIKE	模糊查询

查询住址含有 trail 或 avenue 的用户

```sql
select *
from customers
where address like '%trail%' or address like '%avenue%';

--  %  代表任意个字符
--  _  代表一个单字符
```



## REGEXP	使用正则表达式匹配

^	以某个字符开头

$	以某个字符结尾

|	多个条件相连，表示“或”

a[aki]r	表示含有aar、akr、air中的任意一个

[a-h]e	表示含有a-h中的任意一个和e的组合，如ae，be...he



## IS (NOT) NULL	值为（非）空

查找电话号码为为空的顾客

```sql
select *
from customers
where phone is null
```



## ORDER BY ... DESC/ASC	排序

mysql 中 oder by 语句可以对不是列名的项目的进行排序

选出id为2的商品，并且以总价降序展示

```sql
select *,quantity*unit_price as total_price
from order_items
where order_id=2
order by total_price desc
```



## LIMIT	控制查询结果的数量

使用 limit 可以控制查询结果条数，常用来做分页查询

**limit 2,5 表示跳过 前2条 记录，再查询 5条 记录**

```sql
select *
from customers
limit 2,5
```



## (INNER) JOIN	内连接

使用内连接来关联两张表，INNER 在书写时可以省略

### 跨表连接

在 order 表中通过 id属性 关联 customers

```sql
select order_id,orders.customer_id,first_name,last_name
from orders
join customers
    on orders.customer_id = customers.customer_id
```

### 跨数据库连接

通过 product_id 联合 sql_store 的order_items 和 sql_inventory 的 products

```sql
use sql_store;
select *
from order_items oi
join sql_inventory.products p
    on oi.product_id=p.product_id
```

或

```sql
select *
from sql_store.order_items oi
join sql_inventory.products p
    on oi.product_id=p.product_id
```

### 自连接

当一张表的一个属性要和自己的一个属性列连接时，就要用到自连接

比如一个员工表，一位员工的负责人是另一名员工，那么就要将两个属性相连起来

```sql
select e.employee_id,e.first_name,
       m.first_name as manager
from employees e
join employees m
    on e.reports_to=m.employee_id
```

### 多表连接

使用多个 join 连接多个表

```sql
select o.order_id,
       o.order_date,
       c.first_name,
       c.last_name,
       os.name as status
from orders o
join customers c
    on c.customer_id = o.customer_id
join order_statuses os
    on o.status = os.order_status_id
```

### 复合连接

当表中没有能唯一确定记录的键时，或者是有复合主键，就要使用复合连接

复合连接就是使用 and 来增加连接条件

```sql
select *
from order_items oi
join order_item_notes oin
    on oi.order_id = oin.order_Id
           and oi.product_id = oin.product_id
```

### 隐式连接语法

通常从多个表查询并使用where条件完成连接，但是**不建议使用**，尽量使用显示连接

```sql
select *
from orders o,customers c
where o.customer_id=c.customer_id
```



## (OUTER) JOIN	外连接

**OUTER 在书写时也是可有可无的**

### LEFT JOIN	左连接

左连接以查询表为主表，以查询表的每条记录为基准

如下查询，无论顾客是否有订单，都会查询出所有的顾客

```sql
select c.customer_id,
       c.first_name,
       o.order_id
from customers c 
left join orders o 
    on c.customer_id = o.customer_id
order by c.customer_id
```

### RIGHT JOIN	右连接

右连接以连接表为主表，以连接表的每条记录为基准，**尽量使用左连接，不使用右连接，否则容易混乱**

下面的查询只会查询到有订单的顾客

```sql
select c.customer_id,
       c.first_name,
       o.order_id
from customers c
right join orders o
    on c.customer_id = o.customer_id
order by c.customer_id
```

### 多表外连接

查询顾客表，结果无论是否有订单，无论订单是否有发货人

```sql
select c.customer_id,
       c.first_name,
       o.order_id,
       s.name as shipper
from customers c
left join orders o
    on c.customer_id = o.customer_id
left join shippers s
    on o.shipper_id = s.shipper_id
order by c.customer_id
```

### 自外连接

```sql
select e.employee_id,
       e.first_name,
       m.first_name as manager
from employees e
left join employees m
    on e.reports_to = m.employee_id;
```



## USING	更方便的连接关键字

当要连接的两个表的属性名相同时，就可以使用 USING 代替 ON

```sql
select *
from orders o
join customers c
    using (customer_id)
left join shippers sh
    using (shipper_id)
```

复合连接时

```sql
select *
from order_items oi
join order_item_notes oin
    using (order_id, product_id)
```



## NATURE JOIN	自然连接

让系统自己处理，不推荐使用



## CROSS JOIN	交叉连接

交叉连接即让连接表的每条记录都连接，也就是做笛卡尔积

隐式的交叉连接就是从多个表中查询而不做 on 条件限制

```sql
select *
from order_items oi, customers c
```



## UNION	合并多个查询的结果

合并多个查询的结果（多个查询的结果叠加在一起），它们的**返回的列数一定要是一样的**

```sql
select customer_id,
       first_name,
       points,
       'Bronze' as type
from customers
where points<2000
union
select customer_id,
       first_name,
       points,
       'Silver' as type
from customers
where points between 2000 and 3000
```



# 插入

## INSERT INTO	添加记录

## DEFAULT	默认值

自动递增或者是有默认值的字段可以使用 default 来自动赋值

### VALUES 直接填充数据

这种方式需要按位置填入所有数据

```mysql
insert into customers
values (
        default,
        'john',
        'smith',
        '1990-01-01',
        null,
        'address',
        'city',
        'CA',
        default
       )
```



### 声明字段后用 VALUES 填充数据

这种方式可以不用写 DEFAULT 或 NULL

```mysql
insert into customers(first_name,
                      last_name,
                      birth_date,
                      address,
                      city,
                      state)
values ('john',
        'smith',
        '1990-01-01',
        'address',
        'city',
        'CA')
```





# 聚合查询

## COUNT()	计数

以某个属性为基准，对行数进行计数，空值不会被计算在函数中，如果想要连空值的行一起计数，使用COUNT(*)

```sql
select count(*)
from students
where age>20;
```



## SUM()	和

该列必须为数值类型，且空值不会被计算在函数中

```sql
select SUM(age * 2)
from students;
```



## AVG()	平均数

该列必须为数值类型，且空值不会被计算在函数中

```sql
select AVG(age)
from students;
```



## MAX()	最大值

该列必须为数值类型，且空值不会被计算在函数中

```sql
select MAX(age)
from students;
```



## MIN()	最小值

 该列必须为数值类型，且空值不会被计算在函数中

```sql
select MIN(age)
from students;
```



## GROUP BY	分组聚合

将相同字段的值分为一组，注意这里 **SELECT 后不能查询没有加入到 GROUP BY 后面的字段**，如其他字段name、age等等，因为一个州可能有很多用户，这样查询是不允许的

```sql
select client_id,
       sum(invoice_total) as total_sales
from invoices
where invoice_date >='2019-07-01'
group by client_id
order by total_sales desc ;
```



## HAVING	分组聚合筛选

使用 HAVING 可以对 GROUP BY 查询出来的列进行筛选，使用 **HAVING 筛选的字段必须在 SELECT 中出现**

```sql
select client_id,
       sum(invoice_total) as total_sales,
       count(*) as number_of_invoices
from invoices
group by client_id
having total_sales > 500 and number_of_invoices > 5;
```



## WITH ROLLUP	分组求和

将一组的某个列的数值相加

```sql
select state,
       city,
       sum(invoice_total) as total_sales,
from invoices
join clients c 
	using(client_id)
group by state,city with rollup;
```



# 复杂查询

## 子查询

查询单价比3号物品的单价高的物品

```sql
select *
from products
where unit_price > (
    select unit_price
    from products
    where product_id = 3
);
```



## IN	是否存在

查询不在另一张表里的商品

```sql
select *
from products
where product_id not in(
    select distinct product_id
    from order_items
);
```



查询购买了商品3的用户信息

```sql
select customers.customer_id,
       first_name,
       last_name
from customers
left join orders o
    on customers.customer_id = o.customer_id
where order_id in (
    select order_id
    from order_items
    where product_id = 3
);
```



## ALL	提取所有结果

以下种方式的结果是一样的，使用 ALL 将所有结果提取出来一一对比

```sql
select *
from invoices
where invoice_total > (
    select max(invoice_total)
    from invoices
    where client_id=3
);
```

```sql
select *
from invoices
where invoice_total > all (
    select invoice_total
    from invoices
    where client_id=3
);
```



## ANY	任意一个

其作用与 IN 相同，配合等号使用，如下两种方式结果相同

```sql
select *
from clients
where client_id in (
    select client_id
    from invoices
    group by client_id
    having count(*) >=2
)
```

```sql
select *
from clients
where client_id = any (
    select client_id
    from invoices
    group by client_id
    having count(*) >=2
)
```



## 相关子查询/关联子查询

子查询中引用了外查询中的属性，相关子查询的资源代价比较大

下例是查找薪水比自己所在部门平均薪水高的员工，相关子查询使得每一位员工的子查询计算的平均薪水就是他所在部门的平均薪水

```sql
select *
from employees e
where salary > (
    select avg(salary)
    from employees
    where e.office_id=employees.office_id
);
```

下例是查找发票总额大于顾客自身所有发票平均总额的发票单，在查找每张发票时使用相关子查询计算这张发票的顾客的所有发票总额的平均值

```sql
select invoice_total
from invoices i
where invoice_total >(
    select avg(invoice_total)
    from invoices
    where client_id =i.client_id
);
```



## EXIST	符合条件

EXIST 会判断括号内的条件是否符合

下例对于客户表中的每一位客户，都会检查是否存在一条符合id相等条件的记录

```sql
select *
from clients c
where exists(
    select client_id
    from invoices
    where c.client_id=invoices.client_id
)
```

这是一段使用 IN 关键字的查询，他们的结果相同，区别是下段语句会执行子查询并把结果返回到where子句，相当于 select * from clients where client_id in (1,2,3,4)，这样一来如果有非常大的一张表这样查询会影响性能。

而 EXIST 并没有给外查询一个结果，而是返回一个指令，说明这个子查询中是否有符合这个搜索条件的行，当符合时返回 TRUE，EXIST 运算符就会在最终结果里添加当前记录

```sql
select *
from clients
where client_id in (
    select distinct client_id
    from invoices
);
```

使用 EXIST 查询没有被预定过的商品

```sql
select *
from products p
where not exists(
    select *
    from order_items
    where p.product_id=order_items.product_id
);
```



## SELECT子查询

值得注意的是，子查询可以用重新定义的列（别名）

```sql
select invoice_id,
       invoice_total,
       (select avg(invoice_total)
           from invoices) as invoicing_average,
       invoice_total - (select invoicing_average)
from invoices
```



## FROM子查询

可以使用这样的方式临时构造一个虚拟表，**在 FROM 中用到子查询时，必须要给子查询一个别名**，不管会不会用到子查询

相比于这种只限于解决简单查询的方式，更好的方式是使用视图

```sql
select *
from (
    select client_id,
        name,
        (select sum(invoice_total)
            from invoices
            where client_id=c.client_id) as total_sales,
        (select avg(invoice_total) from invoices) as average,
        (select total_sales - average) as diffence
    from clients c
) as sales_summary
where total_sales is not null;
```





# 事务

## 事务的特性	ACID

### 原子性

### 一致性

### 隔离性

### 持久性





## 并发问题

### 丢失更新	Lost Update

**如果两个事务修改同一行的不同属性，则可能这一条记录会被后一个提交的事物覆盖；**解决方法是使用事务上锁

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917103625147.png" alt="image-20210917103625147" style="zoom:67%;" />

### 脏读	Dirty Reads

**一个事务读取了尚未被提交的数据；**解决方法是使用 “读已提交” 的隔离级别，事务只能读取已提交的数据

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917111215140.png" alt="image-20210917111215140" style="zoom:67%;" />

如下图，事物A修改了数据但未提交，此时事务B就读取了数据并作出了某个决定

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917103800530.png" alt="image-20210917103800530" style="zoom:67%;" />

如下图，事物A回滚了修改，使得事物B读取了本来不应该存在的数据

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917103935894.png" alt="image-20210917103935894" style="zoom:67%;" />



### 不可重复读/不一致读	Non-repeating Reads

**读取了某个数据多次，并得到了不同的结果；**解决方法是要将多个事务隔离起来，确保数据更改对事务不可见，利用 “可重复读” 隔离级别来保证这一点，在如下案例中，可以保证第二次读取时会使用首次读取就创建的快照

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917105804288.png" alt="image-20210917105804288" style="zoom:67%;" />

如下图，在事务A执行过程中，事务B修改了数据，事务A得到了两次不同的结果

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917104410139.png" alt="image-20210917104410139" style="zoom:67%;" />

### 幻读	Phantom Reads

**在一个事物执行查询之后才被另一事物执行添加、更新或删除的数据无法被查询到；**解决方法是使用 “序列化” 隔离级别来保证有其他事务执行数据更改时当前事务能够知晓变动，如果其他事务修改了数据，则当前事务必须等待他们的修改的提交，这样一来就可以保证多个事物按序列化执行

序列化是我们可以应用于一个事物的最高隔离级别，它为我们的事务提供了最高确定性，它的弊端是我们拥有的用户和并发事务越多，我们就要等待越久，影响性能和可扩展性。所以我们应该只在真的有必要防止幻读的情况下才保留此项

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917105450392.png" alt="image-20210917105450392" style="zoom:67%;" />

如下图，A事务查询，但是在期间事务B对数据做出了修改，使得某条记录满足事务A查询的条件，但是这条记录却不会被事务A查询出来

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917105003524.png" alt="image-20210917105003524" style="zoom:67%;" />



## 事务隔离级别

mysql中的事务并发问题和隔离级别策略如下表，隔离级别越高，对系统的负担越重

mysql中的默认的事务隔离级别是 “可重复读取”

+   低隔离级别——更容易并发——高性能——更多并发问题
+   高隔离级别——减少并发——性能、可扩展性略低——更少的并发问题

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210917110102597.png" alt="image-20210917110102597" style="zoom:67%;" />

### 读未提交	Read Uncommitted

### 读已提交	Read Committed

### 可重复读	Repeatable Read	（默认）

### 序列化	Serializable





# 索引

索引是一种数据结构，它由两部分组成：一个字段或几个字段（复合索引可以有多列，但在mysql中，一个索引最多包含16列）和该字段对应的引用

使用索引查询快的原因是索引体积小，它可以放在内存中，相比从磁盘中读取数据就会更快

索引的缺点是它单独一列会增加数据库体积，而且多了更新索引这一步会影响正常操作的性能

**值得注意的是，应该针对查询来设计索引，而不是在设计表的时候就设计索引，索引的最终目标的增快查询速度**

主键会默认成为一级索引，每当创建一个二级索引，mysql会自动将主键包含到二级索引中



## 建立复合索引的顺序

+   把查询更频繁的列放在靠前的位置
+   把基数更大的列放在更靠前位置，基数大即按该条件查询到的唯一值个数大
+   一定要顾及查询本身，要考虑现实意义



## 设计索引

+   观察where子句的条件，看看最常用的列

+   观察order by子句中的列
+   观察select子句中使用的列



## 维护索引

+   在创建索引之前检查现有索引是否已经存在要创建的索引

![image-20210916224219810](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210916224219810.png)

+   在创建索引之前检查现有索引靠前的列是否已经包含要创建索引的列

![image-20210916224205038](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210916224205038.png)

