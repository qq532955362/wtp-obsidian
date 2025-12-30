```shell
## 1.数据库初始化脚本去github上获取（官网可以跳转）
docker search nacos
docker pull {次数最多的那个nacos-server}
## 运行 【注意数据库参数useSSL=false&allowPublicKeyRetrieval=true】这两个本地连接时需要
docker run --name nacos-standalone-mysql \
-e MODE=standalone \
-e NACOS_AUTH_TOKEN={cXE1MzI5NTUzNjJxcTUzMjk1NTM2MnFxNTMyOTU1MzYy:原始长度大于32字符的base64} \
-e NACOS_AUTH_IDENTITY_KEY={自定义的key:wtp-nacos-key} \
-e NACOS_AUTH_IDENTITY_VALUE={自定义的value:wtp-nacos-value} \
-e spring.sql.init.platform=mysql \
-e MYSQL_SERVICE_HOST=host.docker.internal \
-e MYSQL_SERVICE_DB_NAME={你的初始化sql所在的数据库} \
-e MYSQL_SERVICE_PORT=3306 \
-e MYSQL_SERVICE_USER={你的数据库用户名} \
-e MYSQL_SERVICE_PASSWORD={你的数据库用户的密码} \
-e MYSQL_SERVICE_DB_PARAM='useUnicode=true&characterEncoding=utf8&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=Asia/Shanghai'  \
-p 8080:8080 -p 8848:8848 -p 9848:9848 \
-d {你刚刚pull的镜像}
## 以上带有大括号的参数都去掉 直接填写真实值就行不用单引号除非你的变量中有特殊字符
```

    docker search nacos
    docker pull nacos/nacos-server:latest
    ## 运行 【注意数据库参数useSSL=false&allowPublicKeyRetrieval=true】这两个本地连接时需要
    docker run --name nacos-standalone-mysql \
    -e MODE=standalone \
    -e NACOS_AUTH_TOKEN=cXE1MzI5NTUzNjJxcTUzMjk1NTM2MnFxNTMyOTU1MzYy \
    -e NACOS_AUTH_IDENTITY_KEY=wtp-nacos-key \
    -e NACOS_AUTH_IDENTITY_VALUE=wtp-nacos-value \
    -e spring.sql.init.platform=mysql \
    -e MYSQL_SERVICE_HOST=mysql \
    -e MYSQL_SERVICE_DB_NAME=nacos \
    -e MYSQL_SERVICE_PORT=3306 \
    -e MYSQL_SERVICE_USER=root \
    -e MYSQL_SERVICE_PASSWORD=qq532955362 \
    -e MYSQL_SERVICE_DB_PARAM='useUnicode=true&characterEncoding=utf8&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=Asia/Shanghai'  \
    -p 8080:8080 -p 8848:8848 -p 9848:9848 \
    --network bridge \
    -d nacos/nacos-server:latest

