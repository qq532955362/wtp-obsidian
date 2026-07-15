
##### 1 拉取nginx镜像
```sh
docker pull nginx
```

##### 2 新建文件夹存放nginx相关文件
```sh
mkdir -p /opt/nginx-ssl/{html,conf.d,certs}
```

##### 3 将ssl证书从证书机构下载下来传到 /opt/nginx/certs 目录中nginx的一般是 .key 和 .pem

```
[root@iZbp16f29fczq4g6g9vm52Z nginx-ssl]# ls -R
.:
certs  conf.d  html

./certs:
wtp.wang.key  wtp.wang.pem

./conf.d:
wtp-default.conf

./html:
```


##### 4 写下nginx的配置(文件稍后可以修改)

```sh
touch wtp-default.conf
vim wtp-default.conf
## 将下面你的文件写进去
```

`wtp-default.conf`

注意把对应的文件名改成你自己的

```conf
server {
    listen 80;
    server_name localhost;

    # HTTP 重定向到 HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name localhost;

    ssl_certificate     /etc/nginx/certs/server.crt;
    ssl_certificate_key /etc/nginx/certs/server.key;

    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;

    location / {
        proxy_pass http://nextChat:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
	

    # 示例: 反向代理后台应用
    # location /api/ {
    #     proxy_pass http://backend:8080;
    # }
}
```

`测试的文件`
```conf
server {
    listen 80;
    server_name localhost;
    # HTTP 重定向到 HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name localhost;
    ssl_certificate     /etc/nginx/certs/wtp.wang.pem;
    ssl_certificate_key /etc/nginx/certs/wtp.wang.key;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    
    location / {
        proxy_pass http://wtp-personal:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    location /jenkins/ {
        proxy_pass http://jenkins:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    
	
    # 示例: 反向代理后台应用
    # location /api/ {
    #     proxy_pass http://backend:8080;
    # }
}
```


##### 5 编写docker-compose文件

`docker-compose.yml`

注意把对应的文件名改成你自己的

```yml
version: '3.8'
services:
  nginx:
	networks:
	  - nginx-network
    image: nginx:latest
    container_name: nginx-ssl
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /opt/nginx-ssl/html:/usr/share/nginx/html:ro
      - /opt/nginx-ssl/conf.d:/etc/nginx/conf.d:ro
      - /opt/nginx-ssl/certs:/etc/nginx/certs:ro
      - /opt/nginx-ssl/logs:/var/log/nginx
    restart: always
networks:
  ## 定义的网络的名字
  nginx-network:
    name: nginx-network
	## 如果外部已经创建了则使用这个
    external: true
```
        
```
这里的目录映射是为了更好的移植性
你的当前目录应该是：
```
[root@iZbp16f29fczq4g6g9vm52Z nginx-ssl]# pwd
/opt/nginx-ssl
```
##### 6 启动容器
```
## 注意目录 pwd /opt/nginx-ssl/
docker compose up -d
```