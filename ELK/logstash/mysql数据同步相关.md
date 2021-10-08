# logstash mysql数据收集

配置信息目录:`/etc/logstash/conf.d`

配置信息:

```
input{
   stdin {

       }
      jdbc {
        # 连接的数据库地址和哪一个数据库，指定编码格式，禁用SSL协议，设定自动重连
        jdbc_connection_string => "jdbc:mysql://127.0.0.1:3306/result_hepai?characterEncoding=UTF-8&useSSL=false&autoReconnect=true&serverTimezone=UTC"
        jdbc_user => "root"
        jdbc_password => "QWER1234!"
        # 下载连接数据库的驱动包，建议使用绝对地址
       jdbc_driver_library => "/etc/logstash/mysql-connection/mysql-connector-java-8.0.16.jar"

       jdbc_driver_class => "com.mysql.jdbc.Driver"
       jdbc_paging_enabled => "true"
       jdbc_page_size => "50000"
       codec => plain { charset => "UTF-8"}

      #使用其它字段追踪，而不是用时间
      #use_column_value => true   //这里如果是用时间追踪比如：数据的更新时间或创建时间等和时间有关的这里一定不能是true, 切记切记切记，我是用update_time来追踪的
      #追踪的字段
       tracking_column => xxx3id
      record_last_run => true
      #上一个sql_last_value值的存放文件路径, 必须要在文件中指定字段的初始值  这里说是必须指定初始值，我没指定默认是1970-01-01 08：00：00
      last_run_metadata_path => "/usr/local/opt/logstash/lastrun/.logstash_jdbc_last_run"  #这里的lastrun文件夹和.logstash_jdbc_last_run是自己创建的

      jdbc_default_timezone => "Asia/Shanghai"   #设置时区
      #statement => SELECT * FROM order_day_cost # 这里要说明一下如果直接写sql语句，前面这种写法肯定不对的，加上引号也试过也不对，所以我直接写在jdbc.sql文件中
      statement_filepath => "/usr/share/logstash/mysqletc/platform_video_cost.sql"


       #是否清除 last_run_metadata_path 的记录,如果为真那么每次都相当于从头开始查询所有的数据库记录
       clean_run => false

       # 这是控制定时的，重复执行导入任务的时间间隔，第一位是分钟 不设置就是1分钟执行一次
       schedule => "* * * * *"
     }
 }

 filter {

    json {

        source => "message"

        remove_field => ["message"]

    }

      #下面：当使用Logstash自动生成的mapping模板时过滤掉@timestamp和@version字段
    mutate {
        remove_field =>
        ["@timestamp","@version"]
      
       }

}

output {

    elasticsearch {

        # 要导入到的Elasticsearch所在的主机

        hosts =>["127.0.0.1:9200"]

        # 要导入到的Elasticsearch的索引的名称

        index => "platform_video_cost"

        # 类型名称（类似数据库表名）

      # document_type => "platform_video_cost"

        # 主键名称（类似数据库主键）

        document_id => "%{xxx3id}"
    }

    stdout {

        # JSON格式输出

        codec => json_lines

    }
}
```

