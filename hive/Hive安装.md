#### Hive安装
> 前提：mysql已经安装好，hive下载完毕
> 
> hive元数据是什么？
> 
> （1）hive表的数据库名称、表名、字段名称与类型、分区字段与类型
> 
> （2）表的分区、分区属性（location等）
> 
> （3）serdeproperties、tblproperties属性等
> 
> 可以通过
> desc formatted tablename
查看表有哪些属性

##### 1. 配置derby内置数据库管理hive元数据
>
> 1) hive-default.xml通过如下配置设置：

```
<name>javax.jdo.option.ConnectionURL</name>  
  <value>jdbc:derby:;databaseName=metastore_db;create=true</value>  
  <description>JDBC connect string for a JDBC metastore</description>  
</property>  
  
<property>  
  <name>javax.jdo.option.ConnectionDriverName</name>  
  <value>org.apache.derby.jdbc.EmbeddedDriver</value>  
  <description>Driver class name for a JDBC metastore</description>  
</property>  
```
> 2) 初始化数据库

```
bin/schematool -dbType mysql -initSchema
```
>
>3) 测试使用

```
hive 

hive> show databases;
```



##### 2. 配置mysql管理hive元数据
>
> 1) 为Hive建立相应的MySQL账户,并赋予足够的权限,执行命令如下:

```
CREATE USER 'hive' IDENTIFIED BY 'mysql';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'%' WITH GRANT OPTION;
flush privileges;
```
>
>默认情况下Mysql只允许本地登录，所以需要修改配置文件将地址绑定给注释掉：

```
bind-address           = 127.0.0.1
```
>
>重启mysql服务
>
>2) 为hive创建专业元数据库，需要使用刚才创建的“hive”账号登录
>

```
create database hive;
```
>
>3) 在Hive的conf目录下的文件“hive-site.xml”中增加如下配置:

```

<property>
        <name>hive.metastore.local</name>
        <value>true</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://yourip:3306/hive?characterEncoding=UTF-8</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hive</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>mysql</value>
    </property>
```


>4) 把MySQL的JDBC驱动包复制到Hive的lib目录下。
>
>5)初始化数据库

```
bin/schematool -dbType mysql -initSchema
```
>
>6) 验证

```
hive

hive>show databases;
```

