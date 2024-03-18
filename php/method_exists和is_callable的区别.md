# method_exists和is_callable的区别

检查对象或者类内的方法是否存在

```
class Foo {
    public function PublicMethod(){}
    private function PrivateMethod(){}
    public static function PublicStaticMethod(){}
    private static function PrivateStaticMethod(){}
}
$foo = new Foo();
$callbacks = array(
    array($foo, 'PublicMethod'),
    array($foo, 'PrivateMethod'),
    array($foo, 'PublicStaticMethod'),
    array($foo, 'PrivateStaticMethod'),
    array('Foo', 'PublicMethod'),
    array('Foo', 'PrivateMethod'),
    array('Foo', 'PublicStaticMethod'),
    array('Foo', 'PrivateStaticMethod'),
    array('Foo','testMethdo'),
   );
foreach ($callbacks as $callback){
    var_dump($callback);
    var_dump(method_exists($callback[0], $callback[1]));
    var_dump(is_callable($callback));
    echo str_repeat('-', 10);
    echo '<br />';
}

```

运行结果：

```
array(2) { [0]=> object(Foo)#1 (0) { } [1]=> string(12) "PublicMethod" } bool(true) bool(true) ----------
array(2) { [0]=> object(Foo)#1 (0) { } [1]=> string(13) "PrivateMethod" } bool(true) bool(false) ----------
array(2) { [0]=> object(Foo)#1 (0) { } [1]=> string(18) "PublicStaticMethod" } bool(true) bool(true) ----------
array(2) { [0]=> object(Foo)#1 (0) { } [1]=> string(19) "PrivateStaticMethod" } bool(true) bool(false) ----------
array(2) { [0]=> string(3) "Foo" [1]=> string(12) "PublicMethod" } bool(true) bool(true) ----------
array(2) { [0]=> string(3) "Foo" [1]=> string(13) "PrivateMethod" } bool(true) bool(false) ----------
array(2) { [0]=> string(3) "Foo" [1]=> string(18) "PublicStaticMethod" } bool(true) bool(true) ----------
array(2) { [0]=> string(3) "Foo" [1]=> string(19) "PrivateStaticMethod" } bool(true) bool(false) ----------
array(2) { [0]=> string(3) "Foo" [1]=> string(10) "testMethdo" } bool(false) bool(false) ----------
```

可见，is_callable不仅判断方法是否存在，同时也会判断可见性。





如果一个对象存在魔术方法`__call`，在进行方法判断时`method_exists()`会返回FALSE，而`is_callable()`会返回TRUE。

```
class Foo{
	function __call($function,$args){
		echo "方法名：".$function.",参数:".implode(",",$args);
	}
}

$obj=new Foo();

$obj->fun1("tdd",100);
echo '<br>';
var_dump(method_exists($obj,'fun1'));

var_dump(is_callable(array($obj,'fun1')));
```



运行结果：

```
方法名为：func1,参数为：name,100
bool(false) bool(true) 
```

