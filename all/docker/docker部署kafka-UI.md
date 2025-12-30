docker-compose.yml
```yml
services:
  kafka-ui:
	## 指定容器名
	container_name: kafka-ui
	## 对应额镜像
	image: provectuslabs/kafka-ui:latest
	## 端口暴漏
	ports:
	  - 8082:8080
	## 环境变量
	environment:
	  DYNAMIC_CONFIG_ENABLED: 'true'
	  ## ！！配置反向代理servlet初始化路径
	  SERVER_SERVLET_CONTEXT_PATH: /kafbat-ui
	## 挂载卷
	volumes:
	  - ~/kui/config.yml:/etc/kafkaui/dynamic_config.yaml
	## 网络
	networks:
	  - nginx-ssl_default
networks:
  ## 定义的网络的名字
  nginx-ssl_default:
	## 如果外部已经创建了则使用这个
	external: true
```


