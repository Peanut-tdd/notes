# wsl相关配置

wsl内Ubuntu登录密码：tdd520

powershell通过wsl2进入ububtu:
wsl -d ububtu

win进入ububtu路径：
\\\wsl$\Ubuntu

给文件赋值权限
sudo chown -R $USER /www #给指定目录赋予当前用户权限





## WINDOWS下的wsl如何设置Linux子系统默认用户为root

**处理普通用户在phpstorm内没有文件操作权限**

1、只需要在C:\Users\用户名\AppData\Local\Microsoft\WindowsApps路径下确认所安装的Linux子系统具体的版本号



2、然后打开CMD,输入XXXXXX版本号.exe config --default-user root运行





docker run -p 3306:3306 --name mysql -v /mysql/conf:/etc/mysql/conf.d -v /mysql/logs:/logs \
-v /mysql/data:/var/lib/mysql\
-e MYSQL_ROOT_PASSWORD=123456\
-d mysql:5.7





```
git config --global core.autocrlf false
git config --global core.safecrlf false
```





