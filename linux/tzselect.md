# 修改时区时间

**手动修改**

>date 查看本机时间
>
>date -s 2021-08-05 15:28:30

手动修改时区会有误差



**自动同步**

> tzselect
>
> cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime



[参考链接](https://www.cnblogs.com/suiyueshentou/p/7798340.html)：https://www.cnblogs.com/suiyueshentou/p/7798340.html

