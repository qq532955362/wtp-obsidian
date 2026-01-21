## 测服

### 修改dockercompose中的参数 
在`test2`的`/home/mydata/docker-compose/flowable-compose`

#### 数据库地址

```yml
services:
  flowable-ui:
    image: flowable/flowable-ui:6.7.2
    container_name: flowable-ui
    ports:
      - "9023:9023"
    restart: always
    environment:
      - SERVER_PORT=9023
      - SPRING_DATASOURCE_DRIVER-CLASS-NAME=com.mysql.cj.jdbc.Driver
	    ## 这里
      - SPRING_DATASOURCE_URL=jdbc:mysql://172.20.242.120:3306/flowable?allowPublicKeyRetrieval=true&useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
        ## 这里
      - SPRING_DATASOURCE_USERNAME=root
        ## 这里
      - SPRING_DATASOURCE_PASSWORD=203e294a-e5c1-4d24-9fa4-decfe3a4e088
    volumes:
      - ./mysql-connector-java-8.0.20.jar:/app/WEB-INF/lib/mysql-connector-java-8.0.20.jar


```



## 生产

### 修改dockercompose中的参数 
在`prod1`的`/mydata/docker-compose/flowable-docker-compose`

```yml
services:
  flowable-ui:
    image: flowable/flowable-ui:6.7.2
    container_name: flowable-ui
    ports:
      - "9023:9023"
    restart: always
    environment:
      - SERVER_PORT=9023
      - SPRING_DATASOURCE_DRIVER-CLASS-NAME=com.mysql.cj.jdbc.Driver
	  ## 这里
      - SPRING_DATASOURCE_URL=jdbc:mysql://172.20.242.120:3306/flowable?allowPublicKeyRetrieval=true&useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
	  ## 这里
      - SPRING_DATASOURCE_USERNAME=root
      ## 这里
      - SPRING_DATASOURCE_PASSWORD=203e294a-e5c1-4d24-9fa4-decfe3a4e088
    volumes:
      - ./mysql-connector-java-8.0.20.jar:/app/WEB-INF/lib/mysql-connector-java-8.0.20.jar

```
