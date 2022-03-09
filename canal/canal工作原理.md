# canal原理相关

```
canal，译意为水道/管道/沟渠，主要用途是基于 MySQL 数据库增量日志解析，提供增量数据订阅和消费。
```

## mysql主从同步原理

![](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/687474703a2f2f646c2e69746579652e636f6d2f75706c6f61642f6174746163686d656e742f303038302f333038362f34363863316131342d653761642d333239302d396433642d3434616335303161373232372e6a7067.jpg)

- MySQL master 将数据变更写入二进制日志( binary log, 其中记录叫做二进制日志事件binary log events，可以通过 show binlog events 进行查看)
- MySQL slave 将 master 的 binary log events 拷贝到它的中继日志(relay log)
- MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据



## canal工作原理

![](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303139313130343130313733353934372e706e67.png)

- canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送dump 协议
- MySQL master 收到 dump 请求，开始推送 binary log 给 slave (即 canal )
- canal 解析 binary log 对象(原始为 byte 流)





 # canal-adapter工作原理







## 同步选型小结

![image.png](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/557635cb9ad34d18bf54a619f80c47a3.png)





参考链接：https://www.cnblogs.com/xuxiaojian/p/14408862.html#%E5%B8%B8%E7%94%A8%E7%9A%84%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E6%96%B9%E6%A1%88













