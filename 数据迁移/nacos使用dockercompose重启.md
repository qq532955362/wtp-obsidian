## 测服

修改挂载的配置文件

#### 注意这里和生产不一样

`/home/nacos/conf/application.properties`

```properties
# spring
server.servlet.contextPath=${SERVER_SERVLET_CONTEXTPATH:/nacos}
server.contextPath=/nacos
server.port=${NACOS_APPLICATION_PORT:8848}
spring.datasource.platform=mysql
nacos.cmdb.dumpTaskInterval=3600
nacos.cmdb.eventTaskInterval=10
nacos.cmdb.labelTaskInterval=300
nacos.cmdb.loadDataAtStart=false
db.num=1
db.url.0=jdbc:mysql://172.20.242.122:3306/nacos?characterEncoding=utf8&serverTimezone=Asia/Shanghai&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false
db.user=root
db.password=atoto_123456_20260115
### The auth system to use, currently only 'nacos' is supported:
nacos.core.auth.system.type=${NACOS_AUTH_SYSTEM_TYPE:nacos}


### The token expiration in seconds:
nacos.core.auth.default.token.expire.seconds=${NACOS_AUTH_TOKEN_EXPIRE_SECONDS:18000}

### The default token:
nacos.core.auth.default.token.secret.key=${NACOS_AUTH_TOKEN:SecretKey0123456789012nyd6789012345678gbtd34567yh0123456789012nyg6789}

### Turn on/off caching of auth information. By turning on this switch, the update of auth information would have a 15 seconds delay.
nacos.core.auth.caching.enabled=${NACOS_AUTH_CACHE_ENABLE:false}
nacos.core.auth.enable.userAgentAuthWhite=${NACOS_AUTH_USER_AGENT_AUTH_WHITE_ENABLE:false}
nacos.core.auth.server.identity.key=${NACOS_AUTH_IDENTITY_KEY:ufskf11sjf1}
nacos.core.auth.server.identity.value=${NACOS_AUTH_IDENTITY_VALUE:mkfjsd34723nsdjfy3274bsdjk7832}
server.tomcat.accesslog.enabled=${TOMCAT_ACCESSLOG_ENABLED:false}
server.tomcat.accesslog.pattern=%h %l %u %t "%r" %s %b %D
# default current work dir
server.tomcat.basedir=
## spring security config
### turn off security
nacos.security.ignore.urls=${NACOS_SECURITY_IGNORE_URLS:/,/error,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.ico,/console-fe/public/**,/v1/auth/**,/v1/console/health/**,/actuator/**,/v1/console/server/**}
# metrics for elastic search
management.metrics.export.elastic.enabled=false
management.metrics.export.influx.enabled=false

nacos.naming.distro.taskDispatchThreadCount=10
nacos.naming.distro.taskDispatchPeriod=200
nacos.naming.distro.batchSyncKeyCount=1000
nacos.naming.distro.initDataRatio=0.9
nacos.naming.distro.syncRetryDelay=5000
nacos.naming.data.warmup=true

```

修改compose文件到

`/root/docker-compose/nacos-docker-compose.yml`

```yml
services:
  nacos:
    image: nacos/nacos-server:2.0.2
    container_name: nacos
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

`/home/nacos/conf/application.properties`

```properties
# spring
server.servlet.contextPath=${SERVER_SERVLET_CONTEXTPATH:/nacos}
server.contextPath=/nacos
server.port=${NACOS_APPLICATION_PORT:8848}
spring.datasource.platform=mysql
nacos.cmdb.dumpTaskInterval=3600
nacos.cmdb.eventTaskInterval=10
nacos.cmdb.labelTaskInterval=300
nacos.cmdb.loadDataAtStart=false
db.num=1
db.url.0=jdbc:mysql://172.20.242.120:3306/nacos?useUnicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false
db.user=root
db.password=203e294a-e5c1-4d24-9fa4-decfe3a4e088
### The auth system to use, currently only 'nacos' is supported:
nacos.core.auth.system.type=${NACOS_AUTH_SYSTEM_TYPE:nacos}


### The token expiration in seconds:
nacos.core.auth.default.token.expire.seconds=${NACOS_AUTH_TOKEN_EXPIRE_SECONDS:18000}

### The default token:
nacos.core.auth.default.token.secret.key=${NACOS_AUTH_TOKEN:SecretKey012345678901234567890123456789012345678901234567890123456789}

### Turn on/off caching of auth information. By turning on this switch, the update of auth information would have a 15 seconds delay.
nacos.core.auth.caching.enabled=${NACOS_AUTH_CACHE_ENABLE:false}
nacos.core.auth.enable.userAgentAuthWhite=${NACOS_AUTH_USER_AGENT_AUTH_WHITE_ENABLE:false}
nacos.core.auth.server.identity.key=${NACOS_AUTH_IDENTITY_KEY:serverIdentity}
nacos.core.auth.server.identity.value=${NACOS_AUTH_IDENTITY_VALUE:security}
server.tomcat.accesslog.enabled=${TOMCAT_ACCESSLOG_ENABLED:false}
server.tomcat.accesslog.pattern=%h %l %u %t "%r" %s %b %D
# default current work dir
server.tomcat.basedir=
## spring security config
### turn off security
nacos.security.ignore.urls=${NACOS_SECURITY_IGNORE_URLS:/,/error,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.ico,/console-fe/public/**,/v1/auth/**,/v1/console/health/**,/actuator/**,/v1/console/server/**}
# metrics for elastic search
management.metrics.export.elastic.enabled=false
management.metrics.export.influx.enabled=false

nacos.naming.distro.taskDispatchThreadCount=10
nacos.naming.distro.taskDispatchPeriod=200
nacos.naming.distro.batchSyncKeyCount=1000
nacos.naming.distro.initDataRatio=0.9
nacos.naming.distro.syncRetryDelay=5000
nacos.naming.data.warmup=true
```


修改compose文件到

`/root/docker-compose/nacos-docker-compose.yml`

```yml
services:
  nacos:
    image: nacos/nacos-server:2.0.2
    container_name: nacos
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




