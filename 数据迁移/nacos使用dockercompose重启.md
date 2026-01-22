## 测服

修改挂载的配置文件

#### **`注意这里和生产不一样`**

`/home/nacos/conf/application.properties`

修改compose文件到


`/root/docker-compose/nacos-docker-compose.yml`

```yml
services:
  nacos:
    image: nacos/nacos-server:2.0.2
    container_name: nacos-compose
    hostname: 537d9c4024fd
    ports:
      - "8848:8848"
    volumes:
      - /home/nacos/conf:/home/nacos/conf
      - /home/nacos/data:/home/nacos/data
	  - /home/nacos/logs:/home/nacos/logs
    environment:
      - MODE=standalone
    working_dir: /home/nacos
    networks:
      - default
    restart: always

networks:
  default:
    name: nacos_default
    external: true

```




## 生产

修改挂载的配置文件

`prod2`

`/home/nacos/conf/application.properties`

修改compose文件到

`/root/docker-compose/nacos-docker-compose.yml`

```yml
services:
  nacos:
    image: nacos/nacos-server:2.0.2
    container_name: nacos-compose
    hostname: 537d9c4024fd
    ports:
      - "8848:8848"
    volumes:
      - /home/nacos/conf:/home/nacos/conf
      - /home/nacos/data:/home/nacos/data
	  - /home/nacos/logs:/home/nacos/logs
    environment:
      - MODE=standalone
    working_dir: /home/nacos
    networks:
      - default
    restart: always

networks:
  default:
    name: nacos_default
    external: true

```




