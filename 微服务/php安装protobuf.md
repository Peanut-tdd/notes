# php 使用protobuf



**编写person.proto文件**

```
syntax="proto3";            //protobuf版本

package proto;				//包名

message Person{         //定义一个 Message 类似于php的class，go的结构体   需要指定：变量类型、变量名、变量Tag

        string name =3;

        int32 age =2;

        repeated  string address=4;

}

```

**编译文件**

```
protoc --php_out=./ person.proto
```



**使用** ：

```php
<?php
/**
 * Created by PhpStorm.
 * User: Netjoy
 * Date: 2022/11/17
 * Time: 14:18
 */


require 'vendor/autoload.php';      //composer require google/protobuf 引入php-protobuf包  
require 'GPBMetadata/Person.php';  //上一步命令生成文件
require 'Proto/Person.php';  //上一步命令生成文件


//写入
$person = new Proto\Person();

$person->setName("tdd");
$person->setAge(20);
$person->setAddress(["北京", 'shanghai']);

$data = $person->serializeToString(); //序列化
var_dump(strlen($data));//int(25)

$jsonData = $person->serializeToJsonString();
var_dump(strlen($jsonData));//int(55)

file_put_contents('data.bin', $data);

//读取
$fileBinData = file_get_contents("./data.bin");
$getePerson = new \Proto\Person();
$getePerson->mergeFromString($fileBinData);

$jsonArr = [
    'name' => $getePerson->getName(),
    'age' => $getePerson->getAge(),
    'address' => iterator_to_array($getePerson->getAddress())
];

var_dump($jsonArr);

```







