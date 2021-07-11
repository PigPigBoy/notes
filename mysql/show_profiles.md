# show profiles
- 通过 having_profiling 参数,可以查看是否支持
```sql
    select @@having_profile 
``` 
- 查看是否开启
```sql
    select @@profiling
```

- 默认是关闭的，可以在Session级别开启（当前会话有效）
```sql
    set profiling = 1
```

- 作用：记录一系列操作的耗时

![](/others/pictures/show_profiles.jpg)

```sql
    show profile [可选参数] for query 5
```
![](/others/pictures/show_profile.jpg)

- Sending data
  - mysql线程开始访问数据行到返回数据到客户端的过程
  - 进行大量的磁盘读取操作，耗时最长

```sql
    # 可选参数有 all、cpu、block io、context switch、page faults等
    show profile cpu for query 5
```

![](/others/pictures/show_profile_cpu.jpg)