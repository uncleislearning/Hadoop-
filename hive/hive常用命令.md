#### hive特性

定义：

```
The Apache Hive™ data warehouse software facilitates reading, writing, and managing large datasets residing
 in distributed storage and queried using SQL syntax.
```

>优点：
>
>消除了大量通用的mapreduce代码
>
>方便熟悉SQL的用户使用hive，容易上手
>
>非常适合做数据仓库程序
>
>缺点：不支持行级别的更新、快速的查询响应、事务


#### 常用CLI（hive command line option）命令

* bin/schematool -dbType derby -initSchema
>
> 功能介绍：首次安装hive，需要初始化元数据存储的数据库，这里使用的是内嵌的数据库derby，实际中需要配置mysql来管理hive的元数据
>

* hive
>功能介绍：进行hive交互环境

* hive -e 'select a.col from table a'
> 功能介绍：在命令行执行HiveQL语句

* hive -e 'select a.col from table a' --hiveconf mapred.reduce.tasks=32
>功能介绍：设置hive配置变量、并执行查询

* hive -f /home/hive-script.sql
>功能介绍：执行sql脚本;这里的脚本也可能是其他文件系统中的，需要指定完整的路径（如hdfs://namenode:9000/home/hive-script.sql）

* hive -i /home/hive-script.sql
>功能介绍：执行sql初始化脚本之后，进入hive交互环境

#### 常用hive交互环境下的命令

##### hive 交互shell命令

* quit/exit
> 功能介绍：退出hive交互环境

* set -v
> 功能介绍：打印Hadoop、Hive的配置变量

* set 
> 功能介绍：打印被用户重写的Hive的配置变量

* set ‘key’=‘value’
> 功能介绍：重写配置

* reset 
> 功能介绍：恢复默认配置变量

* ! ‘shell command’
> 功能介绍：在hive交互环境下执行本地shell命令

* dfs ‘dfs command’
> 功能介绍：在hive交互环境下执行hdfs文件系统 shell命令

* source ’filepath‘
> 功能介绍：在hive交互环境下执行脚本文件

##### Hive DDL(Data Definition Language)

* 创建数据库

```
语法：
create (database|schema) [if not exists] database_name

[commit database_comment]

[location hdfs_path]

示例：
create database if not exists mydb;

```

* 删除数据库

```
语法：
drop (database|schema) [if exists] database_name [restrict|cascade]

示例：
drop database if exists mydb;

```

* 更改数据库

```
语法：
alter  (database|schema)  database_name set location 

示例：
alter  database  mydb set owner user 'root'

```

* 创建表

```
语法：
create [temporary][external] table [if not exists][db_name.]table_name (col_name data_type ....)[commit table_comment]
[partitioned by (col_name data_type...)]
[clustered by (col_name data_type...)[sorted by (col_name[asc|desc],...)] into num_buckets buckets]
[stored as directories]

示例一(创建一个普通的表)：
create table mytable(id int,name string,tdate date)
partitioned by(province string,country string)
row format delimited
fields terminated by '\t'
stored as textfile


示例二（创建一个外部表，并将指定的数据文件加载入表格）：
create external table mytable(id int,name string,tdate date )
row format delimited fields 
terminated by '\t' 
stored as textfile 
location '/sogou'


示例三（从一个select查询的结果中，创建一个新的表）：
 create table mytable row format serde "org.apache.hadoop.hive.serde2.columnar.ColumnarSerDe"
 stored as rcfile
 as
 select (key%1024) new_key,concat(key,value) kv_pair
 from anotable
 sort by new_key, kv_pair;

示例四(创建一个新表，空的，新表结构与like后面的表结构一毛一样)：
create table empty_new_table
like full_old_table;
 
```


##### Hive DML(Data Manipultation Language)

* 加载数据到表中

```
方法一：
load data [local] inpath 'filepath' [overwrite] into  table table_name [partitioin (partcol=....)]

方法二：
insert overwrite table table_name select .....

示例一(使用sql的方式向表中插入数据)：
insert into table mytable values(1,'李天',30),(1,'李天',30);

insert into table mytable partition(datestamp='2013-09-23') values(1,'李天',30),(1,'李天',30);

示例二（从select查询结果加载数据）：

insert overwrite table mytable select * from anoable;


示例三（从文件中加载数据）：

load data inpath '/hdfs/file/data' overwrite into table mytable;

```


* 更新表

```
语法：
update table_name set column=value[,column=value] [where expression]

示例：

update mytable set name='李天昊' where id=1;

```

* 删除表数据

```
语法：
delete from table_name  [where expression]

示例：
delete  from mytable  where id=1;

```

* 合并表

```
语法：

merge into <target table> as t using <source expression/table> as s
on <boolean expression1>
when matched [and <boolean expression2>] then update set <set clause list>
when matched [and <boolean expression3>] then delete
when not matched [and <boolean expression4>] then insert values<value list>

示例：
	目标表：
	create table transactions(id int,value string,lastUpdateUser string) 
	partitioned by (tran_date string)
	clusterd by (id) into 5 buckets
	stored as orc  tblproperties('transactional'='true')

   源表：
   create table source(id int,value string,tran_date string)
   stored as orc;

   合并表操作：
   
   merge into transactions as t
   using transactions as s
   on t.id=s.id and t.tran_date=s.tran_date
   when matched and (t.value!=s.value and s.value is not null) then    
   update set value = s.value , lastUpdateUser='merge_update'
   when matched and s.value is not null then delete
   when not matched then insert values(s.id,s.value,'merge_update',s. tran_date);

```

#### 问题一：使用hiveQL 编写worldcount程序
```
	create table docs(line string);
	load data inpath 'filepath' overwrite into table docs;
	create table word_counts as select word,count(1) as count from (select explod(split(line,'\s')) as word from docs) w group by word order by word desc;

```