`查询binlog参数`
```sql
show variables like '%bin_log%';
```

`查看当前binlog正在写入的文件`
```sql
SHOW MASTER STATUS;
```

`清理binlog到指定文件为止`
```sql
-- 删除到 binlog.005788 之前的（即保留当前和上一个）
PURGE BINARY LOGS TO 'binlog.005788';
```