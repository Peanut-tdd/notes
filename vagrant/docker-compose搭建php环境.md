# docker-compose 搭建php开发环境

事先安装好docker和docker-compose

```
docker-compose -v  
```



**创建项目:**

```
mdkir compose-php7.4  #创建项目目录
cd compose-php7.4
mkdir nginx			#创建nginx
cd nginx
mkdir conf.d logs  #创建nginx 配置目录和日志目录
```

目录结构如下：

![image-20220811160426443](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20220811160426443.png)





**nginx默认站点配置**

```
server {
    listen       80;
    root   /usr/share/nginx/html;


	access_log /var/log/nginx/default.log;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location / {
        index  index.html index.htm index.php ;
        try_files $uri $uri/ /index.php?$query_string;
        autoindex  on;
    }


    location ~ \.php$ {
        #php74是容器命名
        fastcgi_pass   php-74:9000;
        fastcgi_index  index.php;
        include        fastcgi_params;
        fastcgi_param  PATH_INFO $fastcgi_path_info;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html/$fastcgi_script_name;
    }

}
```



**docker-compose.yml文件配置**

```
#定义docker compose yml版本
version: "3"  
#定义我们的服务对象
services:   
#自定义的服务名称
  nginx:    
     #镜像名称，默认拉取本地镜像，没有的话从远程获取
     image: nginx:latest
     #自定义容器的名称
     container_name: nginx
     #将宿主机的80端口映射到容器的80端口
     ports:
      - "80:80"
     #将宿主机的/home/www目录和容器的/usr/share/nginx/html目录进行绑定，并设置rw权限
     #将宿主机的/home/www/nginx/conf.d/default.conf和容器的/etc/nginx/conf.d/default.conf进行绑定
     volumes:
      - /home/www:/usr/share/nginx/html/:rw
      - /home/www/compose-php7.4/nginx/nginx.conf:/etc/nginx/nginx.conf
      - /home/www/compose-php7.4/nginx/conf.d:/etc/nginx/conf.d
      - /home/www/compose-php7.4/nginx/logs:/var/log/nginx
     #设置环境变量，当前的时区
     environment:
      TZ: "Asia/Shanghai"
     #容器是否随docker服务启动重启
     restart: always
     #容器加入名为lnmp的网络
     networks:
      - lnmp
  php:
     image: php:7.4-fpm
     container_name: php-74
     volumes:
      - /home/www/:/var/www/html/:rw
     restart: always
     cap_add:
          - SYS_PTRACE
     networks:
          - lnmp
networks:   
  #创建了一个自定义的网络叫做lnmp
  lnmp:
```



**执行compose项目**

```
[root@localhost compose-php7.4]# docker-compose up -d
[+] Running 2/2
 ⠿ Container php-74  Started                                                                                                                                                                                          1.1s
 ⠿ Container nginx   Started  
```



检查结果：

![image-20220811161536630](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20220811161536630.png)









