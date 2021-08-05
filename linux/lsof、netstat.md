# linux lsof/netstat查看进程和端口号相关命令

**lsof -i:port 端口查进程**

> [root@localhost nginx]# lsof -i:3306                    
> COMMAND PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
> mysqld  812 mysql   29u  IPv6  17924      0t0  TCP *:mysql (LISTEN)

**lsof -i |grep pid 进程查端口**

> [root@localhost nginx]# lsof -i|grep 812
> mysqld    812   mysql   29u  IPv6  17924      0t0  TCP *:mysql (LISTEN)



**netstat -ltnp |grep port 端口查进程**

> [root@localhost nginx]# netstat -ltnp |grep 80
> tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      4132/nginx: master  
> tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      808/master          
> tcp6       0      0 ::1:25                  :::*                    LISTEN      808/master 



**netstat -ltnp|grep pid 进程查看端口号**

> [root@localhost nginx]# netstat -ltnp|grep 4132
> tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      4132/nginx: master  

80是端口

