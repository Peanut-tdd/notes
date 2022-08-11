# yum安装mysql

## 1、安装链接：https://www.cnblogs.com/shipment/p/14325309.html#1%E5%8E%BB%E5%AE%98%E7%BD%91%E4%B8%8B%E8%BD%BDyum%E4%BB%93%E5%BA%93%E6%96%87%E4%BB%B6

------



## 2、获取root账号密码

`grep 'temporary password' /var/log/mysqld.log`

### 修改root 账号密码

```
mysql -uroot -p 初始密码； #登录

alter user   root@localhost   identified  by  '你的密码';#修改语句
```

在my.cnf配置文件中添加如下配置可去除密码的复杂度：

```
plugin-load=validate_password.so 
validate-password=OFF
```

重启配置。



## 3、mysql外部可访问配置：http://wxnacy.com/2018/06/16/mysql-vagrant-connect/

### 去除127.0.0.1绑定

---

如果你是使用 `rpm` 来安装的话，修改 `/etc/mysql/mysql.conf.d/mysql.cnf`，将绑定 `127.0.0.1` 这一行注释掉

> \# bind-address            = 127.0.0.1

### 对所有 ip 开放

---

登陆 Mysql 并对所有外网 ip 开放权限

> ```
> mysql -uroot -p
> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_mysql_password' WITH GRANT OPTION;
> ```

**注意**

如果在生产环境上的话建议只对指定 ip 开放权限

> GRANT ALL PRIVILEGES ON *.* TO 'root'@'213.1.2.1' IDENTIFIED BY 'your_mysql_password' WITH GRANT OPTION;

