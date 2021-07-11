# 体系结构

## 1、图解
![mysql体系结构](/others/pictures/system-structure.jpg)

- connectors
  - 客户端连接
- 第一层，连接层
  - connection pool
    - 客户端发起一个连接之后，需要在此获取线程
    - 在获取连接时，还需要进行一些 `认证`、`最大连接数`、`缓存` 等操作
- 第二层，服务层
  - management service 管理服务和工具组件
    - 数据备份与恢复
    - 集群
    - 安全
    - 系统配置 etc.
  - sql接口组件
    - dml、ddl、存储过程、函数、视图相关
  - parser 查询分析器组件
    - 解析、过滤 客户端sql语句
  - optimizer 优化器组件
    - sql优化器
  - caches & buffers 缓冲池组件
    - 缓存
- 第三层
  - pluggable storgae engines 存储引擎
    - 插件式存储引擎
- 第四层
  - file system 文件系统
    - 数据
    - 索引
    - 二进制日志、错误日志、查询日志、慢查询日志