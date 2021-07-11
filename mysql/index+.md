数据结构可视化地址 https://www.cs.usfca.edu/~galles/visualization/Algorithms.html
# 索引为什么要使用B+树
## 1. 哈希
### （1）效率高 
### （2）无法进行范围查询、排序【因为哈希值是无序的】

## 2. 平衡二叉树
### （1）随着高度增加，查找速度越来越慢
### （2）无法进行范围查询、排序【因为需要回旋】

## 3. B树
### （1）减少了高度增加影响查询效率
### （2）无法进行范围查询、排序【因为需要回旋】

## 4. B+树
### （1）减少了高度增加影响查询效率
### （2）解决了无法进行范围查询、排序【因为叶子节点之间存在指针相互引用】

<br />

# 索引为什么会失效
## 1、联合索引怎么排序的？
### （1）按照从左往右，从小到大进行排序
## 2、范围查找的右边为什么会失效？like查询%开头为什么会失效？
```
假设有一个联合索引 (a,b)
先是给a排序,a相等的情况下,根据b排序。

如果给的where条件符合了最左匹配原则,where a = xxx and b = xxx
这样mysql通过二分查找,快速定位到a,因为a是有序的。定位到a后，又能快速定位到b,因为在a相等的情况下,b也是有序的。这样b也能进行二分查找快速定位。

如果条件不符合二分查找,即where b = xxx
那么a是无法快速定位,只能全表扫描,挨个挨个匹配b。

即,右边的依赖左边的,左边无法精准定位,那么就会导致索引失效
```

<br />

# 索引正确使用
### 假设当前有以下索引
- `idx_a_b_c`
## 1、全值匹配
```sql 
    select * from tb where a= xxx and b = xxx and c = xxx;
```
<br />

## 2、最左前缀法则
## `where后面字段顺序无关，只与是否存在字段有关。即通过重排序后，能遵循从左到右依次匹配即可`
```sql
    # 生效的情况
    # 索引更被充分利用，索引长度key_len会不断变大
    select * from tb where a = xxx
    select * from tb where a = xxx and b = xxx
    select * from tb where a = xxx and b = xxx and c = xxx
    # 也走索引，因为优化器会优化顺序
    select * from tb where b = xxx and c = xxx and a = xxx 
```
```sql
    # 失效 的情况
    select * from tb where b = xxx and c = xxx
    select * from tb where c = xxx
```
```sql
    # 生效的特殊情况
    # 索引a被用到了，b、c没有被用到
    select * from tb where a = xxx and c = xxx
    # 该sql与下面一句是等价的
    select * from tb where a = xxx
```
<br />

## 3、范围查询
## `范围查询右边的列不会走索引`
```sql
    # 只能走索引a、b
    select * from tb where a = xxx and b > xxx and c = xxx
    # 等价于
    select * from tb where a = xxx and b > xxx
```
<br />

## 4、索引字段禁止做运算操作
<br />

## 5、字符串不加`引号`将造成索引失效
## `经测试验证：单引号与双引号是一样效果的`
<br />

## 6、覆盖索引
## `尽量使用覆盖索引，减少使用select *`
### mysql先是索引树的查询，如果索引不包含查询的列，那么会扫描行[索引回表查询]，降低性能

```sql
    # 执行计划 extra会发生改变
    # Using index condition：需要进行回表查询
    select * from tb where a = xxx and b = xxx and c = xxx
    # Using where, Using index：直接查索引就能把数据查出来
    select a,b,c from tb where a = xxx and b = xxx and c = xxx
```
<br />

## 7、or
## `如果or前有索引，后面没有索引，将索引失效`

```sql
    select * from tb where a = xxx or d = xxx
```
<br />


## 8、模糊匹配
## `like % 开头 索引会失效`
## 解决方法：`覆盖索引`
```sql
    # 不走索引
    select * from tb where a like "%xxx"
    # 覆盖索引
    select id from tb where a like "%xxx%"
    select id, a from tb where a like "%xxx%"
    select a,b from tb where a like "%xxx%"
    # 覆盖索引失效
    select d from tb where a like "%xxx%"
```
<br />

## 9、全表扫描更快
## `mysql发现全表扫描更快，那么将会全表扫描`
<br />

## 10、NULL值判定
## `索引是否失效看具体情况`

- 个人理解为上一条的特例
- 空字段极少，is null 会走索引，is not null 就不会走索引。反之亦然

<br />

## 11、in与not in
## `in走索引，而not in索引失效`
```sql
    # 用到了主键索引
    select * from tb where id in (xxx, yyy, zzz)
    # 没有用到索引
    select * from tb where id not in (xxx, yyy, zzz)
```

<br />


# 索引的选择
## 单列索引与复合索引
- 尽量使用`复合索引`

<br />

# 索引是使用情况
- 起到参考的作用
- 当前会话索引使用情况：`show status like 'Handler_read%'`
- 全局索引使用情况：`show global status like 'Handler_read%'`
- 每个参数具体什么意思，了解即可

![](/others/pictures/handler_read.jpg)