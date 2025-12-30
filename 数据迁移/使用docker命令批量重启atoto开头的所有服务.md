```mysql

-- 设置变量
set @source_str = 'jdbc:mysql://192.168.31.5:3306';
set @target_str = 'jdbc:mysql://192.168.31.5:3333';
set @target_ns = '9a5c843f-a972-4162-973d-ba8412cb430d';
set @spring_profile = 'local';

-- 连接了数据库的服务对应的docker 服务名查找语句
SELECT
  CONCAT(
    'docker ps ',
    GROUP_CONCAT(DISTINCT CONCAT('--filter "name=', REPLACE(data_id, CONCAT('-', @spring_profile, '.yml'), ''), '"')
    SEPARATOR ' '
    )
  ) as result
FROM
  config_info
WHERE
  tenant_id = @target_ns
  AND content LIKE CONCAT('%', @source_str, '%');
-- ! 保存下来给后面的docker批量重启使用

```

```sh
## 这里是上一步输出的内容
docker restart $(docker ps -q --filter "name=atoto")
```

