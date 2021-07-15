# Mysql复制

## 概述
- 复制指的是，主库的DDL、DML操作通过二进制日志，传到从库，从库重新执行，从而使从库与主库的数据保持同步。
- MySQL支持一台主库同时向多台从库进行复制，从库也可以作为其他从库的主库，实现链路复制

## 过程
1. Master提交事务时，会把数据变更记录在binlog中
2. Master会将binlog推送到Slave的中继日志 Relay Log
3. Slave重做中继日志

## 优势
- 主库出现问题，从库可以提供服务
- 读写分离：主库更新，从库查询
- 从库执行备份，减少主库压力

## 搭建步骤
```sh
# mysql的服务ID，保证整个集群环境中唯一
server-id=1

# mysql binlog 日志的存储路径与文件名
log-bin=/var/lib/mysql/mysqlbin

# 错误日志，默认开启
# log-err
```