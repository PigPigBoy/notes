# Mysql常用工具

## 1. mysql
```
参数：
    -u 用户
    -p 密码
    -h host
    -p port
    -e 后面接sql语句，执行完立即退出，一般用于脚本
示例：
    mysql -h 127.0.0.1 -p 3306 -u root -p 1234 -e "select * from tb"
```
<br />

## 2. mysqladmin
- mysqladmin是一个执行管理操作的客户端程序
- 用于检查服务器的配置和当前状态、创建删除数据库等
- 也是用于shell脚本

```
    mysqladmin -u root -p 1234 create 'my_test_db'
    mysqladmin -u root -p 1234 version
```

<br />

## 3. mysqlbinlog
- 将二进制日志输出成给人看的文本

<br />

## 4. mysqldump
- 用于备份或者数据迁移

<br />

## 5. mysqlimport
- 用于导入文档
- 比如导入 mysqldump -T后导出的文本文件
```
    mysqlimport -uroot -p1234 test /tmp/my_dump.txt
```

<br />

## 6. source
- 与 mysqlimport 类似也是导入功能
- 导入 sql文件
```
    source /root/tb_book.sql
```

<br />

## 7. mysqlshow
- 查找工具
- 查找存在哪些数据库、数据库的表、表中的列或索引
```
    # 查询每个数据库中表的数量以及记录的数量
    mysqlshow -uroot -p1234 --count

    # 查询test库的book表的详细情况
    mysqlshow -uroot -p1234 test book --count
```