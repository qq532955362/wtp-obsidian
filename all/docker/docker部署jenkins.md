`docker-compose.yml`

```yml
version: '3'
services:
  jenkins:
    ## 官方镜像
    image: jenkins/jenkins
    ## 指定容器名
    container_name: jenkins
    ## 指定root用户让容器内的jenkins可以访问宿主机docker进程
    user: root
    environment:
      ## 指定整个jenkins后端默认带前缀jenkins用于多模块转发
      - JENKINS_OPTS=--prefix=/jenkins
      - JENKINS_UC=https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
      - JENKINS_UC_DOWNLOAD=https://mirrors.tuna.tsinghua.edu.cn/jenkins
    ## 指定jenkins网络 用于容器间互相通信
    networks:
      - nginx-network
    ## jenkins的端口如果占用换一个宿主机端口
    ## 不暴露端口使用docker network进行通信
    ##ports:
      ##- "8081:8080"   # 改成其他可用端口
      ##- "50000:50000"
    ## jenkins工作空间挂载
    volumes:
      - /srv/jenkins_home:/var/jenkins_home
      ## 这是jenkins容器访问宿主机docker进程的关键
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/bin/docker:/usr/bin/docker
      - /usr/local/bin/docker-compose:/usr/local/bin/docker-compose
    ## 意外重启
    restart: always
## 定义网络
networks:
  ## 定义的网络的名字
  nginx-network:
    ## 如果外部已经创建了则使用这个
    external: true

```

`运行之后在docker logs中查看初始密码`

```sh

docker logs [containerId]


#输出类似：

*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

36c0cfdd924641f1a7693ce07ff9dad5

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************
```

