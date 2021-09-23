# php错误异常处理

**PHP的错误报告有三种**：

1、错误(error),语法解析错误，致命错误

2、警告(warning)

3、注意(notice)

后果：

错误 -> 致命错误，会终止已下程序的执行，语法错误的话，PHP压根就没执行。

警告 -> 他不会终止运行，但会影响结果。

注意 -> 不会终止执行，也不会影响结果。

为了让用户得到更好的体验，我们屏蔽所有错误的输出，是输出，而不是显示。但这样的话，管理员也看不到错误了。页面上不显示错

误，而将错误生成一个日志，提供给管理员查看。



**error_reporting(~E_ALL)**将所有输出都屏蔽掉了，自然，管理员也看不到了。我不要屏蔽所有输出，我只要屏蔽所有显示

```
// 关闭所有PHP错误报告
error_reporting(0);
 
// 报告简单的运行错误
error_reporting(E_ERROR | E_WARNING | E_PARSE);
error_reporting(7);
 
// 报告 E_NOTICE 也挺好 (报告未初始化的变量或者捕获变量名的错误拼写)
error_reporting(E_ERROR | E_WARNING | E_PARSE | E_NOTICE);
 
// 除了 E_NOTICE，报告其他所有错误
// 这是在 php.ini 里的默认设置
error_reporting(E_ALL ^ E_NOTICE);
 
// 报告所有 PHP 错误 (参见 changelog)
error_reporting(E_ALL);
 
// 报告所有 PHP 错误
error_reporting(-1);
 
// 和 error_reporting(E_ALL); 一样
ini_set('error_reporting', E_ALL);//显示所有错误
```



**ini_set('display_errors','off') **;表示屏蔽了所有的页面上错误的显示，但没有屏蔽错误的输出。

**ini_set("display_errors", "on")**;//打开错误提示
**ini_set("error_reporting",E_ALL)**;//显示所有错误



**异常**：

在 PHP语言里，所有异常都必须自己抛出，而不像 JAVA 之类的语言会自动抛出异常，这也正是 PHP源代码里很少看到异常处理语句的

原因之一。

异常与错误：

异常是指程序运行中不符合预期情况以及与正常流程不同的状况。错误则属于自身问题，是一种非法语法或者环境问题导致的、让编译器

无法通过检查设置无法运行的情况。

php只有在你throw 一个异常后，才能用try...catch来捕获异常(一般情况下如此，也有部分异常可以自动捕获)。

```
function exception($a){
	try{
		if($a==1){
			check();
		}

	}catch(\Exception $e){
		echo $e->getMessage();
	}
}

function check(){
	return 11111;
}

exception(1);//输出空


function exception($a){
	try{
		if($a==1){
			check();
		}

	}catch(\Exception $e){
		echo $e->getMessage();
	}
}

function check(){
	throw new \Exception('1111');
}

exception(1); //输出1111

```



