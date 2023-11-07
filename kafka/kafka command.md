## 命令



查看topic 

```
/usr/local/kafka/bin/kafka-topics.sh --bootstrap-server kafka.dev.myyixue.com:9092 --list |grep test-study-event-stream
```





生产者：

```
/usr/local/kafka/bin/kafka-console-producer.sh --broker-list kafka.dev.myyixue.com:9092 --topic test-study-event-stream
```

kafka-console-producer.sh 是 Kafka 提供的一个 shell 脚本，用于在命令行中启动一个控制台生产者，用于向 Kafka 集群发送消息

--broker-list: 指定 Kafka brokers 的地址和端口号，用冒号分隔

--topic: 指定要发送消息的主题名。







消费者组：

```
/usr/local/kafka/bin/kafka-consumer-groups.sh --bootstrap-server kafka.dev.myyixue.com:9092  --list
```

创建消费者从头开始读并设置消费者组：

```
/usr/local/kafka/bin/kafka-console-consumer.sh --bootstrap-server kafka.dev.myyixue.com:9092 --topic test-study-event-stream --from-beginning --group basic_study_event_group_pre
```

bootstrap-server :参数用于指定 Kafka 集群的地址

topic :参数用于指定要消费的主题名称

group :参数用于指定消费者组的名称

from-beginning :读取最早的偏移量

group ：指定消费者组名称



创建消费者时设置偏移量为最新:

```
./bin/kafka-consumer-groups.sh --bootstrap-server hadoop102:9092 --group testGroup --topic first --reset-offsets --to-latest --execute
#启动
./bin/kafka-console-consumer.sh --bootstrap-server hadoop102:9092 --topic first  --group testGroup

```

reset-offsets：表示要重置偏移量

to-latest：表示将偏移量移到最新位置

execute：表示执行偏移量重置操作

执行偏移量重置后，会移动到最新位置，如果需要修改，得重新设置，谨慎操作。



创建消费者时设置具体的偏移量数据(重置偏移量到6):

```
./bin/kafka-consumer-groups.sh --bootstrap-server kafka.dev.myyixue.com:9092 --group testGroup --topic first --reset-offsets --shift-by 2 --execute
```





查看消费者组主具体信息:

```
/usr/local/kafka/bin/kafka-consumer-groups.sh --bootstrap-server kafka-test.internal.classba.cn:9092 --describe --group groupOfflineStudyV2
```

TOPIC：消费主题名称

PARTITION：分区Id

CURRENT-OFFSET： 当前消费偏移量，即消费者当前消费到的消息的偏移量

LOG-END-OFFSET： 分区的最新偏移量，即分区中最新消息的偏移量

LAG：消费者落后于最新偏移量的消息数量，即LAG = LOG-END-OFFSET - CURRENT-OFFSET（最新-当前）

CONSUMER-ID：消费者ID

HOST：消费者所在主机

CLIENT-ID：消费者客户端ID
