## kubectl命令

#### 1.1 获取全部namespace

```
kubectl get ns
```



#### 1.2 创建test namespace

```
kubectl create ns test
```



#### 1.3 删除test namespace

```
kubectl delete ns test
```



#### 1.4通过文件创建namespace

```
kubectl apply -f xxxxx.yaml
```









#### 2.1获取全部命名空间的pod

```
kubectl get pod -A
```



#### 2.2 获取test命名空间的pod

```
kubectl get pod -n test
```



#### 2.3查看某个pod的运行信息

```
kubectl describe pod nginx
```



#### 2.4 删除test命名空间的pod

```
kubectl delete pod nginx -n test
```







#### Deployment 管理pod

#### 3.1 test 命名空间创建deployment，指定pod镜像为nginx

```
kubectl create deployment nginx-deployment -n test  --image=nginx
```



#### 3.2查看deployment

```
kubectl get deployment -n test
```



#### 3.3 删除deployment

```
kubectl delete deployment nginx-deployment  -n test 
```



[Deployment yml文件demo链接](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/)









#### 4.1Service映射内部可访问deployment

```
kubectl expose deployment nginx-deloyment --port=8888 --target-port=80  -n test   #port service对内暴露的端口号
```



#### 4.2查看test 命名空间service

````
kubectl get service -n test 
````



#### 4.3 删除service

```
kubectl delete service nginx-deloyment -n test
```



#### 4.4Service映射外部可访问deployment

```
kubectl expose deployment nginx-deloyment --port=8888 --target-port=80  -n test --type=Nodeport   service对外暴露的端口   //浏览器访问方式:  k8s服务器的ip:端口号
```





#### Ingress

为什么用ingress?

```
service的ip地址会变,ingress相当于nginx
```









