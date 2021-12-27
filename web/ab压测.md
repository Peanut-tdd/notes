# ab压测

```
[root@localhost ~]# ab -help
Usage: ab [options] [http[s]://]hostname[:port]/path
Options are:
    -n requests     Number of requests to perform
    -c concurrency  Number of multiple requests to make at a time
    -t timelimit    Seconds to max. to spend on benchmarking
                    This implies -n 50000
    -s timeout      Seconds to max. wait for each response
                    Default is 30 seconds
    -b windowsize   Size of TCP send/receive buffer, in bytes
    -B address      Address to bind to when making outgoing connections
    -p postfile     File containing data to POST. Remember also to set -T
    -u putfile      File containing data to PUT. Remember also to set -T
    -T content-type Content-type header to use for POST/PUT data, eg.
                    'application/x-www-form-urlencoded'
                    Default is 'text/plain'
    -v verbosity    How much troubleshooting info to print
    -w              Print out results in HTML tables
    -i              Use HEAD instead of GET
    -x attributes   String to insert as table attributes
    -y attributes   String to insert as tr attributes
    -z attributes   String to insert as td or th attributes
    -C attribute    Add cookie, eg. 'Apache=1234'. (repeatable)
    -H attribute    Add Arbitrary header line, eg. 'Accept-Encoding: gzip'
                    Inserted after all normal header lines. (repeatable)
    -A attribute    Add Basic WWW Authentication, the attributes
                    are a colon separated username and password.
    -P attribute    Add Basic Proxy Authentication, the attributes
                    are a colon separated username and password.
    -X proxy:port   Proxyserver and port number to use
    -V              Print version number and exit
    -k              Use HTTP KeepAlive feature
    -d              Do not show percentiles served table.
    -S              Do not show confidence estimators and warnings.
    -q              Do not show progress when doing more than 150 requests
    -g filename     Output collected data to gnuplot format file.
    -e filename     Output CSV file with percentages served
    -r              Don't exit on socket receive errors.
    -h              Display usage information (this message)
    -Z ciphersuite  Specify SSL/TLS cipher suite (See openssl ciphers)
    -f protocol     Specify SSL/TLS protocol
                    (SSL3, TLS1, TLS1.1, TLS1.2 or ALL)
```

-n：总请求数，向请求的url发送多少次请求，如-n1000指向url发送1000次请求

-c ：并发数，一次发送多少个请求 ，如-c100指一次发送100次请求

-T：post发送的数据类型，也就是header头中的Content-Type，如-T "application/json" 表示发送的数据是json类型

-p: post请求使用，后面接post的data数据文件，要注意的是data.txt里面的数据格式要和-T中声明的一致，如-T application/json 则data.txt格式就必须是json格式

-H 在header中添加信息，如-H "X-Token: xxxxx"







**post http请求：**

```
POST /balance/payment HTTP/1.1
Host: vagrant_joy.com
X-Token: 70fb1431920ddf5e812570ee10859117
Content-Length: 412
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="id"

4
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="bank_name"

中国银行
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="card_no"

1234567890
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="bank_branch_name"

闵行支行1
----WebKitFormBoundary7MA4YWxkTrZu0gW

```



**ab post 请求:**

```
ab -n10000 -c100 -p "/test.txt" -T "multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW" -H "X-Token:70fb1431920ddf5e812570ee10859117" "http://127.0.0.1/balance/payment"
```

test.txt内容：

```

----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="id"

4
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="bank_name"

中国银行
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="card_no"

1234567890
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="bank_branch_name"

闵行支行1
----WebKitFormBoundary7MA4YWxkTrZu0gW
                               
```





返回：

```

Server Software:        nginx/1.20.2      //nginx版本
Server Hostname:        127.0.0.1         //服务器主机
Server Port:            80

Document Path:          /balance/payment   //接口地址
Document Length:        153 bytes

Concurrency Level:      100                 //并发数
Time taken for tests:   0.658 seconds     //所有请求完成所花费的时间
Complete requests:      10000     //总请求数量，这是我们设置的参数之一
Failed requests:        0			//失败的请求数
Write errors:           0
Non-2xx responses:      10000
Total transferred:      3030000 bytes
Total body sent:        6690000
HTML transferred:       1530000 bytes   
Requests per second:    15189.44 [#/sec] (mean)   //吞吐率，计算公式：Complete requests/Time taken for tests  总请求数/处理完成这些请求数所花费的时间
Time per request:       6.584 [ms] (mean)          //用户平均请求等待时间
Time per request:       0.066 [ms] (mean, across all concurrent requests)
Transfer rate:          4494.53 [Kbytes/sec] received
                        9923.57 kb/s sent
                        14418.10 kb/s total

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   1.0      2       6
Processing:     1    4   1.5      4      16
Waiting:        1    3   1.4      3      15
Total:          2    7   1.9      7      18

Percentage of the requests served within a certain time (ms)
  50%      7
  66%      7
  75%      8
  80%      8
  90%      9
  95%      9
  98%     10
  99%     11
 100%     18 (longest request)
 
 
 
// Percentage of requests served within a certain time（ms）这部分数据用于描述每个请求处理时间的分布情况，比如以上测试，80%的请求处理时间都不超过7ms，这个处理时间是指前面的Time per request，即对于单个用户而言，平均每个请求的处理时间
```







