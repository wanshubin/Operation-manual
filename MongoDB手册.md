# 组织关系

+   数据库 database
+   集合 collection
+   文档 document

在 MongoDB 中，数据库和集合都不需要手动创建，当我们创建文档时，如果文档所在的集合或数据库不存在会自动创建数据库和集合



# 基本命令

## show dbs	查看所有数据库

显示当前所有的数据库

## use 进入/创建数据库

use abc 将会进入 abc 数据库，如果不存在则会先创建它再进入

## db	查看当前数据库

db表示当前所处的数据库

## show collections	查看所有的集合

显示数据库中所有的集合







# CRUD 操作

## 创建

### 显式创建集合

尽管集合可以自动创建，但是也可以手动创建

```
db.<collection>.insert()
```

### 插入文档

#### 单条插入

```sql
db.<collection>.insert(doc)
db.<collection>.insertOne(doc)
db.<collection>.save(doc)
```

列如插入一条学生信息

```sql
user={
    _id:"1",
    name:"张三",
    age:18,
    score:95,
    birthday:"2021-08-16"
}
db.student.insertOne(user)
```

**save 方法当 id 不存在时为插入，id 存在时为更新**

#### 批量插入

```sql
db.<collection>.insert([doc1,doc2,...])
db.<collection>.insertMany([doc1,doc2,...])
db.<collection>.save([doc1,doc2,...])
```





## 查询

### 查询文档

```sql
db.<collection>.find()
```

例如查询学生集合中的所有文档

```sql
db.student.find()
```



## 更新

### 更新文档

通过 update 函数可以更新集合中的文档

**特别注意：更新文档是更新整个文档的操做，如果修改的值只有部分属性，则在修改后剩余的属性将会被删除！**

```sql
db.<collection>.update(<query>, update, options)
```

+   query —— update 的查询条件，类似于 SQL 中的 where
+   update —— update 的对象和一些更新的操作符，类似于 SQL 中的 set
+   upsert —— 可选项，如果不存在 update 的文档，是否插入该文档，true 为插入，false 为不插入
+   multi —— 可选，是否批量更新，true 表示 按条件查询出的多条记录全部更新，false 只更新找到的第一条记录



也可以通过 save 函数更新

```sql
db.<collection>.save([doc1,doc2,...])
```



## 删除

### 删除集合

```sql
db.<collection>.drop()
```



### 删除文档

#### 删除单条

使用 remove 或 deleteOne 函数来删除集合中的记录

```sql
db.<collection>.remove(<query>, {justOne:<boolean>})
db.<collection>.deleteOne(<query>)
```

+   query —— 可选，删除文档的条件
+   justOne —— 可选，如果设为 true，则只删除一个文档，相当于 deleteOne，false 删除所有匹配的数据，相当于 deleteMany

例如

```sql
db.student.deleteOne({name:"张三"})
```



## 批量删除

使用 remove 或 deleteMany 函数来删除集合中的记录

```sql
db.<collection>.remove(<query>, {justOne:<boolean>})
db.<collection>.deleteMany(<query>)
```

例如

```sql
db.student.deleteMany({name:"张三"})
```



# 比较运算

ne、gt、lt、gte、lte

例如

```sql
db.student.find({id:{"$gt":3, "lte":4},age:20})
```



# 逻辑运算

使用 $not 取反



# 投影

属性设置为 1 为显示，为 0 则不显示

```sql
db.student.find({age:20},{_id:0,name:1,age:1})
```



# 排序

使用 sort 方法进行排序，属性设置为 1 为正序，-1 为倒序

```sql
db.student.find().sort({name:1, age:-1})
```



# 分页

limit 表示取 document 的数量，skip 表示跳过 document 的数量

分页公式 : 

```
db.<collection>.find().skip((pageNum -1) * pageSize).limit(pageSize)
```



# 统计

```
db.student.find().count(_id:{$gt:3})
db.student.find({age:20}).count()
```

