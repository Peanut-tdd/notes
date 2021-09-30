# elasticsearch的pipelines

[创建pipeline](#jump1)

[查看pipeline](#jump2)

[查看管道下的processors处理器](#jump3)

[使用pipeline管道](#jump4)



<span id="jump1">创建pipeline:</span>

```
PUT _ingest/pipeline/es-lumen-log-pipeline
{
  "processors": [
    {
      "grok": {
        "field": "message",
        "patterns": [
           "%{TIMESTAMP_ISO8601:logdate}"
          ]
      },
      "date": {
        "field": "logdate",
        "formats": [
           "yyyy-MM-dd HH:mm:ss"
          ],
          "timezone" : "Asia/Shanghai"
      }
    }
  ]
}

```

<span id="jump2">查看pipeline:</span>

```
GET _ingest/pipeline/es*

返回：{
  "es-lumen-log-pipeline" : {
    "processors" : [
      {
        "grok" : {
          "field" : "message",
          "patterns" : [
            "%{TIMESTAMP_ISO8601:logdate}"
          ]
        },
        "date" : {
          "field" : "logdate",
          "formats" : [
            "yyyy-MM-dd HH:mm:ss"
          ],
          "timezone" : "Asia/Shanghai"
        }
      }
    ]
  }
}

```



<span id="jump3">查看管道下的processors处理器</span>

```
GET _nodes/ingest?filter_path=nodes.*.ingest.processors
```



<span id="jump4">使用pipeline</span>

```
POST _ingest/pipeline/es-lumen-log-pipeline/_simulate
{
  "docs":[
     {
       "_source":{
         "message":"""[2021-09-15 21:26:51] local.ERROR: Symfony\Component\Debug\Exception\FatalErrorException: Allowed memory size of 134217728 bytes exhausted (tried to allocate 10485800 bytes) in D:\phpStudy\PHPTutorial\WWW\php\vendor\elasticsearch\elasticsearch\src\Elasticsearch\Endpoints\Bulk.php:42
Stack trace:
#0 D:\phpStudy\PHPTutorial\WWW\php\vendor\laravel\lumen-framework\src\Concerns\RegistersExceptionHandlers.php(54): Laravel\Lumen\Application->handleShutdown()
#1 [internal function]: Laravel\Lumen\Application->Laravel\Lumen\Concerns\{closure}()
#2 {main} {"exception":"[object] (Symfony\\Component\\Debug\\Exception\\FatalErrorException(code: 1): Allowed memory size of 134217728 bytes exhausted (tried to allocate 10485800 bytes) at D:\\phpStudy\\PHPTutorial\\WWW\\php\\vendor\\elasticsearch\\elasticsearch\\src\\Elasticsearch\\Endpoints\\Bulk.php:42)
[stacktrace]
#0 D:\\phpStudy\\PHPTutorial\\WWW\\php\\vendor\\laravel\\lumen-framework\\src\\Concerns\\RegistersExceptionHandlers.php(54): Laravel\\Lumen\\Application->handleShutdown()
#1 [internal function]: Laravel\\Lumen\\Application->Laravel\\Lumen\\Concerns\\{closure}()
#2 {main}
"} """
       }
     }
    ]
}


返回：
{
  "docs" : [
    {
      "doc" : {
        "_index" : "_index",
        "_type" : "_doc",
        "_id" : "_id",
        "_source" : {
          "@timestamp" : "2021-09-15T21:26:51.000+08:00",
          "message" : """[2021-09-15 21:26:51] local.ERROR: Symfony\Component\Debug\Exception\FatalErrorException: Allowed memory size of 134217728 bytes exhausted (tried to allocate 10485800 bytes) in D:\phpStudy\PHPTutorial\WWW\php\vendor\elasticsearch\elasticsearch\src\Elasticsearch\Endpoints\Bulk.php:42
Stack trace:
#0 D:\phpStudy\PHPTutorial\WWW\php\vendor\laravel\lumen-framework\src\Concerns\RegistersExceptionHandlers.php(54): Laravel\Lumen\Application->handleShutdown()
#1 [internal function]: Laravel\Lumen\Application->Laravel\Lumen\Concerns\{closure}()
#2 {main} {"exception":"[object] (Symfony\\Component\\Debug\\Exception\\FatalErrorException(code: 1): Allowed memory size of 134217728 bytes exhausted (tried to allocate 10485800 bytes) at D:\\phpStudy\\PHPTutorial\\WWW\\php\\vendor\\elasticsearch\\elasticsearch\\src\\Elasticsearch\\Endpoints\\Bulk.php:42)
[stacktrace]
#0 D:\\phpStudy\\PHPTutorial\\WWW\\php\\vendor\\laravel\\lumen-framework\\src\\Concerns\\RegistersExceptionHandlers.php(54): Laravel\\Lumen\\Application->handleShutdown()
#1 [internal function]: Laravel\\Lumen\\Application->Laravel\\Lumen\\Concerns\\{closure}()
#2 {main}
"} """,
          "logdate" : "2021-09-15 21:26:51"
        },
        "_ingest" : {
          "timestamp" : "2021-09-29T09:01:14.386594761Z"
        }
      }
    }
  ]
}


```







参考：https://home.zzx1996.top:1111/archives/%E5%9F%BA%E4%BA%8Efilebeatelasticsearch%E7%9A%84%E6%97%A5%E5%BF%97%E6%B1%87%E6%80%BB%E5%92%8C%E8%A7%A3%E6%9E%90%E6%96%B9%E6%A1%88

