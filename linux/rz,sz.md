# 上传下载服务器文件

**一．在使用SecureCRT上传下载之前需要给服务器安装lrzsz：**
yum -y install lrzsz (注：参数-y中"y"的意思是：当安装过程提示选择全部为"yes")



**二、命令sz、rz的使用方法：**

rz中的r意为received（接收），输入rz时、意为服务器接收文件，既将文件从本地上传到服务器。
sz中的s意为send（发送），输入sz时、意为服务器要发送文件，既从服务器发送文件到本地，或是说本地从服务器上下载文件。
注：不论是send还是received，动作都是在服务器上发起的。



**rz用法：**

注：上传的时候，如果上传到的linux目录有同名的文件，是无法上传的，需要先删掉linux上的同名文件。

输入rz，回车后，会出现文件选择对话框，选择需要上传文件，一次可以指定多个文件，上传到服务器的路径为当前执行rz命令的目录。



**sz用法**

      下载一个文件：sz filename
      下载多个文件：sz filename1 filename2
      下载dir目录下的所有文件，不包含dir下的文件夹：sz dir/*





**三、设置上传、下载文件的默认目录**

文件上传、下载存放的默认位置在securtCRT中设置，位于：
英文版 options — session options — X/Y/Zmodem。
中文版 选项— 会话选项— X/Y/Zmodem。