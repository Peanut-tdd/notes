# mysql相关配置

1. **mysql慢日志查询配置**

   **mysql -uroot -p 登录命令行内查看及编辑相关配置**

   > show variables like 'slow_query%'；
   >
   > show variables like 'long_query_log';
   >
   > 
   >
   > 
   
   >![图1](https://github.com/Peanut-tdd/Picture/blob/main/Dingtalk_20210824175409.jpg?raw=true)
   
   
   
   set global slow_query_log='ON'; 
   set global slow_query_log_file='/var/log/mysql/slow.log';
   set global long_query_time=1;
   
   >![图2](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/Dingtalk_20210824175612.jpg)
   
   注意：上面配置都是临时的，mysql重启后会失效
   
   **编辑配置文件 /etc/my.cnf  如下**
   
   > slow_query_log=on
   >
   > slow_query_log_file=/var/log/mysql/slow.log
   >
   > long_query_time=2(单位/秒)
   
   设置slow_query_log_file时，目录必须存在，并且mysql 有权读写该目录，
   
   mkdir /var/log/mysql
   
   sudo chown mysql:mysql -R /var/log/mysql
   
   **修改配置后重启mysql**
   
   systemctl restart mysqld

-------

2. **mysql general log日志配置**

   mysql命令行查看配置

   ```
   show varibales like 'general_log';
   show variables like 'general_log_file'；
   ```

   编辑配置

   ```
   set global general_log=on; -- 开启日志功能
   set global general_log_file='tmp/general.lg'; -- 设置日志文件保存位置
   set global log_output='table'; -- 设置输出类型为 table
   set global log_output='file'; -- 设置输出类型为file
   ```

   log_output=’FILE’ 表示将日志存入文件,默认值是FILE　 
   log_output=’TABLE’表示将日志存入数据库,这样日志信息就会被写入到mysql.slow_log表中.

-----

3. **mysql binlog(二进制日志)配置**

   它记录了数据库所有执行的DDL和DML语句(除了数据查询语句select、show等)，以事件形式记录并保存在二进制文件中。常用于数据恢复和主从复制

    注：`二进制日志包括两类文件：二进制日志索引文件（文件名后缀为.index）用于记录所有的二进制文件，二进制日志文件（文件名后缀为.00000*）记录数据库所有的DDL和DML(除了数据查询语句)语句事件。`

   
   
   **msyql命令行查看配置**

   ```
   show variables like 'log_bin';#或者可以使用 show variables like '%log_%'；查看全部
   mysql> show binlog events;   #只查看第一个binlog文件的内容
   mysql> show binlog events in 'mysql-bin.000002';#查看指定binlog文件的内容
   mysql> show binary logs;  #获取binlog文件列表
   mysql> show master status； #查看当前正在写入的binlog文件
   ```
   
   
   
   **与 binlog 相关的几个参数如下：**
   
   ```
   server-id = 1003306
   
   log_bin = /data/mysql/logs/binlog
   
   binlog_format = row
   
   expire_logs_days = 15
   
   max_binlog_size         = 100M         #超过max_binlog_size或超过6小时会切换到下一序号文件
    
   binlog_cache_size       = 16M           
   #二进制日志缓冲大小,通过show status like 'binlog_%';查看调整写入磁盘的次数，写入磁盘为0最好
    
   max_binlog_cache_size   = 256M
    
   relay_log_recovery      = 1            
   #当slave从库宕机后，假如relay-log损坏了，
   #导致一部分中继日志没有处理，则自动放弃所有未执行的relay-log，
   #并且重新从master上获取日志，这样就保证了relay-log的完整性。
    
   sync_binlog             = 1            #二进制日志（binary log）同步到磁盘的频率
   innodb_flush_log_at_trx_commit = 1     #每次事务提交将日志缓冲区写入log file，并同时flush到磁盘。
   
   ```

   log_bin：指定binlog是否开启及文件名称。

   server_id：指定服务器唯一ID，开启binlog 必须设置此参数。

   binlog_format：指定binlog模式，建议设置为ROW。

   max_binlog_size：控制单个二进制日志大小，当前日志文件大小超过此变量时，执行切换动作。
   
   expire_logs_days：控制二进制日志文件保留天数，默认值为0，表示不自动删除，可设置为0~99。
   
   binlog默认情况下是不开启的，不过一般情况下，建议开启，特别是要做主从同步时。
   
   
   
   **以下2个参数可以减少网络问题导致的主从数据同步延迟**
   
   ```
   slave_net_timeout       = 5       #当slave从主数据库读取log数据失败后，等待多久重新建立连接并获取数据,默认3600秒
   ```
   
   
   

  **MySQL binlog的三种工作模式**
 - **Statement level（默认) **
   每一条被修改数据的sql都会记录到master的bin-log中，slave在复制的时候sql进程会解析成和原来master端执行过的相同的sql再次执行

   优点：不需要记录每一行的数据变化，减少bin-log日志量，节约磁盘IO，提高新能

   缺点：容易出现主从复制不一致

   
   
- **Row level**

  日志中会记录每一行数据被修改的情况，然后在slave端对相同的数据进行修改。

  优点：能清楚的记录每一行数据修改的细节

  缺点：数据量太大



- **Mixed（混合模式）**

  以上两种模式的混合使用，一般的复制使用STATEMENT模式保存binlog，对于STATEMENT模式无法复制的操作使用ROW模式保存binlog，MySQL会根据执行的SQL语句选择日志保存方式。

--------

### sync_binlog

```
sync_binlog=0
```

当事务提交之后，MySQL不做fsync之类的磁盘同步指令刷新binlog_cache中的信息到磁盘，而让Filesystem自行决定什么时候来做同步，或者cache满了之后才同步到磁盘。这个是性能最好的。

```
sync_binlog=1
```

当每进行1次事务提交之后，MySQL将进行一次fsync之类的磁盘同步指令来将binlog_cache中的数据强制写入磁盘。

```
sync_binlog=n
```

当每进行n次事务提交之后，MySQL将进行一次fsync之类的磁盘同步指令来将binlog_cache中的数据强制写入磁盘。

注：大多数情况下，对数据的一致性并没有很严格的要求，所以并不会把 sync_binlog 配置成 1. 为了追求高并发，提升性能，可以设置为 100 或直接用 0，而对于支付服务这样的应用，还是比较推荐 sync_binlog = 1.

-----

### 查看binlog日志

`binlog`本身是一类二进制文件。二进制文件更省空间，写入速度更快，是无法直接打开来查看的。
因此mysql提供了命令`mysqlbinlog`进行查看。

一般的`statement`格式的二进制文件，用下面命令就可以

```
mysqlbinlog mysql-bin.000001
```

如果是`row`格式，加上`-v`或者`-vv`参数就行，如

```
mysqlbinlog -vv mysql-bin.000001 
```



注：需提前设置文件/etc/selinux/conf 文件的SELINUX为 disabled



