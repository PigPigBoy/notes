# id
- 序号
- 操作表的顺序
  - id大的先执行
  - id相等时，从上而下执行

# select_type
- select类型
- 从上到下，效率越来越慢
  - SIMPLE：简单查询，没有`子查询`或者`union`
  - PRIMARY：存在子查询，外层标记为该标识
  - SUBQUERY：子查询
  - DERIVED：放在临时表的子查询
  - UNION：union后面的select语句
  - UNION RESULT：多张表合并

# <font color=red>type</font>
- 访问类型
  - NULL： 不访问任何表，直接返回数据
  - system： 只有一行记录，const的特例
  - const：索引一次就找到了，只返回一条记录。比如主键索引、唯一索引
  - eq_ref：多表关联查询，只有一条记录
    - `select * from a,b where a.user_id= b.user_id`
  - ref：根据索引查询，有多条记录
    - 优化到这个级别就差不多了
    - `name`有索引，但不是唯一索引
    - `select * from a where name = 'xxx'`
  - range：索引范围查询。where之后有 `between`，`<`，`>`，`in`等操作
  - index：遍历了整个索引树
    - 这个是`all`：`select * from a`
    - 这个是`index`：`select id from a`
  - all：全表扫描

# extra
- 额外信息
  - using filesort<效率低>
    - 使用外部索引排序，不按照表内索引顺序排序
  - using temporary<效率低>
    - 临时表存储中间结果，常见 order by 与 group by
  - using index
    - 使用了覆盖索引，而没有访问数据行，效率不错

  - 举例
    - a字段是索引，b字段不是索引
    - using filesort
      - select * from t order by b
    - using index
      - select * from t order by a