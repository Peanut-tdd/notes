# php生命周期

当我们请求一个php文件时,PHP 为了完成这次请求，会发生5个阶段的生命周期切换:

模块初始化（MINIT），即调用 php.ini 中指明的扩展的初始化函数进行初始化工作，如 mysql 扩展。

请求初始化（RINIT），即初始化为执行本次脚本所需要的变量名称和变量值内容的符号表，如 $_SESSION变量。_

执行该PHP脚本。

请求处理完成(Request Shutdown)，按顺序调用各个模块的 RSHUTDOWN 方法，对每个变量调用 unset函数，如 unset $_SESSION 变量。

关闭模块(Module Shutdown) ， PHP调用每个扩展的 MSHUTDOWN 方法，这是各个模块最后一次释放内存的机会。这意味着没有下一个请求了。

