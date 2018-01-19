####将数据从Mysql导入HDFS 

```
sqoop import --connect jdbc:mysql://127.0.0.1/mydb --username root --password 123456 --table mytable --target-dir '/sqoop/data/mytable' -m 3
```


####将数据从Mysql导入Hive

```
bin/sqoop import --connect jdbc:mysql://192.168.52.201:3306/userdb --username hive1 --password hive1  --table emp --hive-import -m 1 

```


####将数据从Hive导入Mysql

```
sqoop export --connect jdbc:mysql://192.168.56.101:3306/mydb --username xiao --password 123456 --table mytableinmysql --export-dir '/user/hive/warehouse/hive.db/sogou_uid_cnt' --fields-terminated-by '\t'

```