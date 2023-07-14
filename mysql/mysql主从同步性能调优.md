# mysql主从同步性能调优

要优化MySQL主从同步，可以考虑通过以下几种方式进行：



– 通过加快主服务器的binlog写入的速度来提升性能，可以在my.cnf配置文件中调整binlog_cache_size和binlog_sync_size；



– 对从服务器连接超时时间参数进行优化，可以通过调整slave_net_timeout参数来降低超时时间；



– 删除无用的历史binlog，以减少主从同步传输的数据量，可以通过PURGE BINARY LOGS ‘log_name’语句来删除无用的binlog；