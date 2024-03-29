# php 使用protobuf

Protocol Buffers 是 google 的一种数据交换的格式，它独立于语言，独立于平台。提供了多种语言的实现：java、c#、c++、go 和 python，每一种实现都包含了相应语言的[编译器](https://so.csdn.net/so/search?q=编译器&spm=1001.2101.3001.7020)以及库文件。由于它是一种二进制的格式，比使用 xml 进行数据交换快许多。



**编写person.proto文件**

```
syntax="proto3";		

package proto;

message Person{			//定义一个 Message	需要指定：变量类型、变量名、变量Tag

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

```
<?php
/**
 * Created by PhpStorm.
 * User: Netjoy
 * Date: 2022/11/17
 * Time: 14:18
 */


require 'vendor/autoload.php';    	//composer 引入的php-protobuf包
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







