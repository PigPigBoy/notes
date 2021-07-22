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
配置文件在 `/usr/my.cnf`

### Master配置
```sh
# mysql的服务ID，保证整个集群环境中唯一
server-id=1

# mysql binlog 日志的存储路径与文件名
# 因为主从是基于二进制日志，所以一定要开启
log-bin=/var/lib/mysql/mysqlbin

# 是否只读，1代表只读，0代表是读写
read-only=0

# 主从复制时，忽略的数据库
binlog-ignore-db=mysql

# 以下是非必须的

# 错误日志，默认开启
# log-err

# mysql安装目录
# basedir

# mysql临时目录
# tmpdir

# mysql数据存放目录
# datadir

# 指定同步的数据库
# binlog-do-db=db01
```

### 重启mysql
```sh
service mysql restart
```

### 创建账户，用来进行数据同步
```sh
# 创建xjc账户，在所有的数据库、所有的表上主从复制的权限
# 两个*表示所有的
# 只能在 slave_host 这台机器上可以访问
# 密码是 pwd
grant replication slave on *.* to 'xjc'@'slave_host' identified bt '1234'
# 刷新权限列表
flush privileges
```

## 查看Master状态
```sh
show master status
```
![](/others/pictures/show_master_status.jpg)

字段含义
- File：从哪个日志文件开始推送
- Position：从哪个位置开始推送
- Binlog_Ignore_DB：不需要推送的数据库
  
<br />
<hr />
<br />

### Slave配置
```sh
# mysql的服务ID，全局唯一
server-id=2

# 指定binlog日志
log-bin=/var/lib/mysqlbin
```
### 重启mysql
```sh
service mysql restart
```

### 指定主节点、同步的账户、哪个文件、哪个位置开始同步
```sh
change master to master_host='master_host', master_user='xjc',master_password='1234', master_log_file='mysqlbin.000001',master_log_pos=413;
```

### 开启同步
```sh
# 开启同步
start slave
# 查看状态
# 需要关注两个地方： Slave_IO_Running 和 Slave_SQL_Running
show slave status
show slave status\G
```