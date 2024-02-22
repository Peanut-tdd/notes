## php原生curl请求

#### 1.1GET、POST请求

```php
/**
     * Make an HTTP request
     * @param string $url: 请求url
     * @param string $method: 请求方法，目前只有"GET", "POST"
     * @param mix $data_fields: 查询数据
     * @param array $option: 附加选项
     * @param integer $timeout: 超时
     * @return string HTTP请求的响应
     */
    public static function http($url, $method = 'GET', $data_fields = array(), $option = array(), $timeout = 3000) {
        $_time_start = microtime(true);
        $ch          = curl_init();
        curl_setopt($ch, CURLOPT_HTTP_VERSION, CURL_HTTP_VERSION_1_1);
        curl_setopt($ch, CURLOPT_ENCODING, 'gzip, deflate');
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_TIMEOUT_MS, $timeout);
        //curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 3);
        curl_setopt($ch, CURLOPT_CONNECTTIMEOUT_MS, $timeout);
        //curl_setopt($ch, CURLOPT_TIMEOUT, 3);
        curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
        curl_setopt($ch, CURLOPT_MAXREDIRS, 2);
        curl_setopt($ch, CURLOPT_NOSIGNAL, true);
        if (!empty($option['header'])) {
            curl_setopt($ch, CURLOPT_HTTPHEADER, $option['header']);
        }
        if (!empty($option['useragent'])) {
            curl_setopt($ch, CURLOPT_USERAGENT, $option['useragent']);
        }
        if (!empty($option['referer'])) {
            curl_setopt($ch, CURLOPT_REFERER, $option['referer']);
        }
        if (isset($option['cookiejar']) && file_exists($option['cookiejar'])) {
            curl_setopt($ch, CURLOPT_COOKIEJAR, $option['cookiejar']);
            curl_setopt($ch, CURLOPT_COOKIEFILE, $option['cookiejar']);
        }
        if (!empty($option['cookie'])) {
            curl_setopt($ch, CURLOPT_COOKIE, $option['cookie']);
        }
        if (!empty($option['proxy'])) {
            curl_setopt($ch, CURLOPT_HTTPPROXYTUNNEL, true);
            curl_setopt($ch, CURLOPT_PROXY, $option['proxy']);
            curl_setopt($ch, CURLOPT_PROXYTYPE, CURLPROXY_SOCKS5);
        }

        if (defined('CURLOPT_IPRESOLVE') && defined('CURL_IPRESOLVE_V4')) {
            curl_setopt($ch, CURLOPT_IPRESOLVE, CURL_IPRESOLVE_V4);
        }

        // 设置查询数据
        switch ($method) {
        case 'POST':
            curl_setopt($ch, CURLOPT_POST, true);
            if (!empty($data_fields)) {
                if (is_array($data_fields)) {
                    if (isset($option['header']) && in_array('Content-Type: application/json; charset=utf-8',$option['header']) ) {
                        $data_fields = json_encode($data_fields, 320);
                    } else {
                        $data_fields = http_build_query($data_fields);
                    }
                }
                curl_setopt($ch, CURLOPT_POSTFIELDS, $data_fields);
            }
            break;
        case 'GET':
        default:
            // 如果为GET方法，将$data_fields转换成查询字符串后附加到$url后面
            $join_char = strpos($url, '?') === false ? '?' : '&';
            $url .= $join_char . http_build_query($data_fields);
            break;
        }

        curl_setopt($ch, CURLOPT_URL, $url);

        if (parse_url($url, PHP_URL_SCHEME) == 'https') {
            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
            curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
        }

        $_time_start = microtime(true);
        $response    = curl_exec($ch);

        if ($method == 'POST') {
            $action = "POST $url with " . json_encode($data_fields);
        } else {
            $action = "GET $url";
        }

        $http_info = curl_getinfo($ch);

        $log_attrs = array('total_time', 'namelookup_time', 'connect_time', 'pretransfer_time', 'starttransfer_time');
        $log_info  = array();
        foreach ($log_attrs as $key) {
            $log_info[$key] = $http_info[$key];
        }

        if ($errno = curl_errno($ch)) {
            $error   = curl_error($ch);
            $log_str = "Error at $action (errno:$errno, error:$error)";
            \Logger::rpc($log_str . ' #httpinfo(' . json_encode($log_info) . ')', microtime(true) - $_time_start, '', '', LOG_RPC_TYPE_CURL);
            return false;
        }
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE); // code 码

        \Logger::rpc('httpCode:' . $httpCode . ' #rpc request(' . json_encode($url) . json_encode($data_fields) . json_encode($response) . ')', microtime(true) - $_time_start, '', '', LOG_RPC_TYPE_CURL);

        $last_url  = $http_info['url'];
        $http_code = $http_info['http_code'];
        $url_log   = $url != $last_url ? ", url:$last_url" : '';
        if ($http_code != 200) {
            $log_str = "Error at HTTP_STATUS $url (http_code:$http_code {$url_log})";
            \Logger::rpc($log_str, microtime(true) - $_time_start, '', '', LOG_RPC_TYPE_CURL);
        }

        curl_close($ch);
        unset($ch);

        return $response;
    }

```



###### application/json请求实例：

```php
  		$url = self::$host . $api_method;
        $jsonStr = json_encode($params, 320);
        $header = array(
            'Content-Type: application/json; charset=utf-8',
            'Content-Length: ' . strlen($jsonStr)
        );

        $options['header'] = $header;
        $ret = BaseFunc::http($url, 'POST', $params, $options);
        $retArr = json_decode($ret, true);
```



#### 1.2GET、POST并发请求

```php
 /**
     * 并发请求
     * @param $promises
     * @param $timeout
     * @return array
     */
    public static function curl_multis($promises, $timeout = 3000): array {
        $_time_start = microtime(true);
        $mh = curl_multi_init();
        $ch = [];
        foreach ($promises as $key => $promise) {
            $ch[$key] = curl_init();
            curl_setopt($ch[$key], CURLOPT_URL, $promise['url']);
            curl_setopt($ch[$key], CURLOPT_HTTP_VERSION, CURL_HTTP_VERSION_1_1);
            curl_setopt($ch[$key], CURLOPT_ENCODING, 'gzip, deflate');
            curl_setopt($ch[$key], CURLOPT_RETURNTRANSFER, true);
            curl_setopt($ch[$key], CURLOPT_TIMEOUT_MS, $timeout);
            curl_setopt($ch[$key], CURLOPT_MAXREDIRS, 2);
            curl_setopt($ch[$key], CURLOPT_NOSIGNAL, true);

            if ($promise['method'] == 'POST') {
                curl_setopt($ch[$key], CURLOPT_POST, true);
                if (isset($promise['headers']) && !empty($promise['headers'])) {
                    curl_setopt($ch[$key], CURLOPT_HTTPHEADER, $promise['headers']);
                    if (strpos(implode(',', $promise['headers']), 'Content-Type: application/json; charset=utf-8') !== false) {

                        $promise['body'] = json_encode($promise['body']);
                    }
                }
                if (is_array($promise['body'])) {
                    $data_fields = http_build_query($promise['body']);
                } else {
                    $data_fields = $promise['body'];
                }
                curl_setopt($ch[$key], CURLOPT_POSTFIELDS, $data_fields);
            }
            if ($promise['method'] == 'GET') {
                // 如果为GET方法，将$data_fields转换成查询字符串后附加到$url后面
                $join_char = strpos($promise['url'], '?') === false ? '?' : '&';
                $promise['url'] .= $join_char . http_build_query($promise['body']);
            }
            curl_multi_add_handle($mh, $ch[$key]);
        }

        do {
            curl_multi_exec($mh, $running);
            curl_multi_select($mh);
        } while ($running > 0);

        // 关闭全部句柄
        $results = [];
        foreach ($promises as $key => $promise) {
            $results[$key] = curl_multi_getcontent($ch[$key]);
            curl_multi_remove_handle($mh, $ch[$key]);
        }
        \Logger::rpc('##curl_multis request(' . "url:" . json_encode($promises) . " ## results:" . json_encode($results) . " ## counts:" . count($promises) . ')', microtime(true) - $_time_start, '', '', LOG_RPC_TYPE_CURL);
        curl_multi_close($mh);
        return $results;
    }

```





###### application/json并发请求实例

```php

        $api_method="/ddj/userapi/nnyc";
        $url=self::$host . $api_method;
        $promises=[];
        foreach($half_year_date as $date_item){
            $promises[] = [
                'url' => $url,
                'method' => "POST",
                'headers' => ['Content-Type: application/json; charset=utf-8'],
                'body' => [
                    'account_id' =>(int)$account_id,
                    'start'=>(int)$date_item[0],
                    'end'=>(int)$date_item[1]
                ],
            ];
        }


        $ret=BaseFunc::curl_multis($promises);

        $result=[];
        foreach($ret as $item){
            $item=json_decode($item,1);
            $records=$item['data']['records']??[];
            if($records){
                $account_id=$item['data']['account_id'];
                array_walk($records,function(&$item)use($account_id){
                    $item['account_id']=$account_id;
                });

                $result=array_merge($result,$records);
            }
        }
       return $result;
```











