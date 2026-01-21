`docker-compose-yearning.yml`

```yml
services:
  yearning:
	container_name: yearning
	image: yeelabs/yearning:latest
	environment:
	  MYSQL_USER: root
	  MYSQL_PASSWORD: qq532955362
	  MYSQL_ADDR: mysql
	  MYSQL_DB: yearning
	  SECRET_KEY: ${必须16位}
	  IS_DOCKER: is_docker
	  Y_LANG: zh_CN
    ports:
      - 8001:8000
      # 首次使用请先初始化
	command: /bin/bash -c "./Yearning install && ./Yearning run"
	restart: always
    networks:
	  - nginx-network
networks:
  nginx-network:
	external: true
```