# dockers命令

镜像：

```
docker search nginx //搜索nginx镜像
docker pull training/webapp         //从docker hub 仓库拉取training/webapp镜像，也可从指定的地址仓库中拉取镜像

docker images //查看镜像
docker rmi  training/webapp  		//删除镜像
```



容器：

```
docker run -itd  --name  test-ubuntu ubuntu    //  i交互式操作 t终端 d后台运行  --name 对容器重命名
docker stop container_id  //停止容器
docker start container_id  //重启容器
docker rm container_id //删除容器

```

注:`在执行 docker run 的时候如果添加 --rm 标记，则容器在终止后会立刻删除。注意，--rm 和 -d 参数不能同时使用。`



数据卷和数据卷容器：

```
docker run -itd -v /dbdata ubuntu  //-v 创建一个数据卷容器，可多次使用-v 挂载多个数据卷

docker run -itd --name test-ubuntu -v /home/www:/opt/www  ubuntu      //-v 挂载主机目录作为数据卷

```







端口映射：-P

```
docker run -itd -P --name https-nginx nginx   //随机映射一个主机端口到容器开放的网络端口上

docker ps 
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS             PORTS                                     NAMES
0ee99c171e9e   nginx     "/docker-entrypoint.…"   6 seconds ago       Up 4 seconds       0.0.0.0:49153->80/tcp, :::49153->80/tcp   https-nginx
//将主机的49153端口映射到容器的80端口上

```



端口映射：-p

```
docker run -itd -p hostPort:containerPort nginx  // 指定端口映射

docker run -itd -p ip::containerPort nginx //指定ip地址随机端口映射

docker run -itd -p ip:hostPort:containerPort //指定ip端口映射


docker port container_id //查看端口映射
```













