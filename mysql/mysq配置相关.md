# MySQL配置相关

- **thread_cache_size**

  mysql缓存的线程数

 ```
 mysql> show status like 'thread%';
 +-------------------+-------+
 | Variable_name     | Value |
 +-------------------+-------+
 | Threads_cached    | 4     |   当前被缓存的空闲线程的数量
 | Threads_connected | 1     |   正在使用（处于连接状态）的线程
 | Threads_created   | 5     |   服务启动以来，创建了多少个线程
 | Threads_running   | 1     |   正在忙的线程（正在查询数据，传输数据等等操作）
 ```



- back_log

  可存放在堆栈内的请求数

   该值指出在MySQL暂时停止回答新请求之前的短时间内多少个请求可以被存在堆栈中。也就是说，如果MySql的连接数据达到max_connections时，新来的请求将会被存在堆栈中，以等待某一连接释放资源，该堆栈的数量即back_log，如果等待连接的数量超过back_log，将不被授予连接资源。将会报：unauthenticated user | xxx.xxx.xxx.xxx | NULL | Connect | NULL | login | NULL 的待连接进程时.

  ​    back_log值不能超过TCP/IP连接的侦听队列的大小。若超过则无效，查看当前系统的TCP/IP连接的侦听队列的大小命令：cat /proc/sys/net/ipv4/tcp_max_syn_backlog目前系统为1024。对于Linux系统推荐设置为小于512的整数。







- **wait_timeout和interactive_timeout**

  wait_timeout 控制连接最大空闲时长

   对于非交互式连接，类似于mysql连接，wait_timeout的值继承自服务器端全局变量wait_timeout。

    对于交互式连接，类似于jdbc客户单连接，wait_timeout的值继承自服务器端全局变量interactive_timeout。

  判断一个连接的空闲时间，可通过show processlist输出中Sleep状态的时间

  

