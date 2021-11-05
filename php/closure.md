# php closure 匿名函数

*提到闭包就不得不想起匿名函数，也叫闭包函数（closures），貌似PHP闭包实现主要就是靠它。声明一个匿名函数是这样：*

```
$func=function(){
		
};	//带结束符
```

可以看到，匿名函数因为没有名字，如果要使用它，需要将其返回给一个变量。匿名函数也像普通函数一样可以声明参数，调用方法也相同： 

```
$func=function($str){
	echo $str;
};
$func("hello world");   //输出hello world
```

顺便提一下，PHP在引入闭包之前，也有一个可以创建匿名函数的函数：create function，但是代码逻辑只能写成字符串，这样看起来很晦涩并且不好维护，所以很少有人用。 





**实现闭包
**将匿名函数在普通函数中当做参数传入，也可以被返回。这就实现了一个简单的闭包。



**下边有三个例子**

```
//例一
//在函数里定义一个匿名函数，并且调用它
function printStr() {
    $func = function( $str ) {
        echo $str;
    };
    $func( 'some string' );
}
printStr();


//例二
//在函数内返回匿名函数，并且调用它

function getClosureFunction(){
	$func=function($str){
		echo $str;
	};
	return $func;
}

$closureFunction=getClosureFunction();

$closureFunction('hello world');


//例三
把匿名函数当作参数使用
function callFunc($str){
	$str("hello world");
}
//第一种调用
$func=function($str){
	echo $str;
};

callFunc($func);  

//第二种调用
callFunc(function($str){
	echo "hello world";
});

//使用匿名类
function callFunc($str){
	
	return (new class($str){        //匿名类入参
			function __construct($closure){
				$closure();	
			}
			
			function init(){
				echo "init";
			}
			
			function __destruct(){
				echo "success";
			}
	})->init();


}



$str="hello";
callFunc(function()use($str){
	echo $str;
});

```



连接闭包和外界变量的关键词：use

```
function getMoney() {
    $rmb = 1;
    $dollar = 6;
    $func = function() use ( $rmb ) {
        echo $rmb;
        echo $dollar;
    };
    $func();
}getMoney();
//输出：
//1
//报错，找不到dorllar变量
```

闭包可以保存所在代码块上下文的一些变量和值。PHP在默认情况下，匿名函数不能调用所在代码块的上下文变量，而需要通过使用use关键字。 换一个例子看看：可以看到，dollar没有在use关键字中声明，在这个匿名函数里也就不能获取到它，所以开发中要注意这个问题。



 有人可能会想到，是否可以在匿名函数中改变上下文的变量，但我发现是不可以的：

```
function getMoney() {
    $rmb = 1;
    $func = function() use ( $rmb ) {
        echo $rmb;
        //把$rmb的值加1
        $rmb++;
    };
    $func();
    echo $rmb;
}getMoney();
//输出：
//1
//1
```



啊，原来use所引用的也只不过是变量的一个副本而已。但是我想要完全引用变量，而不是复制。 要达到这种效果，其实在变量前加一个 & 符号就可以了：

```
function getMoney() {
    $rmb = 1;
    $func = function() use ( &$rmb ) {
        echo $rmb;
        //把$rmb的值加1
        $rmb++;
    };
    $func();
    echo $rmb;
}getMoney();
//输出：
//1
//2
```

好，这样匿名函数就可以引用上下文的变量了。如果将匿名函数返回给外界，匿名函数会保存use所引用的变量，而外界则不能得到这些变量，这样形成‘闭包'这个概念可能会更清晰一些。

```
function getMoneyFunc() {
    $rmb = 1;
    $func = function() use ( &$rmb ) {
        echo $rmb;
        //把$rmb的值加1
        $rmb++;
    };
    return $func;
}$getMoney = getMoneyFunc();
$getMoney();
$getMoney();
$getMoney();
//输出：
//1
//2
//3
```

