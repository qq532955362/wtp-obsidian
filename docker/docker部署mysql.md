```sh
## 获取指定版本的mysql镜像
docker pull mysql:8.0.26
## 运行
docker run -d \
  --name mysql8 \
  -e MYSQL_ROOT_PASSWORD=qq532955362 \
  -e MYSQL_DATABASE=erp \
  -p 3306:3306 \
  -v /opt/mysql/data:/var/lib/mysql \
  mysql:8.0.26
```