## 查看所有topic 多个broker用逗号(可以只连接其中一个，topic都是同步的) ./kafka-topics.sh --list --bootstrap-server broker-addr1:9092,broker-addr2:9092

参数说明

```sh
## 消费某个topic中的数据 
./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my_topic --from-beginning

## 参数说明

--from-beginning 从最开始消费 如果不指定则为latest

--offset n n为正整数或者latest 或者 earliest

--partition 指定了offset必须指定partition从哪个partition

```


```sh

## 查看topic详情 包括partition等 
./kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic my_topic

## 参数说明

--from-beginning 从最开始消费

--offset n n为正整数或者latest 或者 earliest

--partition 指定了offset必须指定partition从哪个partition

## 查看某个topic的已经提交的offset 
./kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic my_topic --time -1 
# 根据消费者组查看已经提交的offset 
./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group my_group --describe

##参数说明

--time -1表示最新 -2表示最早