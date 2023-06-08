# mysqldump导入导出数据

## 导出其他ip数据库数据到本地

1. **导出education数据库里面的users表的表数据和表结构（下面以users表为例）**

   **格式：**mysqldump -u[用户名] -h[ip] -p[密码] -P[端口号] 数据库名 表名 >导出的文件名.sql

   **命令 ：** mysqldump -hip -uusername -ppassword database_name db_name > D:/path/database/db_name.sql

   **例**：mysqldump -uroot -h127.0.0.1 -proot -P3306 education users>d:/user.sql 
   
   **导出多张表：**mysqldump -uroot -proot --databases test --tables t1 t2>two.sql

2. **只导出表结构不导表数据，添加“-d”命令参数**

   **例：**mysqldump -uroot -h127.0.0.1 -proot -P3306 -d education users>d:/user.

3. **只导出表数据不导表结构，添加“-t”命令参数**

   **例：**mysqldump -uroot -h127.0.0.1 -proot -P3306 -t education users>d:/user.sql

4. **导出数据库的表结构和表数据（导入的时候需要指定数据库，保证指定的数据库存在）**

   **导出命令：**mysqldump -h127.0.0.1 -P3306 -uroot -proot education >d:/database.sql

   **导入命令：**mysql -uroot -proot -h127.0.0.1 -P3306 education<d:/database.sql

mysqldump -u f12e062e-65e1-4eb8-944f-d5adb8adac26 -psRURW7ZbtLAUEXkK -h js.agll.biz -P 33060 ecshop_suntory > /data/db.sql

## 导入本地数据库备份到其他ip数据库（数据库要先创建）

1. 方法一：**系统命令行**	

   **格式：**mysql -h[ip] -P[(大写)端口] -u[用户名] -p[密码] [数据库名] < d:XX.sql(路径) 

   **命令：** ``` mysql -hip -uusername -ppassword -Pport(端口号)database_name < D:/path/database.sql```

   **例：** ``` mysql -h10.0.55.16 -uloulan_test -plou-Test_123 -P4000 loulan < D:/loulan.sql`

   ​	mysql -h47.100.27.206 -udb_rw -pYC5H7@Uw -P3306 spt_test < /data/db.sql

2. 方法二：**mysql命令行**

   **格式：** mysql -h主机地址 -u用户名 －p用户密码
   
   **命令：**  
   
   >mysql -h10.0.0.10 -uroot -proot
   >
   >mysql> use test;
   >mysql> source /home/test/database.sql

**参考链接:**

https://blog.csdn.net/u012436346/article/details/86570902



mysqldump -h47.100.27.206 -udb_rw -p"YC5H7@Uw" -P3306 ecshop_suntory > /logs/ecshop.sql



 
