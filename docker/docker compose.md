# docker compose 

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



添加自动补全

```
curl -L https://raw.githubusercontent.com/docker/compose/1.2.0/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```



