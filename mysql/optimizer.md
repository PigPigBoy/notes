# SQL优化步骤

## 1. 查看SQL执行频率
分析数据库是插入为主还是查询为主
```sql
# 当前连接统计
show status like "Com_%";

# 全局统计
show global status like "Com_%";

# 针对InnoDB表统计
show status like "Innodb_rows_%";
```

### 关心的几个参数
参数|含义
:-:|:-:
Com_select|select次数，一次查询+1
Com_insert|insert次数，批量插入也是+1
Com_update|update次数
Com_delete|delete次数
Innodb_rows_read|select查询返回的行数
Innodb_rows_inserted|insert插入的行数
Innodb_rows_updated|update更新的行数
Innodb_rows_deleted|delete删除的行数
Connections|试图连接Mysql的次数
Uptime|服务器的工作时间
Slow_queries|慢查询次数
<br />

# 2. 定位低效SQL
- 慢查询日志
- show processlist：可以实时查看sql执行的情况

Id|User|Host|db|Command|Time|State|Info
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
35|root|localhost|ts_lecture|Query|2|Sending Data|select * from lecture

- id：用户登录mysql，系统分配的"connection_id"，可以使用函数connection_id()查看
- user：当前用户。如果不是root，那么只显示用户权限范围的sql语句
- host：ip+端口，可用来追踪出现问题语句的用户
- db：这个进程是连接哪一个数据库
- command：当前连接的执行命令，一般取值为休眠（Sleep），查询（Query），连接（connect）等
- time：显示这个状态持续的时间，单位是秒
- `state`：当前sql语句的状态。比如：
  - copying to tmp table
  - sorting result
  - sending data
- info：显示这条sql语句

<br />

# 3. explain分析执行计划
id|select_type|table|type|possible_keys|key|key_len|rows|extra
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
1|SIMPLE|tb_1|ALL|NULL|NULL|NULL|10000|Using Where

- id：执行select子句或者操作表的顺序
- select_type：select类型
  - SIMPLE：简单表，即不使用表连接或子查询
  - PRIMARY：主查询，即外层查询
  - UNION：UNION中第二个或后面的查询语句
  - SUBQUERY：子查询的第一个SELECT
- type：表的连接类型，从好到坏依次是
  - system
  - const
  - eq_ref
  - ref
  - ref_or_null
  - index_merge
  - index_subquery
  - range
  - index
  - all
- possible_keys：可能使用的索引
- key：实际使用的索引
- key_len：索引字段的长度
- row：扫描的行数
- extra：执行情况说明与描述

# 4. trace
MySQL5.6提供了对SQL的跟踪trace，通过trace能进一步了解优化器的执行计划，知道为什么选择A计划而不是B计划