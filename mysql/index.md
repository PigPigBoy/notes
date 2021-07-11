## 概述
- 索引是帮助MySQL高效获取数据得`数据结构`
- 如下图，左侧查询效率为O(1)，右侧为O(logn)
![初识索引](/others/pictures/idx1.jpg)

## 优劣势
- 能减少查询次数，降低IO成本，CPU消耗
- 增删改时，要更新索引信息。
- 索引其实也是一张表，需要额外空间来保存

## 索引结构

索引|InnoDB|MyISAM|Memory
:-:|:-:|:-:|:-:
B-Tree|√|√|√
Hash|×|×|√
R-Tree|×|√|×
Full-Text|>=5.6|√|×

我们所说得索引，默认是B+树索引。其中**聚合索引**、**复合索引**、**前缀索引**、**唯一索引**默认都是B+树索引，统称为索引

---

## B-Tree索引
- [B-树](/data-structure/BTree.md) [见数据结构专题]
- 在Mysql中，对B+Tree进行了优化，即`叶子节点之间增加了指针`。可以称之为`带顺序指针的B+树`。增加指针的主要目的就是便于范围搜索，`提高区间访问的性能`。如图，我想查找9~15之间的数据

![](/others/pictures/B+Tree_Mysql.jpg)

## 索引分类
- 单值索引
  - 一个索引只包含一个列
- 唯一索引
  - 索引值必须唯一，允许存在`多`个空值
- 复合索引
  - 一个索引包含多个列

## 语法
- 创建
  - create [unique|fulltext|spatial] index idx_name [using index_type] on table (...columns) 
  - alter table table_name add [unique|fulltext|spatial] idx_name (... columns)
- 查看
  - show index from table
- 删除
  - drop index idx_name on table

## **`设计原则`**
- 查询频次高，数据量大
- where条件
- 尽量使用唯一索引
- 索引过多，对于增删改频繁的表来说，降低效率
- 使用`短`字段索引，这是为了提高索引访问的IO效率
- 利用最左前缀