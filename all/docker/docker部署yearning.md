`docker-compose-yearning.yml`

```yml
version: '2.2'
services:
  yearning:
    container_name: yearning-compose
    image: yeelabs/yearning:latest
    environment:
      MYSQL_USER: root
      MYSQL_PASSWORD: qq532955362
      MYSQL_ADDR: mysql
      MYSQL_DB: yearning
      SECRET_KEY: ${必须16位} ## 必须是16位
      IS_DOCKER: is_docker
      Y_LANG: zh_CN
    ports:
      - 8001:8000
    command: /bin/bash -c "./Yearning install && ./Yearning run"
    restart: always
    networks:
      - nginx-network
networks:
  nginx-network:
    external: true
```