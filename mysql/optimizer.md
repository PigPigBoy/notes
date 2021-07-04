# SQL优化步骤

## 1. 查看SQL执行频率
```sql
# 当前连接统计
show status like "Com_%";

# 全局统计
show global status like "Com_%";

# 针对InnoDB表统计
show status like "Innodb_rows_%";
```

### 关心的几个参数
参数|含义
:-:|:-:
Com_select|select次数，一次查询+1
Com_insert|insert次数，批量插入也是+1
Com_update|update次数
Com_delete|delete次数
Innodb_rows_read|select查询返回的行数
Innodb_rows_inserted|insert插入的行数
Innodb_rows_updated|update更新的行数
Innodb_rows_deleted|delete删除的行数
Connections|试图连接Mysql的次数
Uptime|服务器的工作时间
Slow_queries|慢查询次数