# 基于filebeat 的module收集nginx日y志

**yum安装filebeat的module目录位置**：`/etc/filebeat/modules.d`。

```
#查看module集合
filebeat modules list 
#开启nginx module
filebeat modules enable nginx
#关闭nginx module
filebeat modules disable nginx
```

![image-20210930113847641](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20210930113847641.png)

配置 modules.d目录下**nginx.yml**文件

```
# Module: nginx
# Docs: https://www.elastic.co/guide/en/beats/filebeat/7.x/filebeat-module-nginx.html

- module: nginx
  # Access logs
  access:
    enabled: true 

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    var.paths: ["/var/log/nginx/access.log"]

  # Error logs
  error:
    enabled: true

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    var.paths: ["/var/log/nginx/error.log"]

  # Ingress-nginx controller logs. This is disabled by default. It could be used in Kubernetes environments to parse ingress-nginx logs
  ingress_controller:
    enabled: false

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    #var.paths:
~               
```



配置**filebeat.yml**文件收集nginx日志

```
filebeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml

  # Set to true to enable config reloading
  reload.enabled: true

  # Period on which files under path should be checked for changes
  reload.period: 10s
  
  
  
  
```





