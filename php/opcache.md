## 简述 OpCache 的原理

 PHP执行这段代码会经过如下4个步骤(确切的来说，应该是PHP的语言引擎Zend)

- 1. Scanning(Lexing) ,将PHP代码转换为语言片段(Tokens)
- 2. Parsing, 将Tokens转换成简单而有意义的表达式
-   3. Compilation, 将表达式编译成Opocdes
-   4. Execution, 顺次执行Opcodes，每次一条，从而实现PHP脚本的功能。

现在有的Cache比如APC,可以使得PHP缓存住Opcodes，这样，每次有请求来临的时候，就不需要重复执行前面3步，从而能大幅的提高PHP的执行速度。