## 测服

修改dockercompose

在`test1`的`/root/docker-compose/traccar-docker-compose.yml`

修改配置文件中的地址

```sh
vim /home/mydata/traccar/traccar.xml
```

```yml
services: 
  traccar: 
    image: traccar/traccar:latest
	container_name: traccar
	hostname: traccar
	restart: unless-stopped
	environment:
	  - DB_CREATE=false
	ports: 
	  - "8082:8082"
	  - "5000-5150:5000-5150"
	  - "5000-5150:5000-5150/udp"
	volumes: 
	  - /home/mydata/traccar/logs:/opt/traccar/logs:rw
	  - /home/mydata/traccar/traccar.xml:/opt/traccar/conf/traccar.xml:ro
```

### **遇到的问题**

```sh
[root@usa ~]# docker compose -f /root/docker-compose/traccar-docker-compose.yml up -d
WARN[0000] Found orphan containers ([elasticsearch-7-new]) for this project. If you removed or renamed this service in your compose file, you can run this command with the --remove-orphans flag to clean it up. 
[+] Running 0/1
 ⠴ Container traccar-compose  Starting                                                                                                                                                                                                                                                                                                                                           0.5s
Error response from daemon: driver failed programming external connectivity on endpoint traccar-compose (1e3d1c3b2994c9768aeea12411c35f3759ccaf9e8fc2f56c4ddf758d568e3c90): Error starting userland proxy: listen tcp4 0.0.0.0:5142: bind: address already in use
```

出现之后解决办法

```sh
sudo pkill -f 'docker-proxy.*host-port 5142'
```



## 生产


修改dockercompose

在`prod2`的`/root/docker-compose/traccar-docker-compose.yml`

`修改配置文件中的地址、用户名和密码`

```sh
vim /home/mydata/traccar/traccar.xml
```

```yml
services: 
  traccar: 
    image: traccar/traccar:latest
	container_name: traccar
	hostname: traccar
	restart: unless-stopped
	ports: 
	  - "8082:8082"
	  - "5000-5150:5000-5150"
	  - "5000-5150:5000-5150/udp"
	volumes: 
	  - /home/mydata/traccar/logs:/opt/traccar/logs:rw
	  - /home/mydata/traccar/traccar.xml:/opt/traccar/conf/traccar.xml:ro
```



