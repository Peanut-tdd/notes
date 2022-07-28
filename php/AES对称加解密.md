# AES加解密

在php5.*之前使用的是mcrypt_decrypt函数，从php7.1后废弃了，我们使用openssl_encrypt来实现加密和使用openssl_decrypt实现解密



一：openssl_encrypt加密方法详解

```
openssl_encrypt($data,$method,$aes_key,$options=0,$iv='',&$tag=null,$aad='',$tag_length=16)
```

参数说明：

$data：待加密明文

$method:加密方法，可以通过openssl_get_cipher_methods()获取有哪些加密方式

$aes_key:加密aes_key

$options:是以下标记的按位或： **`OPENSSL_RAW_DATA`** 、 **`OPENSSL_ZERO_PADDING`**

iv:非NULL的初始化向量（可以是key的16位字符串，`substr($key,0,16)`）











二：openssl_decrypt解密方法详解

```
openssl_decrypt($data,$method,$aes_key,$options=0,$iv='',$tag='',$ad='')
```

$data:待解密数据

$method:解密方法

$aes_key:加解密aes_key

$options:是以下标记的按位或： **`OPENSSL_RAW_DATA`** 、 **`OPENSSL_ZERO_PADDING`**

iv:非NULL的初始化向量（可以是key的16位字符串，`substr($key,0,16)`）









三：填充和移除填充算法

```
/**
 * 填充算法
 * @param string $source
 * @return string
 */
function addPKCS7Padding($source) {
    $source = trim($source);
    $block =32;  //设置长度
    $pad = $block - (strlen($source) % $block);
    if ($pad <= $block) {
        $char = chr($pad);
        $source .= str_repeat($char, $pad);
    }  
    return $source;
}

/**
 * 移去填充算法
 * @param string $source
 * @return string
 */
function stripPKSC7Padding($source) {
      $pad = ord(substr($text, -1));
        if ($pad < 1 || $pad > strlen($source)) {
            $pad = 0;
        }
        return substr($text, 0, (strlen($text) - $pad));
}

```



