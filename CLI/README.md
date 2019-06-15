# Hive命令行操作

## 参数

### `hive --hiveconf <pro=value>`
>优先级高于hive-site.xml当中的设置，但低于Hive交互式Shell中的set所设置的值

### `hive --database <database name>`
>指定使用的数据库

### `hive -f <file>`
> hive执行文件,可执行hdfs文件（hdfs://）

### `hive -d,--define <key=value>`
> 定义变量，${key}引用

### `hive -S,--silent`
>静默模式，不显示查询过程

### `hive -V,--verbose`
>冗余模式，打印更多信息

## 交互式命令

### `hive> reset`
> 重置set配置

### `hive>  set k=v`
> 设置环境变量

### `hive> set -v`
> 打印所有的配置信息

### `hive> add [FILE|JAR|ARCHIVE] [value*]`
> 添加文件，使用UDF时需使用add jar

### `hive> list [FILE|JAR|ARCHIVE] [value]*`
> 列出添加的文件

### `hive> delete [FILE|JAR|ARCHIVE] [value*]`
> 删除添加的文件

### `hive> !<shell command>`
> 使用shell命令

### `hive> dfs <hdfs command>`
> 使用hdfs命令