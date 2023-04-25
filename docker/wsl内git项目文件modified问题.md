# wsl内git项目文件未修改但是显示modified

git diff 文件名

![image-20230425112848517](C:\Users\D1M\AppData\Roaming\Typora\typora-user-images\image-20230425112848517.png)





# 解决办法

将git的core.filemode设置为false即可，命令：

> git config core.filemode false



[参考链接](https://blog.csdn.net/qq285744011/article/details/111225180)