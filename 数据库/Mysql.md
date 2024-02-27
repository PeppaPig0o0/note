# Mysql

```sql
show databases;
--注释 --双横杠
\s -- 查看数据库字符编码以及其它信息
\c -- 结束当前语句
exit -- 退出连接 
quit -- 退出连接

help -- 查看帮助信息
```

## 库表行—基本操作

### 库操作

库的增删改查

```sql
-- 1.增
create database <库名字>;
create database 库名字 [charset=utf8];  -- 默认utf8

-- 2.删
drop database <库名字>;

-- 3.改
-- 数据库名字无法改，一般只改编码
alter database 库名字 charset=utf8;

-- 4.查
show databases;  -- 查所有的库 
show create database 库名字;  -- 查看创建库的sql语句
```

```sql
-- 创建库，字符集
create database 库名字 [charset=utf8];  -- 默认utf8
create database 库名字 [default charset=utf8];
create database 库名字 [default character set utf8];

-- utf8mb4支持特殊字符例如表情包等等，尽量使用这个
create database 库名字 [default character set utf8mb4];
create database 库名字 [default character set utf8mb4] [default collate 排序规则];
-- 默认 utf8_general_ci和 utf8mb4_general_ci,一般不用手动设置
```

### 表操作

```sql
-- 选中数据库
user 库名字;
select database(); -- 查看当前所在数据库
```

表的增删改查

```sql
-- 1.增
create table 表名字(id int, name char);
create table 表名字(id int, name char) charset=utf8;  -- 编码默认和创建库的编码一致

-- 2.删
drop table 表名字;

-- 3.改
alter table 表名字 modify 字段 类型; -- 修改字段的数据类型
-- 例：alter table test modify name char(4);
alter table 表名字 modify 旧字段名 新字段名 类型; -- 修改字段的名字
-- 例：alter table test modify name Name char(8);

-- 4.查
show tables;  -- 查所有的表
show create table 表名字; -- 查看创建表的sql语句
describe 表名字; -- 查询表结构
desc 表名字;
```

```sql
-- 对所有表的操作，都可以使用绝对路径的方式：  库名.表名
-- 不切换数据库也能操作表
-- 例：create table db1.test(id int, name char);
```

### 行(记录)操作

```sql
-- 1.增
-- 插入一条
insert into 表名 values(1, '字符111');
-- 插入多条
insert into 表名 values(2, '字符111'),(3, '字符111'); -- 正确
insert into 表名 values((4, '字符111'),(5, '字符111')); -- 错误

-- 2.删
delete from 表名 where 条件=值 and 条件=值;

-- 3.改
update 表名 set 字段=值 where 条件=值 and 条件=值;

-- 4.查
select * from 表名; -- 查所有
select 字段,字段 from 表名; -- 查指定字段
```

```sql
-- 创建和删除库和表时候可以加上判断
-- 执行时不会报错
create database [if not exists] <库名字> [charset=utf8];
drop database [if exists] <库名字> ;
```



## SQL语言分类

| 类型 | 关键词                          | 描述                                           |
| ---- | ------------------------------- | ---------------------------------------------- |
| DDL  | create、alter、drop             | 数据库定义语言，用来定义和管理数据库或者数据表 |
| DML  | insert、update、delete          | 数据库操纵语言，用来操作数据                   |
| DQL  | select                          | 数据库查询语言，用来查询数据                   |
| DCL  | grant、revoke、commit、rollback | 数据库控制语言                                 |

## 表操作

### 存储引擎

存储引擎就是表的类型
不同的存储引擎，不同的处理机制

```sql
-- 查看所有的存储引擎
show engines;
-- 默认InnoDB
```

![image-20231229235344784](E:\笔记\数据库\Mysql.assets\image-20231229235344784.png)

```sql
-- 创建表时，可以指定引擎
-- innodb (5.6以后支持全文索引)(支持行级锁)
create table test(id int, name char) engine=innodb; -- 默认innodb
-- 5.5以前的默认索引(支持表级锁)
create table test(id int, name char) engine=myisam; -- 默认innodb
-- 数据存在内存中
create table test(id int, name char) engine=memory; -- 默认innodb
-- 黑洞，数据存入就会消失，一般测试性能使用
create table test(id int, name char) engine=innodb; -- 默认innodb
```

### 创建表语法

```sql
create table <表名>(
	<字段1> <字段类型>[(字段宽度)] [约束条件]，
	<字段2> <字段类型>[(字段宽度)] [约束条件1 约束条件2]，
	<字段3> <字段类型>[(字段宽度)]
) [engine=innodb] [charset=utf8];

-- 最后一个字段后面，不能有逗号
-- 字段名不能重复
-- 字段宽度：字符个数（不知字节长度）
```

### 修改表语法

```sql
-- 1.修改字段类型、约束条件等
alter table <表名> modify <字段名> <新字段类型>[(宽度)] [约束条件];
-- 修改字段名
alter table <表名> change <旧字段> <新字段名> <新字段类型>[(宽度)] [约束条件];

-- 2.增加字段
alter table <表名> add <字段名> <新字段类型>[(宽度)] [约束条件]; -- 默认新插入字段在最后
-- first插入到最前面，after <字段> 插入到某个字段后面
alter table <表名> add <字段名> <新字段类型>[(宽度)] [约束条件] [first|after <字段>];

-- 3.删除字段
alter table <表名> drop <字段名>;

-- 4.修改表名
alter table <表名> rename <新表名>;
```

### 复制表

```sql
-- 把一张表所有内容复制到新表中
create table <新表名> select * from <表名>;

-- 把一张表部分列复制到新表中
create table <新表名> select id,name from <表名> where 条件;

-- 仅复制表结构
create table <新表名> select id,name from <表名> where 条件为假;
create table <新表名> like <表名>;
```



## 数据类型

### 数值

主要有整型和浮点型

#### 整型

![image-20231230003722944](E:\笔记\数据库\Mysql.assets\image-20231230003722944.png)

*注意1：*

5.7开启严格模式以后，如果插入的数据超出范围，会报错。5.6及之前，如果插入超过范围，会改为正最大值和负最大值，tinyint 139->127  -155->-128

*注意2：*

整型的宽度是固定的，设置的宽度是显示宽度

#### 浮点型（未完成）

浮点数表示形式： 符号、尾数、基数、指数
$$
±m*2^e
$$


![image-20231230005223776](E:\笔记\数据库\Mysql.assets\image-20231230005223776.png)

### 字符类型

1.char 定长字符串

​	char(10) 最多存10个字符，不足10个补空格

​	浪费空间，存取速度快

2.varchar 变长字符串

​	varchar(10) 最多存10个字符，不补空格

​	节省空间，存取速度慢

![image-20231230011143792](E:\笔记\数据库\Mysql.assets\image-20231230011143792.png)

### 日期类型

```sql
-- year——年

-- date——年月日

-- time——时分秒

-- datetime——年月日时分秒 8个字节，用的比较多

-- timestamp——年月日时分秒 4个字节一般不用
```

```sql
-- 例：
use db2;
create table user(
	id int,
	name varchar(16),
	born year,
	birthday date,
	active time,
    reg_time datetime
	
);
insert into user values(1, "张三", now(), now(), now(), now());
insert into user values(2, "李四", '2002', '2002-1-3', '13:01:02' , '2019-12-30 14:29:51');
```

![image-20231230223315466](E:\笔记\数据库\Mysql.assets\image-20231230223315466.png)

### 枚举类型

枚举

```sql
-- enum enumerate  单选（从给定的范围选一个值，例如性别）
```

```sql
-- 例：
create table user1(
	id int,
    name varchar(16),
    gender enum('male', 'female', 'other')
);
insert into user1 values(1, '张三', 'male');  -- 性别不在范围内会报错
```

集合

```sql
-- set 多选(例如爱好)
-- 例：
create table user2(
	id int,
    name varchar(16),
    hobby set('tea', 'coffee', 'cola', 'water')
);
insert into user2 values(1, '张三', 'tea');
insert into user2 values(2, '李四', 'tea,cola');
insert into user2 values(3, '王五', 'tea,milk'); -- 集合中不存在，报错
```

![image-20231230224119404](E:\笔记\数据库\Mysql.assets\image-20231230224119404.png)

![image-20231230224144412](E:\笔记\数据库\Mysql.assets\image-20231230224144412.png)

## 约束条件

```sql
-- 有顺序
[unsigned] [zerofill] [not null] [default <value>]
unsigned  -- 无符号
zerolfill  -- 填充0
not null  -- 不能为空
default <value> -- 默认值
unique key -- 唯一
primary key  -- 主键
auto_increment -- 自动递增  
```

### unique

```sql
-- 单列唯一
-- 这两种都可以
create table user(
	id int unique,
	name varchar(16),
);
create table user(
	id int,
	name varchar(16),
    unique(id)
);

-- 联合唯一
-- 几个字段加一起唯一
create table ip(
	id int,
	host varchar(15),
    port int,
    unique(host,port),
    unique(id)
);
```

![image-20231230225109196](E:\笔记\数据库\Mysql.assets\image-20231230225109196.png)

### primary key

不为空，唯一

```sql
-- 单列主键
-- 一般设为id
create table user3(
	id int primary key,
	name varchar(16)
);
```

![image-20231230225346627](E:\笔记\数据库\Mysql.assets\image-20231230225346627.png)

```sql
-- 复合主键
create table ip(
	id int,
	host varchar(15),
    port int,
    primary key(host, port),
    unique(id)
);
insert into ip(host, port) values('192.168.0.1', 1234);
insert into ip(host, port) values('192.168.0.1', 1235);
```

![image-20231230225754733](E:\笔记\数据库\Mysql.assets\image-20231230225754733.png)

### auto_increment 

```sql
-- id 主键 自动递增
create table user3(
	id int primary key auto_increment,
	name varchar(16)
);
-- 依然可以给id手动传值
```

![image-20231230230230825](E:\笔记\数据库\Mysql.assets\image-20231230230230825.png)

### 清空表

```sql
-- 清空数据，不清空自增值
delete frome table;
-- 清空数据和自增值
truncate table;
-- 自增id会变为1
```

## 外键

```sql
-- 外键
create table dep(
	id int primary key,
    name varchar(16),
    `desc` varchar(64)
);
create table emp(
	id int primary key,
    name varchar(16),
    gender enum('male', 'female'),
    mobile varchar(11),
    dep_id int,
    foreign key(dep_id) references dep(id)  -- 外键，关联dep表
);
```

```sql
-- 增加一些示例数据
insert into dep(id, name, `desc`) values
(1, '技术部', '负责技术工作'),
(2, '销售部', '负责卖东西'),
(3, '人事部', '负责人事工作');

insert into emp values
(1, '张三', 'male', 135,1),
(2, '李四', 'female', 135,2),
(3, '王五', 'male', 135,2),
(4, '赵六', 'female', 135,3);
```

问题：如果在两张表有数据关联的情况下，是无法修改dep的id，也无法删除dep的记录的。

```sql
-- on delete cascade  同步删除
-- on update cascade  同步更新

foreign key(dep_id) references dep(id)  -- 外键，关联dep表
on delete cascade  -- 部门表（技术部记录）删除，员工表对应员工也会被删除
on update cascade  -- 部门表（技术部记录）更新，员工表对应部门id也会更新
```

尽量少用外键约束，外键概念最好在应用层解决

### 多对一

类似上面案例

### 多对多

需要把外键剥离出来，单独创建一张表

```sql
create table dep(  -- 部门表
	id int primary key,
    name varchar(16),
    `desc` varchar(64)
);
create table emp(  -- 员工
	id int primary key,
    name varchar(16),
    gender enum('male', 'female'),
    mobile varchar(11),
);
create table emp2dep(
	id int primary key auto_increment,
    emp_id int not null,
    dep_id int not null,
    foreign key(emp_id) references emp(id) on delete cascade on update cascade,
    foreign key(dep_id) references dep(id) on delete cascade on update cascade
);
```

### 一对一

foreign key 没有强制要求一对一，

可以给外键设置的表的字段，添加一个unique的唯一约束实现

## 查询语法

### 单表查询

```sql

-- 有严格的顺序！！！
select [distinct] <字段...> 
	from <库名>.<表名>
	where <条件>
	group by <分组条件>
	having <过滤条件>
	order by <排序字段> {ASC | DESC} -- 默认升序ASC
	limit n;

-- distinct 去重，记录去重
-- having 也是过滤，但执行顺序在分组之后，where在分组之前
```

```sql
create database db5 charset=utf8;
use db5;
create table emp(
    id int primary key auto_increment,
    name varchar(16) not null,
    gender enum('male', 'female') not null,
    age int not null,
    salary float(10, 2),
    dep varchar(32),
    notes varchar(64)
);

insert into emp(name, gender, age, salary, dep) values
('关羽', 'male', 20, 8000, '技术部'),
('张飞', 'male', 25, 12000, '技术部'),
('赵云', 'male', 19, 6800, '技术部'),
('马超', 'male', 26, 11000, '技术部'),
('黄忠', 'female', 48, 15000, '技术部'),
('夏侯惇', 'male', 36, 34000, '技术部'),
('典韦', 'male', 19, 6500, '技术部'),
('吕布', 'female', 20, 9000, '技术部'),
('周瑜', 'female',32, 36000, '技术部'),
('文丑', 'male', 27, 24000, '技术部'),
('刘备', 'male', 32, 4000, '市场部'),
('诸葛亮', 'male', 27, 2700, '市场部'),
('庞统', 'male', 37, 4200, '市场部'),
('徐庶', 'male', 36, 4000, '市场部'),
('荀彧', 'male', 25, 2400, '市场部'),
('荀攸', 'male', 25, 2400, '市场部'),
('鲁肃', 'male', 43, 4300, '市场部'),
('司马懿', 'female', 44, 5000, '市场部'),
('杨修', 'male', 19, 800, '市场部'),
('丁仪', 'male', 49, 3500, '市场部'),
('宋江', 'male', 30, 4000, '人事部'),
('吴用', 'male', 38, 3000, '人事部'),
('扈三娘', 'female', 42, 2500, '人事部'),
('顾大嫂', 'female', 38, 3300, '人事部'),
('孙二娘', 'female', 32, 2400, '人事部'),
('丁得孙', 'male', 32, 2800, '人事部'),
('柴进', 'male', 30, 4200, '财务部'),
('卢俊义', 'male', 44, 4000, '财务部');


```

```sql
-- 支持四则运算加减乘除
-- as 起个别名，不加可以
select name,salary*12 from emp;
select name,salary+1 as yearly_salary,dep from emp;
select name,salary*12 yearly_salary from emp;
```

```sql
-- concat() 设置显示格式
-- 类似f''
select concat('姓名：', name, '    年龄：', age) as '姓名 年龄', concat('薪资：', salary) as '薪资' from emp;
select concat(name, '-', age, '-', salary) as '姓名-年龄-薪资' from emp;
-- concat_ws() 使用符号自动拼接
-- 类似 '-'.join()
select concat_ws('-', name, age, salary) as '姓名-年龄-薪资' from emp;
```

#### where

```sql
-- 1.and
select name, age from emp where age >=30 and age<= 40;
select name, age from emp where age between 30 and 40;  -- 闭区间

-- 2.or 
select name, age from emp where age<30 or age>40;  -- 小于三十或大于40

-- 3.not
select name, age from emp where not age >=20 and age<= 25;  -- 这种不行，not只判断前面
select name, age from emp where not age between 20 and 25; 

-- 4.in
select name, age, salary from db5.emp where salary in (3000, 4000, 5000); 

-- 5.is null 空字符串不算null,空字符串类型是字符串
select name, age, notes from db5.emp where notes='';   -- 和下面不同
select name, age, notes from db5.emp where notes is null; 
select name, age, notes from db5.emp where notes is not null; 

-- 6.like(模糊匹配)
--  _ 正则里面的 .    表示任意字符
--  % 正则里面的 .*   表示任意多个字符
select * from db5.emp where name like '荀_';  -- 只能匹配一个字
select * from db5.emp where name like '丁_*';  -- 可以匹配多个字

-- 7. 正则表达式匹配
-- regexp '正则表达式'
select * from emp where name regexp '^[丁|荀|张].*'
```

#### where [like和正则]  --待完善

##### like

```sql

```

##### 正则

```sql

```



#### group by

``` sql
select dep from db5.emp group by dep;

-- 聚合函数,统计的数据不能是null
count() 
max()
min()
sum()
avg()

select count(name) from db5.emp group by dep;

select dep,max(salary) from db5.emp group by dep;

select dep,sum(salary) from db5.emp group by dep;

select dep as '部门',avg(salary) as '平均工资' from db5.emp group by dep;

-- group_concat(name)
-- 获取分组的每一项
```

#### having

```sql
-- having 和 where功能相同
-- having 是在group by之后，所以having可以过滤分组以后的数据

-- 例： 查询部门员工数小于5的部门名字、员工名字、和人数
select dep,group_concat(name),count(id) from db5.emp group by dep having count(id)<5;

-- 例：查询部门年龄大于35的员工超过3人的部门名、以及大于35的人数、超过35的人名
select dep,count(id),group_concat(name) from db5.emp where age>35 group by dep having count(id)>3;
```

![image-20240102170447797](E:\笔记\数据库\Mysql.assets\image-20240102170447797.png)

#### order by 

```sql
-- 升序或降序  asc升序  desc降序
select * from db5.emp order by salary desc;

-- 可以根据多个条件排序
select * from db5.emp order by salary desc, id asc;
```

#### limit

``` sql
-- 展示的数据条数
select * from db5.emp order by salary desc, id asc limit 5;
```

```sql
-- limit 分页  
-- limit 起始位置，条数
select * from emp limit 0,10; -- 第一页
select * from emp limit 10,10; -- 第二页
select * from emp limit 20,10; -- 第三页

-- limit 分页效率低，因为每次都会从头开始查询
```

### 多表查询

#### 笛卡尔积

```sql
-- 两个表里的元素一一组合
select * from emp, dep;
-- 一张表30条  一张表6条，会查出来180条数据
-- 大多数时候不需要笛卡尔积
```

#### 连接查询

内连接——交集

```sql
-- 隐式内连接，不推荐。有些数据关联不到，会查不出来
select * from emp, dep where emp.dep_id=dep.id;

-- 显式内连接(内连接inner可以省略)
select * from emp inner join dep  on dep.id = emp.dep_id;
select * from emp join dep  on dep.id = emp.dep_id;
```

外连接

```sql
-- 左外连接 (外连接outter可以省略)
-- 查询左表所有数据，包括交集部分
select * from emp left join dep on dep.id = emp.dep_id;


-- 右外连接
-- 查询右表所有数据，包括交集部分
select * from emp right join dep on dep.id = emp.dep_id;
select emp.name,dep.name from emp right outer join dep on dep.id = emp.dep_id;
select dep.name, emp.name from dep right join emp on dep.id = emp.dep_id;
```

自连接

```sql
-- 自连接(一定要给表起别名)
-- 看成两张表，可以用内连接/外连接
select a.name as '员工', b.name as '领导'  from emp as a join emp as b on a.leader_id=b.id;

select a.name as '员工', b.name as '领导'  from emp as a left join emp as b on a.leader_id=b.id;
```

联合查询

把多个查询结果合并到一起   union  union all

```sql
-- 联合查询 union all
-- 把两次查询的结果拼接起来（可能会有重复）
select * from emp where salary>=15000
union all
select *from emp where age>=40;

-- union 去除重复项
select * from emp where salary>=15000
union
select *from emp where age>=40;

-- 注意：联合查询可以查询不同的表，但是字段数量必须相同
```

### 子查询

1. 标量子查询

   ```sql
   -- 返回单个值（就是把两个查询和在一起）
   select * from emp where dep_id=(select id from dep where name='技术部');
   ```

2. 列子查询

   ```sql
   -- in
   -- not in
   -- any			子查询返回列表中，有任何一个满足即可
   -- some			同any
   -- all			子查询返回列表，必须全部满足
   
   -- in   not in
   select * from emp where dep_id in (
       select id from dep where name='人事部' or name='技术部'
   )
   
   -- all
   select * from emp where join_date>all(
       select join_date from emp where dep_id=(
           select id from dep where name='市场部'));
   ```

3. 行子查询

   ```sql
   -- = 
   -- !=
   -- in 
   -- not in
   
   select * from emp where (salary, leader_id)=(select salary, leader_id from emp where name='关羽');
   ```

4. 表子查询

   ```sql
   -- in
   -- not in
   
   -- 表子查询用在where后做田间判断，也可以用在from后面作为数据源
   select * from (select * from emp where salary=8000) as e left join dep on e.dep_id=dep.id;
   ```

   ```
   select emp.*  from emp left join job_grade as j where emp.salary>=j.min_salary and emp.salary<=j.max_salary;
   ```

   

## 常用函数

### 字符串函数

```sql
-- 1.字符串拼接
select concat('姓名:', name) 

-- 2.转小写
select lower('HELLO') 

-- 3.转大写
select upper('hello') 

-- 4.左填充，用'-'在hello左边填充至10个字符
select lpad('hello', 10, '-') 

-- 5.右填充，用'-'在hello右边填充至10个字符
select concat('姓名:', name) 

-- 6.去掉字符串两端的空格
select trim('    hello  ') 

-- 7.切片从第二个地府开始,切3个字符
select substring('hello:', 2, 3) 
```

### 数值函数

```sql
-- 1.向上取整
select ceil(1.3) 

-- 2.向下取整
select floor(1.6) 

-- 3.取10/3的模
select mod(10, 3) 

-- 4.0-1随机小数
select rand() 

-- 5.四舍五入，保留4位小数，位数可以为负(保留小数点以前)
select round(17.552268, 4) 
```

### 日期函数

```sql
select '当前日期', CURRENT_DATE();
select '当前时间', CURRENT_TIME();
select '当前日期时间', CURRENT_TIMESTAMP();  -- 少用这个
select '当前日期时间', now();  -- 用这个

select '年', year(now());
select '月', month(now());
select '日', day(now());
select '时', hour(now());
select '分', minute(now());
select '秒', second(now());

-- 时间差计算,interval(间距)单位:year month day hour minute second microsecond等
-- 当前时间+1年/月/日 等等
select date_add(now(), interval 1 year);
-- 第一个日期 - 第二个日期  返回天数
select datediff('2024-12-3 12:50:32', now());
-- 第一个时间 - 第二个时间  返回小时
select timediff('2024-12-3 12:50:32', now())
```

### 流程控制函数

```sql
-- if(条件, 为真返回值, 为假返回值)
select name,if(gender='male', '男性', '女性') from db5.emp;
-- ifnull(字段, 为null返回值)  类似 .get(gender, '未知')
select name,ifnull(gender, '未知') from db5.emp;

-- case...when...then...else  end
select name,
       case
           when salary>10000 then '核心员工'
           when salary>5000 then '普通员工'
            else '新员工'
            end
        as '员工薪资'
from db5.emp;
```

## 数据库控制语言DCL

### 用户操作

#### 查询用户

```sql
-- 1.查询用户
select * from mysql.user;
```

#### 创建用户

```sql
-- 2.创建用户
create user '用户名'@'IP' identified by '密码';
create user 'td'@'%' identified by '007741ak';
```

#### 修改用户

```sql
-- 修改密码
alter user 'td'@'%' identified with mysql_native_password by '新密码';

update mysql.user set host='主机名' where user='用户名';

```

#### 删除用户

```sql
drop user '用户'@'主机名';
```

### 分配权限

权限控制（运维.DBA）

- all/all privileges：所有权限
- insert：插入数据权限
- delete：删除数据权限
- udpate：修改数据权限
- selete：查询数据权限
- create：创建数据库/表权限
- drop：删除数据库/表/视图权限
- alter：修改表/字段权限

查询权限

> show grants for '用户名'@'主机名';

```sql
show grants for 'fei'@'localhost';
```



分配权限（授予权限）

> grant 权限列表 on 库名.表名 to '用户名'@'主机名';

```sql
grant all on db6.* to 'fei'@'localhost';
```



撤销权限

> revoke 权限列表 on 库名.表名 from ' 用户名'@'主机名';

刷新权限

> flush privileges;



## 事务

一组操作的集合（多条sql语句的集合）

这个集合是一个不可分割的工作单位，所有操作一起提交，

如果提交过程中，有任何一条sql出现了错误，那便会回退到整个事务执行之前的状态，

即：！要么同时成功，要么同时失败！。         

```sql
-- 开启事务
start transaction ; -- 这种方法需要显式地指定事务的起始和结束。在事务开始后,可以使用COMMIT或ROLLBACK语句来结束事务。
begin ; -- 这种方法可以省略事务的结束语句。在事务开始后,如果发生错误,可以使用ABORT语句来终止事务;如果事务成功完成,可以使用COMMIT语句来结束事务。

-- 提交事务
commit ;
-- 结束事务
rollback ;
```

### 事务四个特性 ACID

- 原子性（**A**tomicity）

  > 事务是不可分割的最小操作单元，这个操作单元要么全部成功，要么全部失败。

- 一致性（**C**onsisteny）

  > 事务完成的时候，必须使所有数据都保持一致状态。

- 隔离性（**I**solation）

  > 在并发的时候，A事务在操作的时候，它不会影响B事务的执行；B事务在操作的时候，也不会影响A事务。

- 持久性（**D**urability）

  > 事务不管是提交还是回滚，它对数据库里面数据的改变，都是永久的。

### 并发事务

脏读

一个事务读取到了另一个事务还未提交的数据

不可重复读

幻读



### 事务隔离级别

### 

| 隔离级别   | Read Uncommitted | Read Committed | Repeatable Read（默认） | Serializable |
| ---------- | :--------------: | :------------: | :---------------------: | :----------: |
| 脏读       |        ✔︎         |       ✗        |            ✗            |      ✗       |
| 不可重复读 |        ✔︎         |       ✔︎        |            ✗            |      ✗       |
| 幻读       |        ✔︎         |       ✔︎        |            ✔︎            |      ✗       |

问：我们在一个事务里面插入一批非常大的数据，这个事务已经执行了很长时间，并且还在执行过程中，没有提交，如何知道当前已经插入了多少条数据？

答：利用Read Uncommitted隔离级别，用一个新的事务，读取到这一批大数据的插入进度。



```sql
-- 查看事务隔离级别
select @@transaction_isolation

-- 设置事务隔离级别
set {session|global} transaction isolation level <隔离级别>;
```



## Mysql体系结构

![image-20240104120155278](E:\笔记\数据库\Mysql.assets\image-20240104120155278.png)

在存储层，库->文件夹，表->目录

## 索引

有序的数据结构，提高查询效率

有二叉树 B-Tree  B+Tree 红黑树等

-  B+Tree索引
- Hash索引
- Full-Text索引
- R-Tree索引

|   索引    |   Innodb    | MyIsam | Memory |
| :-------: | :---------: | :----: | :----: |
|  B+Tree   |      ✔      |   ✔    |   ✔︎    |
|   Hash    |      ✗      |   ✗    |   ✔    |
| Full-Text | 5.6之后支持 |   ✔    |   ✗    |
|  R-Tree   |      ✗      |   ✔    |   ✗    |

1. 二叉树

   顺序插入时，会形成链表，查询效率大幅度降低

2. 红黑树

   二叉树基础上会自平衡，当数据量特别大的时候，层级非常深，查找速度同样会很慢。

3. B-Tree（多路平衡查找树）

   一个节点下面可以包含多个子节点

4. B+Tree(默认)

   所有数据都出现在叶子节点上，叶子节点会出现单向链表，范围查找方便

5. Hash索引

   Memory支持

   

### 索引分类

- 主键索引
- 唯一索引
- 常规索引
- 全文索引

存储形式不同：

- 聚集索引/聚促索引
- 二级索引/辅助索引/非聚集索引

​	

​	









