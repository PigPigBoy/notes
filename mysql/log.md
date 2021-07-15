# mysql日志

## 类型
- 错误日志
  - 默认`开启`
  - 默认存放位置为mysql的数据目录 `/var/lib/mysql`
  - 默认的日志文件名是 `hostname.err`
  - 查看日志的位置指令 `show variables like 'log_err%'`
- 二进制日志
  - 默认`关闭`，需要在`/usr/my.cnf`配置文件开启，并配置mysql日志格式
    - binlog的三种格式（默认 `MIXED`）
      - STATEMENT
        - `mysqlbinlog mysqlbin.000001`
        - 记录SQL语句，每一条对数据修改的SQL都会记录在日志文件中
        - 主从复制的时候，从库会将日志解析为原文本，并再从库重新执行一次
      - ROW
        - `mysqlbinlog -vv mysqlbin.000002`
        - 记录行的数据变化，而不保存sql语句
      - MIXED
        - 混合了STATEMENT 与 ROW，避开了各自的缺点
        - 一般使用STATEMENT
        - 特殊情况采用ROW
  - 默认存放位置为mysql的数据目录
  - 记录了DDL和DML语句
  - 不包含select语句
  - 灾难恢复起到极其重要的作用
  - 日志删除
    - 方式一：`Reset Master` 删除全部的binlog日志。删除之后，日志编号从xxxx.000001重新开始
    - 方式二：`purge master logs to 'mysqlbin.xxxxxx'` 删除编号xxxxxx之前的日志文件
    - 方式二：`purge master logs before 'yyyy-mm-dd hh24:mi:ss'` 删除该日期之前的日志文件
    - 方式四：设置参数`--expire_logs_days=n`过期时间，n天后自动删除
- 查询日志
  - 记录了所有操作，不仅查询，也包括增删改
  - 默认`关闭`
    - 控制开关：`general_log=1/0`
    - 设置文件名，默认host_name.log：`general_log_file=file_name`
- 慢查询日志
  - 默认关闭
  - 参数 `long_query_time` 时间阈值，默认10秒，可以精确到微秒
  - 参数 `min_examined_row_limit` 扫描行数阈值
  - 开启慢查询日志： `slow_query_log = 1`
  - 慢查询日志的文件名：`slow_query_log_file=slow_query.log`
  - 时间限制 `long_query_time = 10`
  - 实时刷新文件尾部： `tail -f slow_query.log`
  
<br />

查看日志内容
```shell
tail -f /var/lib/mysql/xxx.err
```