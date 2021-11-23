

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
ini_set('error_reporting', E_ALL);//报告所有错误
```



**ini_set('display_errors','off') **;表示屏蔽了所有的页面上错误的显示，但没有屏蔽错误的输出。

**ini_set("display_errors", "on")**;//打开错误提示
**ini_set("error_reporting",E_ALL)**;//显示所有错误



例：

```
//以下两步设置不会显示php错误报告，并且页面空白
error_reporting(0)； //关闭所有php错误报告
ini_set("display_errors"，1)// 开启错误显示  

//以下两步也不会显示错误报告，并且页面显示”网页无法正常运行 HTTP ERROR 500"
error_reporting(1);
ini_set("display_errors",0);

//以下设置可以显示php错误报告
error_reporting(1);
ini_set("display_errors",1);


```





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

----





**顶层异常处理器:set_exception_handler**

在我们实际开发中，异常捕捉仅仅靠 try {} catch () 是远远不够的。set_exception_handler() 函数可设置处理所有未捕获异常的用户定义函数。

使用**set_exception_handler**方法来设置用户自定义的异常处理函数

```
function exception_handler($exception) {
  echo "Uncaught exception: " , $exception->getMessage(), "\n";
  echo "file:",$exception->getFile(),"\n";
  
  echo "line:",$exception->getLine();
  
  
}

set_exception_handler('exception_handler');  //入参自定义的函数方法

throw new Exception('Uncaught Exception');

echo "Not Executed\n";

```





**PHP 内置的异常处理类**

```

class Exception  {    
protected $message = 'Unknown exception';   // 异常信息    
protected $code = 0;                        // 用户自定义异常代码     
protected $file;                            // 发生异常的文件名    
protected $line;                            // 发生异常的代码行号     
function __construct($message = null, $code = 0){
	
} 
final function getMessage(){}                // 返回异常信息     
final function getCode(){}                   // 返回异常代码     
final function getFile(){}                   // 返回发生异常的文件名      
final function getLine(){}                  // 返回发生异常的代码行号      
final function getTrace(){}                  // backtrace() 数组      
final function getTraceAsString(){}         // 已格成化成字符串的 getTrace() 信息     
/* 可重载的方法 */     
function __toString(){}                       // 可输出的字符串 

}  
```



**自定义异常类：**

```

class MyException extends Exception  {   


	// 重定义构造器使 message 变为必须被指定的属性      
	public function __construct($message, $code = 0) {         
		// 自定义的代码          
		// 确保所有变量都被正确赋值         
		parent::__construct($message, $code);  
	} 
    
	
	// 自定义字符串输出的样式 */     
	public function __toString() {         
		return __CLASS__ . ": [{$this->code}]: {$this->message}\n";   
	}    
	
	
	public function customFunction() {         
		echo "A Custom function for this type of exception\n";   
	}  
	
} 

```





**错误抛出**

除了系统运行时php主动抛出的意外错误。我们可以使用trigger_error产生的一个自定义用户级别的error/warning/notice错误信息。





**顶级错误处理器：set_error_handler**

顶级错误处理器 set_error_handler 一般用于捕捉 E_NOTICE 、E_USER_ERROR、E_USER_WARNING、E_USER_NOTICE 级别的错误，不能捕捉 E_ERROR, E_PARSE, E_CORE_ERROR, E_CORE_WARNING, E_COMPILE_ERROR 和 E_COMPILE_WARNING。

例：

```
//捕获notice等错误
function _error_handler($errno, $errstr ,$errfile, $errline)
{
    echo "错误编号errno: $errno<br>";
    echo "错误信息errstr: $errstr<br>";
    echo "出错文件errfile: $errfile<br>";
    echo "出错行号errline: $errline<br>";
}
 
set_error_handler('_error_handler');  // 注册错误处理方法来处理所有错误
 
 
echo $foo['bar'];  // 由于数组未定义，会产生一个notice级别的错误

```





**致命错误处理器：register_shutdown_function**

**register_shutdown_function**函数可实现当程序执行完成后执行的函数，其功能为可实现程序执行完成后的后续操作。

我们前面说过，set_error_handler 能够捕捉的错误类型有限，很多致命错误例如解析错误等都无法捕捉，但是这类致命错误发生时，PHP 会调用 register_shutdown_function 所注册的函数，如果结合函数 error_get_last，就会获取错误发生的信息。

可以这样理解调用条件：

- 当页面被用户强制停止时
- 当程序代码运行超时时
- 当ＰＨＰ代码执行完成时，代码执行存在异常和错误、警告





例：

```

function shutdown(){
	if($error=error_get_last()){
		echo "message:".$error['message']."\n";
		
		echo "line:",$error['line'],"\n";
	}
}



register_shutdown_function('shutdown');
aa();//执行一个不存在的方法
```







---

**laravel异常处理**

laravel 的异常处理由类 \Illuminate\Foundation\Bootstrap\HandleExceptions::class 完成：



**PHP7 实现了一个全局的 throwable 接口，原来的 Exception 和部分 Error 都实现了这个接口**， 以接口的方式定义了异常的继承结构。于是，**PHP7 中更多的 Error 变为可捕获的 Exception** 返回给开发者，如果不进行捕获则为 Error ，如果捕获就变为一个可在程序内处理的 Exception。这些可被捕获的 Error 通常都是不会对程序造成致命伤害的 Error，例如函数不存在。

PHP7 中，基于 /Error exception，派生了 5 个新的 engine exception：ArithmeticError / AssertionError / DivisionByZeroError / ParseError / TypeError。在 PHP7 里，无论是老的 /Exception 还是新的 /Error ，**它们都实现了一个共同的 interface: /Throwable**。



```
  
class HandleExceptions{  


	public function bootstrap(Application $app){  
    
		  $this->app = $app;  
		  
		  error_reporting(-1);   
		  
		  set_error_handler([$this, 'handleError']);
		  
		  set_exception_handler([$this, 'handleException']);  
		  
		  register_shutdown_function([$this, 'handleShutdown']);


		  if (! $app->environment('testing')) {           
			ini_set('display_errors', 'Off');        
		  } 
		  
	}
}




public function handleException($e){ 

	   if (! $e instanceof Exception) {       
		   $e = new FatalThrowableError($e);  
	   } 
	   
	   $this->getExceptionHandler()->report($e); 
	   
	   if ($this->app->runningInConsole()) {      
		$this->renderForConsole($e);
		
	   } else { 
	   $this->renderHttpResponse($e);   
		}
}




class FatalThrowableError extends FatalErrorException{    


	public function __construct(\Throwable $e){ 
	   if ($e instanceof \ParseError) {   
		   $message = 'Parse error: '.$e->getMessage();     
		   $severity = E_PARSE;       
	   } elseif ($e instanceof \TypeError) {  
	   
		   $message = 'Type error: '.$e->getMessage();      
		   $severity = E_RECOVERABLE_ERROR;
		   
	   } else {      
		   $message = $e->getMessage();          
		   $severity = E_ERROR;       
	   }     
	   
	   \ErrorException::__construct(message, $e->getCode(),$severity, $e->getFile(),$e->getLine()); 
	   $this->setTrace($e->getTrace());  
	}
	   
}

```













