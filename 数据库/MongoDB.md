# MongoDB

## 简介

Nosql数据库

NoSQL数据库的概念在处理大量数据的互联网巨头 (例如Google，Facebook，Amazon等)中变得很流行。使用RDBMS处理海量数据时，系统响应时间变慢。
为了解决此问题，当然可以通过升级现有硬件来“横向扩展”我们的系统。但这个成本很高这个问题的替代方案是在负载增加时将数据库负载分配到多个主机上。这种方法称为“横向扩展”。
纵向扩展：提升单机配置，抗住负载
横向扩展：分散负载到多机

### 结构

| Mysql               | MongoDB                             |
| ------------------- | ----------------------------------- |
| 数据库              | 数据库                              |
| 表-table            | 集合-collection                     |
| 数据行-row          | 文档-document                       |
| 列-column           | 字段/域-field                       |
| 索引-index          | index                               |
| table joins  表连接 | 表连接， 不支持                     |
| primary key 主键    | primary key 主键（自动_id作为主键） |
|                     |                                     |

mongodb，是以一行行bson，作为文档数据行

### 安装

#### windows

```

```



#### ubuntu

```bash
# tgz版本安装

mkdir -P /data /data/db
cd /data
# 下载
curl -0 https://fastdl.mongodb.org/linux/mongodb-linux-x86 64-rhel70-4.2.22.tgz
# 解压
tar -xvf mongodb-linux-x86 64-rhel70-4.2.22.tgz
# 环境变量
export PATH=$PATH:/data/mongodb-linux-x86_64-rhel70-4.2.22/bin
# 设置数据文件、日志文件存储的路径
mongod --dbpath /data/db --port 27017 --logpath /data/db/mongod.log --fork
```



```bash
sudo apt-get install gnupg curl


curl -fsSL https://pgp.mongodb.com/server-6.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg \
   --dearmor
```



```bash
# 后台启动mongodb
sudo systemctl start mongod

# 如果出现这个错误 ，先运行这个
# Failed to start mongod.service: Unit mongod.service not found.

sudo systemctl daemon-reload
```

```bash
sudo systemctl start mongod
# 查看mongod状态
sudo systemctl status mongod

sudo systemctl enable mongod
sudo systemctl stop mongod
sudo systemctl restart mongod
```



#### docker（未完成）

```

```

## CURD

### 创建与删除库

1.创建数据库

```
use 数据库名字
```

如果数据库不存在，则创建数据库，否则切换到指定数据库。



2.查看所有数据库

```
show databases
show dbs
```

向数据库中插入一些数据之后，数据库才会真正创建

默认的数据库为 test



3.删除集合

```bash
db.集合名.drop()
```

4.删除数据库

```bash
# 切换到要删除的数据库
use runoob
# 删除
db.dropDatabase()
```

### 集合操作

```bash
# 创建一个集合(表)
db.createCollection(name)
# 隐式创建
db.集合名字.insert(数据)
```

```bash
# 展示所有集合
show collections
show tables
```

```bash
# 删除集合
db.集合.drop()
```



### 插入insert

```bash
# 将一个或多个文档插入到集合中。(json格式数据)
db.COLLECTION_NAME.insert(document)  
# 将单个文档插入集合中。
db.collection.insertOne() 
# 将多个 文档插入到集合中。
db.collection.insertMany() 
```



### 更新update replace

```bash
db.collection.update()
db.collection.updateOne() # 修改一条
db.collection.updateMany() 
db.collection.replaceOne()  # 替换一条 整个文档
```

```bash
db.collection.update(
   <filter>,  # 过滤条件,和find方法用法相同
   <update>,  # 要应用的修改
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ],
     hint:  <document|string>        // Available starting in MongoDB 4.2.1
   }
)
```

```bash
# 覆盖修改，改变整个document
db.mongo.updateOne({likes:10}, {likes:100})
# 但是报下面这个错误
# 无法修改_id的值，也无法把一个不同的_id来替换文档，所以报错

# 所以改用replaceOne() # 替换整个document
```

![image-20231228205217070](E:\笔记\数据库\MongoDB.assets\image-20231228205217070.png)

```bash
# 局部修改
# 使用修改器$set
# 把likes==10的数据修改为......
db.mongo.updateOne({likes:10}, {$set:{likes:100}});
```

```bash
# update 默认修改一条
db.mongo.update({likes:110}, {$set:{likes:100}});
# 修改多条
db.mongo.update({likes:110}, {$set:{likes:100}}, {multi: true});
```

```bash
db.mongo.updateOne()  # 一条
db.mongo.updateMany()  # 多条
```

```bash
# 对个字段 原有值 进行增加/减少    $inc 运算符
# 对likes字段增加1
db.mongo.updateMany({likes:110}, {$inc:{likes:1}});
```



```bash
# 要替换**_id**字段以外的文档的全部内容，请将一个全新的文档作为第二个参数传递给
db.inventory.replaceOne(
   {条件},
   {替换为的数据}
)
```

#### 聚合管道更新（未完成）

```
？？？？

```



### 删除remove/delete

```bash
# 快要弃用
# 删除符合条件的所有document
db.collection.remove({条件})
# 删除所有document
db.collection.remove()
```

```bash
# 现在用这些
db.collection.deleteOne()
db.collection.deleteMany()
```





### 基本查找

#### 查询

```bash
# db.collection.find({查询语句}, {指定返回的字段})
# 查找collection中的所有document
db.collection.find()
db.collection.findOne()  # 返回第一条
```

#### 影射

```bash
# 投影查询，筛选返回的字段
# 例：
db.mongo.find({likes:10}, {title:1,by:1})
# 查询likes==10的所有数据，只提取title和by字段。默认_id会显示


# 如果size字段的值还有嵌套，例如：
 { 
 item: "journal", 
 status: "A", 
 size: { h: 14, w: 21, uom: "cm" }, 
 instock: [ { warehouse: "A", qty: 5 } ] 
 }
#想要影射size里面的内容可以这样使用，主要要加双引号
db.mongo.find({status:"A"}, {item:1, status:1, "size.h":1})
```

#### 条件查询

等值查询

```bash
# 传入一个或多个键值对{ <field1>: <value1>, ... }
db.collection.find( { status: "D" } )
```

$in  $nin

```bash
# 查询status字段的值，匹配$in内部的值
# 即 status="A" 或 status="D"
db.collection.find( { status: { $in: [ "A", "D" ] } } )
```

多个查询字段，逗号隔开

```bash
db.mongo.find({
"likes":{$in: [10, 100]}, 
"by":{$in: ["MongoDB", "MongoDB中文网"]},
})
```



### 嵌套文档的查询

```bash
# 查询嵌套字段,例如size，需要完全准确，每个字段都需要，并且顺序要一致
db.mongo.find({
    status: "A", 
    size:{h: 10,
          w: 15.25,
          uom: "cm"}
})
```

```bash
# 例如这样的数据
db.mongo.insertMany( [
   { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
   { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
   { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
   { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);
```

```bash
# 有效 
db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } )
# 无效，因为qty和warehouse顺序不对
db.inventory.find( { "instock": { qty: 5, warehouse: "A" } } )
```

```bash
# 查询instock里面qty小于20的
db.inventory.find( { 'instock.qty': { $lte: 20 } } )
# 针对数组类型的，可以使用数组下标
# 查询instock里面第0个元素 qty小于20的
db.inventory.find( { 'instock.0.qty': { $lte: 20 } } )
```



## 操作符

### 比较操作符$lt

```bash
# $lt 小于
db.mongo.find({"likes":{$gt: 50}, })
```

| Name                                                         | Description           |
| :----------------------------------------------------------- | :-------------------- |
| [`$eq`](https://www.mongodb.com/docs/v4.0/reference/operator/query/eq/#op._S_eq) | 等于                  |
| [`$gt`](https://www.mongodb.com/docs/v4.0/reference/operator/query/gt/#op._S_gt) | 大于                  |
| [`$gte`](https://www.mongodb.com/docs/v4.0/reference/operator/query/gte/#op._S_gte) | 大于等于              |
| [`$in`](https://www.mongodb.com/docs/v4.0/reference/operator/query/in/#op._S_in) | 映射，在某个范围内    |
| [`$lt`](https://www.mongodb.com/docs/v4.0/reference/operator/query/lt/#op._S_lt) | 小于                  |
| [`$lte`](https://www.mongodb.com/docs/v4.0/reference/operator/query/lte/#op._S_lte) | 小于等于              |
| [`$ne`](https://www.mongodb.com/docs/v4.0/reference/operator/query/ne/#op._S_ne) | 不等于                |
| [`$nin`](https://www.mongodb.com/docs/v4.0/reference/operator/query/nin/#op._S_nin) | 映射，不 在某个范围内 |



### 逻辑操作符$and

```bash
db.mongo.find({
    $and:[
         {"likes":{$in: [10, 100]}, 
        "by":{$eq: "MongoDB中文网"}}
        ]
})


db.mongo.find({
    $比较运算符:[   # 注意大括号
         {"likes":{$in: [10, 100]}, 
        "by":{$eq: "MongoDB中文网"}}
        ]
})

db.test01.find({
    $and:[
    	{"item":{$eq: "paper"}, 
        "instock.0.warehouse":{$eq: "B"}
        }]
})
```



| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`$and`](https://www.mongodb.com/docs/v4.0/reference/operator/query/and/#op._S_and) | Joins query clauses with a logical `AND` returns all documents that match the conditions of both clauses. |
| [`$not`](https://www.mongodb.com/docs/v4.0/reference/operator/query/not/#op._S_not) | Inverts the effect of a query expression and returns documents that do *not* match the query expression. |
| [`$nor`](https://www.mongodb.com/docs/v4.0/reference/operator/query/nor/#op._S_nor) | Joins query clauses with a logical `NOR` returns all documents that fail to match both clauses. |
| [`$or`](https://www.mongodb.com/docs/v4.0/reference/operator/query/or/#op._S_or) | Joins query clauses with a logical `OR` returns all documents that match the conditions of either clause. |

### 切片操作符$Slice 



```

```

### 多条件查询操作符$elemMatch 

```bash
db.mongo.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } )
# 等同于
db.mongo.find( { "instock.qty": 5, "instock.warehouse": "A" } )
```

```bash
db.mongo.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } )
# 等同于
db.mongo.find( { "instock.qty": { $gt: 10,  $lte: 20 } } )
```

### $all操作符

```python
# 案例document 
db.test01.insertOne({ 
    item: "journal", 
    qty: 25, 
    tags: ["blank", "red"], 
    dim_cm: [ 14, 21 ] 
})
```

```bash
# 只能精确查找嵌套字段tags，元素的数量、顺序都不能有一丝一毫不同
db.mongo.find( { tags: ["red", "blank"] } )
```

```bash
# $all操作符
# 不在乎元素顺序-数组中也可以有其它元素-
db.mongo.find( { tags: { $all: ["red", "blank"] } } )
```

### $size

```bash
# 条件：tags的长度是3
db.inventory.find( { "tags": { $size: 3 } } )
```

## 查询进阶

### 统计查询

```bash
db.mongo.countDocuments({qty: 75})
db.mongo.count({条件}) # 弃用了
#返回 结果个数
```

### 分页查询

```bash
# limit(数字)    限制返回个数
db.mongo.find({条件}).limit(3)

# skip(数字)     跳过前几个结果,默认0
db.mongo.find({条件}).skip(3)  # 前3个不返回

# 分页:每页n条，第p页
db.mongo.find({条件}).limit(n).skip((p-1)*n)
# 分页:每页20条，第5页
db.mongo.find({条件}).limit(20).skip((5-1)*20)
```

### 排序查询

```bash
# .sort(排序方式)     1:升序   -1:降序
db.mongo.find().sort({qty:1})
# 可以根据多个条件排序，字段顺序从前往后
db.mongo.find().sort({qty:1, "dim_cm.0":-1})
```

```bash
sort()  skip()  limit()
# 这三个同时出现的时候，顺序执行优先级
# 先sort()——>再skip()——>最后limit()
# 和所在的位置无关
```

### 正则查询(未完成)

```

```

