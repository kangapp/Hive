# Hive

## Hive简介

>The Apache Hive ™ data warehouse software facilitates reading, writing, and managing large datasets residing in distributed storage using SQL. Structure can be projected onto data already in storage. A command line tool and JDBC driver are provided to connect users to Hive.

- Hive是建立在Hadoop HDFS上的数据仓库基础架构
- Hive可以用来进行数据提取转换加载（ETL）,通过sqoop加载数据
- Hive定义的简单的sql查询语言Hql
- Hive是sql解析引擎，将sql语句转换为M/R job或spark job执行
- Hive的表其实就是HDFS文件和目录

### Hive元数据
- Hive将元数据存储在数据库中，支持mysql、derby等数据库
- Hive中的元数据包括表的名字，表的列和分区及其属性（是否为外部表），表的数据所在的目录等

## Hive安装和部署

- 安装包下载：`curl -O http://archive.cloudera.com/cdh5/cdh/5/hive-1.1.0-cdh5.7.0.tar.gz`
- 解压并配置环境变量
- 修改配置文件  
**`hive.env.sh`**  

|name    | description  |  
|--------|------|
|HADOOP_HOME   | 指定Hadoop安装路径 |
|HIVE_CONF_DIR  |指定hive配置文件路径|

**`hive-site.xml`** 

|name    | value   | 
|-------|----------|
|avax.jdo.option.ConnectionURL|jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true\&amp;useSSL=false|
|javax.jdo.option.ConnectionDriverName|com.mysql.jdbc.Driver|
|javax.jdo.option.ConnectionUserName|root|
|javax.jdo.option.ConnectionPassword<|123456|  

web界面配置参考：https://cwiki.apache.org/confluence/display/Hive/HiveWebInterface#HiveWebInterface-FeaturesofHWI

- 官网下载mysql和mysql连接类  
启动如有mysql报错，可以尝试初始化：`schematool -dbType mysql -initSchema`

## Hive DDL（数据定义语言）之数据库操作

官网地址：https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

### Create/Drop/Alter/Use Database

- Create Database
> CREATE (DATABASE|SCHEMA) [IF NOT EXISTS] database_name  
  [COMMENT database_comment]  
  [LOCATION hdfs_path]  
  [WITH DBPROPERTIES (property_name=property_value, ...)];  

  查看数据库信息
  ![查看数据库信息](images/DBS.png)  
  ![](images/desc.png)
  命令行添加当前数据库提示  
  `set hive.cli.print.current.db=true;`

- Drop Database
> DROP (DATABASE|SCHEMA) [IF EXISTS] database_name [RESTRICT|CASCADE];

- Alter Database
> ALTER (DATABASE|SCHEMA) database_name SET DBPROPERTIES (property_name=property_value, ...);   -- (Note: SCHEMA added in Hive 0.14.0)
 
> ALTER (DATABASE|SCHEMA) database_name SET OWNER [USER|ROLE] user_or_role;   -- (Note: Hive 0.13.0 and later; SCHEMA added in Hive 0.14.0)
  
> ALTER (DATABASE|SCHEMA) database_name SET LOCATION hdfs_path; -- (Note: Hive 2.2.1, 2.4.0 and later)

- Use Database

### Create/Drop/Truncate Table

- [Create Table](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-CreateTable)

示例
```
hive (hive)> create table emp(
           > empno int,
           > ename string,
           > job string,
           > mgr int,
           > hiredate string,
           > sal double,
           > deptno int
           > ) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';
```

查看表结构信息  
`desc formatted emp;`

## Hive DML（数据操作语言）

### Loading files into tables

>LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)]
 
>LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)] [INPUTFORMAT 'inputformat' SERDE 'serde'] (3.0 or later)
```bash
# /user/hive/warehouse/hive.db/emp/emp.txt
hive (hive)> LOAD DATA LOCAL INPATH '/Users/liufukang/app/hive-1.1.0-cdh5.7.0/test_data/emp.txt' OVERWRITE INTO TABLE emp;
```