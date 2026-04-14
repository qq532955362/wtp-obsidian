
## docker run

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


## docker compose

```yml
services:
  mysql: 
    image: mysql:8.0.26
    container_name: mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=qq532955362
      - MYSQL_DATABASE=wtp
    networks:
      - nginx-network
    volumes:
	  - /opt/mysql/data:/var/lib/mysql
networks:
  ## 定义的网络的名字
  nginx-network:
    name: nginx-network
    ## 如果外部已经创建了则使用这个
    external: true
```