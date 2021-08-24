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
   
   mystemctl restart mysqld

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
   
   msyql命令行查看配置
   
   ```
   show variables like 'log_bin';
   ```
   
   与 binlog 相关的几个参数如下：
   
   ```
   server-id = 1003306
   
   log-bin = /data/mysql/logs/binlog
   
   binlog_format = row
   
   expire_logs_days = 15
   ```
   
   log_bin：指定binlog是否开启及文件名称。
   
   server_id：指定服务器唯一ID，开启binlog 必须设置此参数。
   
   binlog_format：指定binlog模式，建议设置为ROW。
   
   max_binlog_size：控制单个二进制日志大小，当前日志文件大小超过此变量时，执行切换动作。
   
   expire_logs_days：控制二进制日志文件保留天数，默认值为0，表示不自动删除，可设置为0~99。
   
   binlog默认情况下是不开启的，不过一般情况下，建议开启，特别是要做主从同步时。
   
   
   
   