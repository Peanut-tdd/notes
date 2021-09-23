# Linux中 du （详解）、 df （详解）和 free（详解）以及它们的区别

**一 、du : 显示每个文件和目录的磁盘使用空间~~~文件的大小。**

```
命令参数
-a   #显示目录中文件的大小  单位 KB 。

-b  #显示目录中文件的大小，以字节byte为单位。

-c  #显示目录中文件的大小，同时也显示总和；单位KB。

-k 、 -m  、#显示目录中文件的大小，-k 单位KB，-m 单位MB.

-s  #仅显示目录的总值，单位KB。

-h  #以K  M  G为单位显示，提高可读性~~~（最常用的一个~也可能只用这一个就满足需求了）
```



查看文件大小

```
du -sh result_hepai.sql 
```
![image-20210915135640676](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20210915135640676.png)



查看文件夹大小

```
du -sh 
```

![image-20210915135529814](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20210915135529814.png)



查看文件夹下各文件大小大小

```
du -sh *
```

![image-20210915135738547](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20210915135738547.png)

查看子文件大小

```
du -h --max-depth=1
```

![image-20210915135911381](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20210915135911381.png)



按文件大小排序

```
du -sh *|sort -hr
```

![image-20210915140333524](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20210915140333524.png)

