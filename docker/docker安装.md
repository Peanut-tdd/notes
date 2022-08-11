# docker安装

**脚本安装docker**

```
#安装docker
[root@localhost]# curl -sSL https://get.daocloud.io/docker | sh
#重启
[root@localhost]# systemctl restart docker.service
#开机启动
[root@localhost]# systemctl enable docker.service
#查看是否启动
[root@localhost]# systemctl status docker.service
```

