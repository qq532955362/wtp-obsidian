## 测服

修改dockercompose中的数据库地址

在`test1`的`/root/docker-compose/xxl-job-docker-compose.yml`

```yml
services:
  xxl-job-admin:
    image: xuxueli/xxl-job-admin:2.3.0
    container_name: xxl-job-admin-compose
    ports:
      - "8081:8080"
    volumes:
      - /home/mydata/xjob/log:/data/applogs
    environment:
      - PARAMS=--spring.datasource.url=jdbc:mysql://pc-rj9yb9xt16gbo74hn.rwlb.rds-aliyun-america.rds.aliyuncs.com:3306/timer-job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=application --spring.datasource.password=VCQEmY_DWlYOl)rA(DdHX
    restart: unless-stopped
```



## 生产

修改dockercompose中的数据库地址

在`prod2`的`/root/docker-compose/xxl-job-docker-compose.yml`

```yml
services:
  xxl-job-admin:
    image: xuxueli/xxl-job-admin:2.3.0
    container_name: xxl-job-admin-compose
    ports:
      - "8081:8080"
    volumes:
      - /home/mydata/xjob/log:/data/applogs
    environment:
      - PARAMS=--spring.datasource.url=jdbc:mysql://pe-rj9be6sroo6bk0g44.rwlb.rds-aliyun-america.rds.aliyuncs.com:3306/timer-job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=xxljob --spring.datasource.password=atoto_xxljob_123456
    restart: unless-stopped
```

