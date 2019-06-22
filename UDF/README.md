# UDFs实战

## UDF

### 应用场景1：从身份证号计算出年龄
```sql
use hive;

create table person(
        name string,
        id string
)
row format delimited
fields terminated by ',';

load data local inpath '/Users/liufukang/Project/hive/data/person.txt' into table person;
```
- 实现1：substring()取出身份证的年份，然后用当前时间的年份去减
```sql
select year(to_date(current_timestamp())) - substring(id,7,4) from person;
```
- 实现2：自定义UDF函数，输入身份证字段，输出年龄

```java
//编写代码并生成jar包
import org.apache.hadoop.hive.ql.exec.UDF;

import java.util.Calendar;

public class getAgeFromId extends UDF {

    Calendar cal = Calendar.getInstance();
    int year = cal.get(Calendar.YEAR);

    public String evaluate(final String s) {
        if(s == null) {
            return null;
        } else {
            int age = year - Integer.parseInt(s.substring(6,10));
            return Integer.toString(age);
        }
    }
}
```
```sql
add jar /Users/liufukang/Project/hive/lib/UDFTest-1.0.jar;

create temporary function getAge as 'cn.test.getAgeFromId';

select name,getAge(id) from person;
```

**`可以把添加函数的sql写在$HIVE_HOME/conf/.hiverc中，hive启动会默认执行此文件，不然hive重新启动之后之前创建的函数就会失效`**