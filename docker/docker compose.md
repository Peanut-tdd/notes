# docker compose 

## pip安装

安装步骤

因为docker compose 是python 编写的，故先安装python 及python 软件包管理工具pip

安装python

```
yum install python36
```

安装pip

```
pip --version     # Python2.x 版本命令 
pip3 --version    # Python3.x 版本命令



$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py   # 下载安装脚本
$ sudo python get-pip.py    # 运行安装脚本

$ sudo python3 get-pip.py    # 运行安装脚本。//python3 安装


ln -s /usr/local/bin/pip /usr/bin/pip   //添加全局变量
```

安装docker compose

```
sudo pip install docker-compose
```





## 脚本安装

```
curl -SL https://github.com/docker/compose/releases/download/v2.7.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

- 添加可执行权限

```
sudo chmod +x /usr/local/bin/docker-compose
```

- 建立软连接

```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

- 测试

```
[root@localhost bin]# docker-compose -v
Docker Compose version v2.7.0
```









