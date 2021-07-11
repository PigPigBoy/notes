# 内存管理

## 1. 内存优化原则
### （1）保证操作系统与其他程序内存够用的情况下，尽可能的给mysql分配更多的内存
### （2）MyISAM存储引擎的数据文件读取依赖于操作系统自身的IO缓存。因此，如果有大量的MyISAM表们就要`预留更多的内存给操作系统`做IO缓存
### （3）每一次Session会话（客户端的连接）都有一块专用的内存，做排序区、连接区等缓存使用。所以每一个连接的内存不能设置过大，要根据最大连接数合理分配。如果设置太大，不但浪费资源，更有可能在并发连接数高的情况下，导致物理内存耗尽。

<br />

## 2. MyISAM 内存优化（了解）
### （1）MyISAM的缓存机制
- `key_buffer`缓存索引块，注意，具体的数据块没有特别做缓存
- 对于具体的数据块，mysql没有缓存机制，完全依赖于操作系统的IO缓存。
### （2）参数
- `key_buffer_size`
  - 索引块缓冲区的大小
  - 建议配置为物理内存的 `1/4`
  - 查看默认大小：`show variables like key_buffer_size`
  - 默认8M
  - /usr/my.cnf：`key_buffer_size = 512M`
- `read_buffer_size`
  - 针对顺序扫描，可以增大来改善性能
  - 注意这是session级别的，不可以设置过大
- `read_rnd_buffer_size`
 - 针对排序操作（比如 order by），可以增大来改善性能
 - 注意这是session级别的，不可以设置过大

<br />

## 3. InnoDB 内存优化（掌握）
### （1）InnoDB的缓存机制
- 不论是`索引块`还是`数据块`，都是缓存在IO缓存池中
- 优化思路：分配更大的内存给InnoDB做IO缓存池

### （2）参数
- `innodb_buffer_pool_size`
  - 值越大、缓存命中越高，磁盘IO越少，性能越高
  - 默认128M
  - `innodb_buffer_pool_size = 512M`
- `innodb_log_buffer_size`
  - 重做日志缓存大小
  - 大数据量、大事务的时候，避免事务提交前执行不必要的日志写磁盘
  - `innodb_log_buffer_size = 10M`
