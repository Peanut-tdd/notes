# 安装redis扩展

安装phpize模块

```
yum -y install php-devel


whereis phpize
whereis php-config
```







下载解压redis扩展

```
wget http://pecl.php.net/get/redis-5.3.4.tgz
tar -zxvf redis-5.3.4.tgz
cd redis-5.3.4

/usr/bin/phpize        #执行phpize
./configure --with-php-config=PATH #PATH为php的地址，配置环境
make && make install # 编译

cd /etc/php.ini
extension=redis.so #添加扩展
```







