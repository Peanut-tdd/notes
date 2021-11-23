# dockers命令

镜像：

```
docker pull training/webapp         //从docker hub 仓库拉取training/webapp镜像，也可从指定的地址仓库中拉取镜像
docker images //查看镜像
docker rmi  training/webapp  		//删除镜像
```



容器：

```
docker run -itd ubuntu    //  i交互式操作 t终端 d后台运行
docker stop container_id  //停止容器
docker start container_id  //重启容器
docker rm container_id //删除容器

```

