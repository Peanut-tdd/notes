# ab压测





**mac安装ab压测**

```
brew install httpd
```





**执行ab压测**

```
abs -n1000 -c100 https://www.baidu.com/
```

-n 总请求数

-c 并发数





压测返回：

```
This is ApacheBench, Version 2.3 <$Revision: 1923142 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.baidu.com (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        BWS/1.1
Server Hostname:        www.baidu.com
Server Port:            443
SSL/TLS Protocol:       TLSv1.2,ECDHE-RSA-AES128-GCM-SHA256,2048,128
Server Temp Key:        ECDH prime256v1 256 bits
TLS Server Name:        www.baidu.com

Document Path:          /
Document Length:        227 bytes

Concurrency Level:      100
Time taken for tests:   3.455 seconds
Complete requests:      1000
Failed requests:        757
   (Connect: 0, Receive: 0, Length: 757, Exceptions: 0)
Total transferred:      23463235 bytes
HTML transferred:       22391203 bytes
Requests per second:    289.43 [#/sec] (mean)
Time per request:       345.504 [ms] (mean)
Time per request:       3.455 [ms] (mean, across all concurrent requests)
Transfer rate:          6631.86 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       85  264  65.0    273     514
Processing:    27   53  30.7     40     290
Waiting:       21   42  16.2     37     113
Total:        112  317  75.9    317     604

Percentage of the requests served within a certain time (ms)
  50%    317
  66%    332
  75%    347
  80%    369
  90%    402
  95%    470
  98%    519
  99%    546
 100%    604 (longest request)

```





重点参数

```
Time taken for tests:   3.455 seconds     //压测耗时
Failed requests:        757                //失败的请求数，失败率75.7%
   (Connect: 0, Receive: 0, Length: 757, Exceptions: 0)    失败原因

Requests per second:    289.43 [#/sec] (mean)			//QPS
Time per request:       345.504 [ms] (mean)				//并发请求的每请求平均响应时间
Time per request:       3.455 [ms] (mean, across all concurrent requests) //每请求响应时间

 
Percentage of the requests served within a certain time (ms)   //响应时间分布，重点关注p50,p90,p99；观察有无长尾雪崩
  50%    317
  66%    332
  75%    347
  80%    369
  90%    402
  95%    470
  98%    519
  99%    546
 100%    604 (longest request)
 
```





