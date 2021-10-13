# canal-adapter1.1.5同步数据至es7

## 增量同步配置

**注意：**同步至es7须先替换plugin下的client-adapter.es7x-1.1.5-jar-with-dependencies.jar包，alpha下载地址：https://github.com/alibaba/canal/releases/download/canal-1.1.5-alpha-2/canal.adapter-1.1.5-SNAPSHOT.tar.gz



application.yml配置

```
server:
  port: 8081
spring:
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
    default-property-inclusion: non_null

canal.conf:
  mode: tcp #tcp kafka rocketMQ rabbitMQ
  flatMessage: true
 # zookeeperHosts:                           #对应集群模式下的zk地址
  syncBatchSize: 1000
  retries: 0
  timeout:
  accessKey:
  secretKey:
  consumerProperties:
    # canal tcp consumer
    canal.tcp.server.host: 127.0.0.1:11111         #单机模式下的canal server IP：port
    canal.tcp.zookeeper.hosts:
    canal.tcp.batch.size: 500
    canal.tcp.username:
    canal.tcp.password:
    # kafka consumer
   #kafka.bootstrap.servers: 127.0.0.1:9092
   #kafka.enable.auto.commit: false
   #kafka.auto.commit.interval.ms: 1000
   #kafka.auto.offset.reset: latest
   #kafka.request.timeout.ms: 40000
   #kafka.session.timeout.ms: 30000
   #kafka.isolation.level: read_committed
   #kafka.max.poll.records: 1000
    # rocketMQ consumer
   # rocketmq.namespace:
   # rocketmq.namesrv.addr: 127.0.0.1:9876
   # rocketmq.batch.size: 1000
   # rocketmq.enable.message.trace: false
   # rocketmq.customized.trace.topic:
   # rocketmq.access.channel:
   # rocketmq.subscribe.filter:
    # rabbitMQ consumer
   # rabbitmq.host:
   # rabbitmq.virtual.host:
   # rabbitmq.username:
   # rabbitmq.password:
   # rabbitmq.resource.ownerId:

  srcDataSources:                    #源数据库配置
    defaultDS:
      url: jdbc:mysql://192.168.33.10:3306/loulan?useUnicode=true&characterEncoding=UTF-8&useSSL=false&autoReconnect=true&serverTimezone=UTC
      username: root
      password: QWER1234!
  canalAdapters:                                               #适配器列表，plugin目录下放置的是支持的适配器包
  - instance: example # canal instance Name or mq topic name     #canal实例名 或MQ topic名
    groups:
    - groupId: g1
      outerAdapters:
      - name: logger
#      - name: rdb
#        key: mysql1
#        properties:
#          jdbc.driverClassName: com.mysql.jdbc.Driver
#          jdbc.url: jdbc:mysql://127.0.0.1:3306/mytest2?useUnicode=true
#          jdbc.username: root
#          jdbc.password: 121212
#      - name: rdb
#        key: oracle1
#        properties:
#          jdbc.driverClassName: oracle.jdbc.OracleDriver
#          jdbc.url: jdbc:oracle:thin:@localhost:49161:XE
#          jdbc.username: mytest
#          jdbc.password: m121212
#      - name: rdb
#        key: postgres1
#        properties:
#          jdbc.driverClassName: org.postgresql.Driver
#          jdbc.url: jdbc:postgresql://localhost:5432/postgres
#          jdbc.username: postgres
#          jdbc.password: 121212
#          threads: 1
#          commitSize: 3000
#      - name: hbase
#        properties:
#          hbase.zookeeper.quorum: 127.0.0.1
#          hbase.zookeeper.property.clientPort: 2181
#          zookeeper.znode.parent: /hbase
      - name: es7                                        #修改为conf目录下的outAdapter目录
        key: es-user                                       
        hosts: 127.0.0.1:9300 # 127.0.0.1:9200 for rest mode java识别时端口号为9300  
        properties:
          mode: reset # or rest
          # security.auth: test:123456 #  only used for rest mode
          cluster.name: es
#        - name: kudu
#          key: kudu
#          properties:
#            kudu.master.address: 127.0.0.1 # ',' split multi address
```







user.yml输出配置

```
dataSourceKey: defaultDS
destination: example
outerAdapterKey: es-user
groupId: g1
esMapping:
  _index: user
  _id: _id
  sql: "select id as _id,name,staff_id, nickname,avatar,phone,status,fax,created_at,updated_at from user"
  commitBatch: 3000


#{
#  "mappings":{
#    "_doc":{
#      "properties":{
#        "id": {
#          "type": "long"
#        },
#        "name": {
#          "type": "text"
#        },
#        "email": {
#          "type": "text"
#        },
#        "order_id": {
#          "type": "long"
#        },
#        "order_serial": {
#          "type": "text"
#        },
#        "order_time": {
#          "type": "date"
#        },
#        "customer_order":{
#          "type":"join",
#          "relations":{
#            "customer":"order"
#          }
#        }
#      }
#    }
#  }
#}
```



canal-adapter运行：`./bin/startup.sh`



-----

## 全量同步

执行 etl api接口

`curl -XPOST 127.0.0.1:8081/etl/es7/es-user/user.yml`

- es7 选择的适配器name

- es-user 选择的适配器key

  

注意：可能会出现**“java.sql.SQLException: Zero date value prohibited”**报错

解决方案：application.yml的srcDataSources.defaultDS.url源数据库配置末尾加参数：`&zeroDateTimeBehavior=convertToNull`

url参数为：`jdbc:mysql://192.168.33.10:3306/loulan?useUnicode=true&characterEncoding=UTF-8&useSSL=false&autoReconnect=true&serverTimezone=UTC&zeroDateTimeBehavior=convertToNull`

