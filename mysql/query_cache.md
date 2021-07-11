# 查询缓存

## 概述
- 当执行完全相同的SQL语句的时候，服务器就会直接从缓存中读取结果。当数据被修改，之前的缓存会失效。修改频繁的表不适合做查询缓存。

![](/mysql/query_cache.jpg)

1. 客户端发送一条查询
2. 检查缓存。如果命中了，则立即返回，否则下一步
3. sql解析器、优化器生成执行计划
4. 根据执行计划调用存储引擎api来查询
5. 结果返回给客户端,同时记缓存

<br />

## 配置
- 是否支持？`show variables like 'have_query_cache'`
  - 5.7 支持
- 是否开启？`show variables like 'query_cache_type'`
  - 5.7 默认关闭
- 缓冲容量？`show variables like 'query_cache_size'`
  - 5.7 默认 1MB
- 缓存的状态变量
  - `show status like 'Qcache%'`
  - 变量如下
  
参数|含义
:-:|:-:
Qcache_free_blocks|查询缓存中可用的内存块个数
Qcache_free_memory|查询缓存中可用的内存容量
Qcache_free_`hits`|查询缓存命中数
Qcache_free_`inserts`|添加到查询缓存的查询数
Qcache_lowman_prunes|由于内存不足而从查询缓存中删除的查询数
Qcache_`not_cached`|由于query_cache_type设置而无法缓存或者未缓存的数量
Qcache_queries_in_cache|在查询缓存中的查询数
Qcache_total_blocks|查询缓存的中块总数

<br />

## 开启查询缓存
query_cache_type有三个取值
- off / 0 ： 缓存功能关闭
- on / 1 ： 打开缓存功能，select结果符合条件会缓存。显示指定 sql_no_cache，不予缓存
- demand / 2 ： 显示指定 sql_cache的select语句才会缓存

在 /usr/my.cnf 增加配置： `query_cache_type = 1`。重启服务即可`service mysql restart`

```sql
    select sql_cache a,b from tb;
    select sql_no_cache a,b from tb;
```

<br />

## 查询缓存失效
- sql语句不一致
  - `s`elect count(*) from tb;
  - `S`elect count(*) from tb;
- 存在一些不确定的函数比如 `now()`，`current_date()`，`curdate()`，`curtime()`，`rand()`，`uuid()`，`user()`，`database()`。其实就是第一条的特例。
- 不使用表
  - select 'hello'
  - 这...需要缓存吗？
- 系统表
  - select * from information_schema.engines
  - 其实和上面一样。这两条完全没有缓存的必要。
  - 系统数据库有哪些？
    - information_schema
    - performance_schema
    - mysql
- 存储过程、函数、触发器内的sql不走缓存查询
- 查询缓存被清空
  - 表数据发生了变化
  - 哪些操作会触发？
    - 增删改
    - truncate
    - alter
    - drop

<br />

## Mysql8删除了查询缓存
### why