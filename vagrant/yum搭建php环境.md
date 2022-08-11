# yum搭建服务端开发环境

**切换阿里云yum源 **

**使用之前请确保已经安装wget，如未安装请执行下面一条命令来安装 yum install -y wget**

```
1）备份当前yum源防止出现意外还可以还原回来
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base-repo.bak
2）使用wget下载阿里yum源repo文件
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo把下载下来阿里云repo文件设置成为默认源
4）生成阿里云yum源缓存
yum makecache
```





# yum 安装nginx

参考nginx[官网](http://nginx.org/en/linux_packages.html)安装



## yum 安装php7.4

1.添加EPEL和REMI存储库

运行以下命令以添加所需的存储库：

```
sudo yum install epel-release
sudo yum -y install https://rpms.remirepo.net/enterprise/remi-release-7.rpm



sudo yum -y install yum-utils   
yum repolist all |grep php  
sudo yum-config-manager --enable remi-php74

sudo yum install php  php-cli php-fpm php-mysqlnd php-zip php-devel php-gd php-mcrypt php-mbstring php-curl php-xml php-pear php-bcmath php-json php-redis


php -v
```







## nginx配置端口访问php

- 修改root 路径
- 修改php匹配设置路径   fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;

```
server {
    listen       80;
    server_name  localhost;
   root /home/www/;                                                     #改路径
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        #root   /usr/share/nginx/html;
        index  index.html index.htm index.php;               #加php文件类型
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
    #    root           html;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name; #改php文件路径配置，读root配置路径
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```

- 修改php-fpm的所属组和用户为nginx





## nginx配置socket访问php

使用Unix域Socket因为不走网络，可以提高Nginx和php-fpm通信的性能。

**php-fpm配置注释端口监听，开启socket**

```
;listen = 127.0.0.1:9000
listen = /var/run/php/php-fpm.sock
;添加其他用户组可执行权限
listen.mode = 0666  
```



```
mdkir -p /var/run/php
chown nginx:nginx /var/run/php #赋予和php一样的用户组权限后，php-fpm的listen.mode不需要加其他用户可执行权限
```



**nginx 代理php配置修改 **

    # fastcgi_pass   127.0.0.1:9000;
    fastcgi_pass   unix:/var/run/php/php-fpm.sock;



**重启php-pfm和nginx**

