# __toString

当对象被当作字符串对待时，会触发这个魔术方法

只能使用**return**返回一个字符串，否则报**Recoverable fatal error** 致命错误

```
class person{
	

	public function __toString(){
		return "obj" ;
	}

}


$obj=new person();

echo $obj;
```

