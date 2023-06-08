```
server {
        listen 9058;

        server_name ecshop.com;

        charset utf-8;
        include mime.types;
        default_type  application/octet-stream;
        access_log    /data/logs/nginx/ecshop-spt.access.log main;
        error_log     /data/logs/nginx/ecshop-spt.error.log;
        index index.php  index.html index.htm;
  
        location / {
                try_files $uri $uri/ /index.php$is_args$args;
        }
        location  ~  .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
                proxy_store on;
                proxy_store_access user:rw group:rw all:rw;
                access_log off;
                expires 2d;
        }


        location  ~  .*\.(js|css)$ {
                proxy_store on;  #启用缓存到本地的功能
                proxy_store_access user:rw group:rw all:rw;
                access_log off;
                expires 1h;
        }

        location ~ \.php(.*)$ {
                include fastcgi_params;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_param PATH_INFO $fastcgi_path_info;
                fastcgi_pass    127.0.0.1:9000;
                fastcgi_index index.php;
                fastcgi_connect_timeout 5000;
                fastcgi_send_timeout 5000;
                fastcgi_read_timeout 5000;
                fastcgi_buffer_size 1024k;
                fastcgi_buffers 4 1024k;
                fastcgi_busy_buffers_size 1024k;
                fastcgi_temp_file_write_size 1024k;
                fastcgi_intercept_errors on;
                fastcgi_param  SCRIPT_FILENAME  /var/www/html/public$fastcgi_script_name;
        }
}
```

