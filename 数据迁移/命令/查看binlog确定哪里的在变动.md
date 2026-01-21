```sql
show binary logs;
```

```sh
mysqlbinlog --base64-output=decode-rows binlog.000301 | tail -n 1000
```


