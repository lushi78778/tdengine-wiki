
# 常用sql命令

## 修改用户密码

```sql
alter user root pass `your password` 
```

## 登录数据库

```bash
taos -u root -p
```

## 数据库操作

```sql
# 创建库（如果不存在）keep 字段是指文件在表存储的时间
create database if not exists mydb keep 365 days 10 blocks 4;

# 使用库
use mydb;

# 删除库
drop database mydb;

# 删除库（如果存在）
drop database if exists mydb;

# 显示所有数据库：
show databases;

# 修改数据库文件压缩标志位：
alter database mydb comp 2;

# 修改数据库副本数：
alter database mydb replica 2;

# 修改数据文件保存的天数：
alter database mydb keep 365;

# 修改数据写入成功所需要的确认数：
alter database mydb quorum 2;

# 修改每个VNODE (TSDB) 中有多少cache大小的内存块：
alter database mydb blocks 100;
```

## 表操作

```sql
# 创建表，创建表时timestamp 字段必须为第一个字段类型，为主键：
create table if not exists mytable(column_name timestamp, column_name int，……);

# 根据超级表创建子表，这样建表之后，子表会复制除去超级表里面的tags字段外的所有字段；

create table table_name using super_table tags (column_value,column_value……)；

# 删除数据表
drop table if exists mytable;

# 显示当前数据库下的所有数据表信息
show tables;

# 显示当前数据库下的所有数据表信息
show tables like "%table_name%";

# 获取表的结构信息
describe mytable;

# 表增加列
alter table mytable add column addfield int;

# 表删除列
alter table mytable drop column addfield;
```
 
## 超级表操作

```sql
# 创建超级表
# 创建STable, 与创建表的SQL语法相似，但需指定TAGS字段的名称和类型。说明：
# 1) TAGS 列的数据类型不能是timestamp类型；
# 2) TAGS 列名不能与其他列名相同；
# 3) TAGS 列名不能为预留关键字；
# 4) TAGS 最多允许128个，可以0个，总长度不超过16k个字符
create table if not exists mysupertable (time timestamp, column_name int，……) tags (column_name nchar(50), column_name nchar(100)，……);

# 删除超级表
drop table if exists super_table ;

# 显示当前数据库下的所有超级表信息
show stables like "%super%";

# 获取超级表的结构信息
describe super_table ;

# 超级表增加列
alter table super_table add column column_name int;

# 超级表删除列
alter table super_table drop column column_name;

# 添加标签
alter table super_table add tag column nchar(60);

# 删除标签
alter table super_table drop tag tag_name;

# 修改标签名
alter table super_table change tag old_tag_name  new_tag_name;

# 修改子表标签值(TAG)
alter table item_table_name set tag column_key = "value";
```
## 栗子
创建电表超级表：super_table
```sql
create database mydb；
use mydb；
create table super_dianbiao（ts timestamp,dianya float,dianliu float） tags (yezhu_name nchar(15),xiaoqu_location nchar(50),menpai_num nchar(10));
```
创建子表dianbiao
```sql
create table dianbiao1001 using super_dianbiao tags('张三','东城小区','1-1101');
create table dianbiao1002 using super_dianbiao tags('李四','东城小区','1-1102');
```
往子表中插入数据
```sql
insert into dianbiao1001 values(now,1.7,3.2);
```