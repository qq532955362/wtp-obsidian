docker-compose.yml
```yml
version: '3.7'

services:
  elasticsearch:
    image: elasticsearch:7.6.2
    container_name: elasticsearch-7-new
    restart: always
    environment:
      - ES_JAVA_OPTS=-Xms1024m -Xmx1024m
      - ES_SETTING_INDEX_SEARCH_SLOWLOG_THRESHOLD_QUERY_WARN=5s
      - ES_SETTING_INDEX_INDEXING_SLOWLOG_THRESHOLD_INDEX_WARN=5s
      - xpack.security.enabled=true
      - ELASTIC_PASSWORD=wtp-test-password
	  - xpack.security.http.ssl.enabled=false
      - xpack.security.transport.ssl.enabled=false
    ports:
      - "9201:9200"
      - "9301:9300"
    volumes:
      - /newdisk1/atoto/elasticsearch/data:/usr/share/elasticsearch/data
      - /newdisk1/atoto/elasticsearch/plugins:/usr/share/elasticsearch/plugins
      - /newdisk1/atoto/elasticsearch/logs:/usr/share/elasticsearch/logs
      - /newdisk1/atoto/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
    networks:
      - app_default
networks:
  default:
    driver: bridge
```
elasticsearch.yml
```yml
uster.name: elasticsearch7-new
discovery.type: single-node
path.logs: /usr/share/elasticsearch/logs
```

写好对应的配置文件后执行命令

```sh
sudo mkdir -p /newdisk1/atoto/elasticsearch/logs
sudo chown -R 1000:0 /newdisk1/atoto/elasticsearch/logs
sudo chmod -R 755 /newdisk1/atoto/elasticsearch/logs
```
```sh
1000 对应容器内用户的 UID（elasticsearch）
0 是 root 组，Elasticsearch 默认就是这个组合
755 允许读写执行，足够容器写日志
```