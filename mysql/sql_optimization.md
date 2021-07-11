# SQL优化场景

## 1、大批量插入数据
- 一次性插入100W数据怎么做

### （1）按主键有序插入
- 经测试，100w条数据，有序插入约是20s，无序插入约是110秒

### （2）关闭唯一性校验
- 表存在唯一性索引，每次插入会对唯一性进行校验

### （3）手动提交事务
- 在导入前, `set autocommit = 0` 关闭自动提交

<br />

## 2、insert
### （1）批量插入
- 减少客户端与数据库之间的连接、关闭等消耗
### （2）手动提交事务
```sql
    start transaction;
    insert into tb values (a, b, c);
    insert into tb values (d, e, f);
    insert into tb values (g, h, i);
    insert into tb values (j, k, l);
    commit;
```

### （3）按主键有序插入

<br />

## 3、order by
### mysql排序有两种
- `Using index`
  - 效率高
  - 通过索引顺序扫描直接返回有序数据
- `Using filesort`
  - 效率低
### 如何判断
`覆盖索引。返回的字段在全部都索引列中，就会使用索引`

假设 tb 有 a，b，c，d 四个字段，其中，`c`，`d`是复合索引字段
```sql
    # Using filesort
    select * from tb order by c

    # Using index
    select id from tb order by c
    select id, c, d from tb order by c

    # Using filesort
    # a不在索引中
    select id, a, c, d from tb order by c
```

`多字段排序时，每个字段排序规则不一致，也会有filesort`
```sql
    # Using index; Using filesort
    select id from tb order c asc, d desc
```

`排序字段要与索引顺序保持一致`
```sql
    # Using index; Using filesort
    select id from tb order by d, c
```

### `总结`：
- 通过索引直接返回数据（覆盖索引）
- where 与 order by 使用相同的索引
- order by 的顺序与索引顺序相同
- order by 字段要么统一升序，要么统一降序

<br />

## 4、filesort
- `一次扫描算法`：一次性所有满足条件的所有字段，放在`排序区 sort buffer`中进行排序。内存开小较大，但是相比两次扫描算法，效率更高
- 两次扫描算法：mysql4.1之前。现根据条件拿到排序字段与行指针信息，然后在排序区进行排序。如果排序区不够，则在临时表中存储排序结果。完成排序后，再根据行指针读取记录。
- mysql判断系统变量 max_length_for_sort_data 的大小选取一次扫描算法还是两次扫描算法。所以可以适当提高 `sort_buffer_size`和`max_length_for_sort_data`系统变量，增加排序区的容量，提高排序效率。

<br />

## 5、group by
group by 也会进行排序操作。与 order by 相比， group by 多了 排序之后的分组操作。在分组时候还使用到了其他的一些聚合函数。在group by的实现过程中，与order by一样可以使用到索引

### 1、只分组，不排序
- `a`字段没有索引的情况
```sql
    # 分组 + 排序
    # Using temporary；Using filesort
    select a, count(a) from tb group by a

    # 分组，不对a进行排序，可以省去filesort过程
    # Using temporary
    select a, count(a) from tb group by a order by null
```
- `a`字段有索引的情况
```sql
    # 分组，不对a进行排序
    # Using index
    select a, count(a) from tb group by a order by null
```

<br />

## 6、嵌套查询
- 子查询可以被更高效的`join`替代
- 以下两种写法是等价的
```sql
    # 隐式内连接
    select * from a,b where a.id = b.id
    # 显式内连接
    select * from a inner join b on a.id = b.id
```
<br />

## 7、or
- 保证 or 两边的字段都走索引
- 无法用到复合索引。比如 idx_a_b，对于 `a or b` 是不走索引
- 使用 `union` 来替换 `or`

```sql
    # range
    select * from tb where id = 1 or id = 2
    # const
    select * from tb where id = 1 union select * from tb where id = 2
    # range
    select * from tb where id in (1,2)
```
- 假设存在索引 idx_a_c
```sql
    # index_merge
    select * from tb where a = xxx or id = xxx

    # const(id) + ref(a)
    select * from tb where a = xxx union select * from tb where id = xxx
```

## 8、limit
- `select * from tb limit 2000000, 10`
-  需要对 2000010 条记录进行排序，查询、排序代价很大
- 优化方式一
```sql
    # 在id主键上进行排序操作
    select id from tb order by id limit 2000000, 10;
    # 根据id回表查
    select * from tb where id in (xxx)

    # 对比
    # 4.5s
    # all
    select * from tb limit 2000000, 10;
    # 2.9s
    # index[id字段排序，遍历整个索引树]、eq_ref[t1.id = t2.id 连表查询到一条记录]
    select * from tb t1, (select id from tb order by id limit 2000000, 10) t2 where t1.id = t2.id;
```
- 优化方式二：适用于id自增的情况。把limit转化为id
```sql
    # 0s
    # range
    select * from tb where id > 2000000 limit 10
```

## 9、sql提示
- use index
  - 建议使用指定索引
  - `select * from tb use index(idx_name) where condition`
- ignore index
  - 禁止使用指定索引
  - `select * from tb ignore index(idx_name) where condition`
- force index
  - 强制使用指定索引
  - 即使这个索引效率慢
  - `select * from tb force index(idx_name) where condition`