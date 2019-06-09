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
|avax.jdo.option.ConnectionURL|jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false|
|javax.jdo.option.ConnectionDriverName|com.mysql.jdbc.Driver|
|javax.jdo.option.ConnectionUserName|root|
|javax.jdo.option.ConnectionPassword<|123456|  
web界面配置参考：https://cwiki.apache.org/confluence/display/Hive/HiveWebInterface#HiveWebInterface-FeaturesofHWI