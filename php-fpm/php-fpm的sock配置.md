# Linux上php-fpm配置sock连接

**修改php-fpm配置文件**

whereis php-fpm获取配置文件路径/etc/php-fpm.conf	

修改listen=127.0.0.1:9000 为listen=/var/run/php/php-fpm.sock

>;listen = 127.0.0.1:9000
>listen = /var/run/php/php-fpm.sock

以上修改每次重启php-fpm都会报权限错误 :

> 2021/08/04 03:54:05 [crit] 644#644: *1 connect() to unix:/var/run/php/php-fpm.sock failed (13: Permission denied) while connecting to upstream, client: 192.168.33.1, server: localhost, request: "GET / HTTP/1.1", upstream: "fastcgi://unix:/var/run/php/php-fpm.sock:", host: "192.168.33.10"

可以找到php-fpm.sock文件，发现是没有读写权限，直接修改www.conf中的listen.mode = 0777，不用每次重启时，再去chmod 777 php-pfm.sock文件权限



**修改nginx server配置**

修改如下：

>     location ~ \.php$ {
>     #    root           html;
>        # fastcgi_pass   127.0.0.1:9000;
>         fastcgi_pass   unix:/var/run/php/php-fpm.sock;
>         fastcgi_index  index.php;
>         fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
>         include        fastcgi_params;
>     }

