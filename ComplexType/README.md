# Hive复杂数据类型实战

> Hive复杂数据类型
>> - 数组：Array，有顺序且每一个数组元素都必须是相同的数据类型，从0开始索引
>> - 键值对：MAP
>> - 结构体：STRUCT
>> - 联合体：UNIONTYPE,还没完全支持

## 复杂数据类型表基本操作

- 创建表
```sql
use complex;

create external table if not exists emp1(
        id int comment '员工编号',
        name string comment '员工姓名',
        salary float comment '员工薪资',
        family array<string> comment '家庭成员',
        deductions map<string, float> comment '税收项',
        address struct<city:string, street:string, zip:int> comment '住址（城市，街道，编码）'
)
row format delimited
fields terminated by ','
collection items terminated by ' '
map keys terminated by ':';
```
- 导入数据

>1,Mars,14500,Miao Super,Tax:.2 Insurance:.03,Guangzhou 黄埔大道 120303
2,John,14501,Miao1 Super1,Tax:.3 Insurance:.01,Guangzhou 中山大道 120303
3,Jack,14502,Miao2 Super2,Tax:.4 Insurance:.02,Guangzhou 财经路 120303
4,Allen,14503,Miao3 Super3,Tax:.5 Insurance:.04,Guangzhou 京溪路 120303

```sql
use complex;

load data local inpath '/Users/liufukang/Project/hive/data/emp1.txt' into table emp1;
```

- 操作
```sql
-- 下标溢出不报错，返回NULL
select family[0] from emp1;
-- 查询数组元素个数
select size(family) from emp1;
-- 判断数组是否包含某个元素，true/false
select array_contains(family,'Miao') from emp1;
-- 数组元素排序
select sort_array(family) from emp1;
```

```sql
-- 查询key对应的value值
select deductions['Tax'] from emp1;
-- 查询map键值对的个数
select size(deductions) from emp1;
-- 查询map的所有key
select map_keys(deductions) from emp1;
-- 查询map的所有value值
select map_values(deductions) from emp1;
```

```sql
-- 读取struct对应的属性值
select address.city,address.zip from emp1;
```

## 复杂类型的嵌套

### ARRAY和STRUCT嵌套

- 创建表
>1,Mars,14500,Guangzhou:黄埔大道:12345 Changsha:RenminRoad:123454
```sql
use complex;

create external table if not exists emp2(
        id int comment '员工编号',
        name string comment '员工姓名',
        salary float comment '员工薪资',
        info array<struct<city:string, street:string, zip:int>>
)
row format delimited
fields terminated by ','
collection items terminated by ' '
map keys terminated by ':';

load data local inpath '/Users/liufukang/Project/hive/data/emp2.txt' into table emp2;
```

- 查询
```sql
select info[0].city from emp2;
```

### STRUCT和MAP嵌套

> 存在4层嵌套关系
>>各个字段之间  
>>Struct的各个成员之间  
>>Map的键值对之间  
>>每个键值对的key和value之间  

**`如果自己指定分隔符，无法满足超过4层的嵌套条件，需要采用默认的分割规则：`**  
**`第一层：\001 即^A`**  
**`第一层：\002 即^B`**  
**`第一层：\003 即^C`**  
**`第一层：\004 即^D`**  
...以此类推  

> 1^AMars^A14500^BTax^D.2^CInsurance^D.05
- 创建表
```sql
use complex;

create external table if not exists emp3(
        id int comment '员工编号',
        name string comment '员工姓名',
        info struct<salary:float, deductions:map<string,float>>
);

load data local inpath '/Users/liufukang/Project/hive/data/emp3.txt' into table emp3;
```
- 查询
```sql
select info.deductions['Tax'] from emp3;
```

### ARRAY/MAP/STRUCT相互嵌套

>1^AMars^AWife^EMiao^DDad^ERoger^DMon^EWang^DBro^ESuper^BGuangzhou^C黄埔大道^C123456

- 创建表
```sql
use complex;

create external table if not exists emp4(
        id int comment '员工编号',
        name string comment '员工姓名',
        info struct<
                family:array<map<string,string>>,
                address:struct<city:string,street:string,zip:int>
        >
);

load data local inpath '/Users/liufukang/Project/hive/data/emp4.txt' into table emp4;
```

**`结论：`** 当嵌套的层级超过了3层之后，自定义的分隔符无法满足字段定义的需求，会造成数据分隔导致位移，此时只能用默认分隔符进行多层的分隔。当然，在正常情况下，不会设计特别复杂的数据类型嵌套。

*`Load data的方式，需要自己输入分隔符，但是大多数情况下，进行ETL的时候，数据是通过insert into...的方式进行加载，此时分隔符就由Hive表定义时指定`*