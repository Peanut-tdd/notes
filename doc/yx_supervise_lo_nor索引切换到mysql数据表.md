## yx_supervise_lo_nor索引切换到mysql数据表

## 表结构

```Bash
CREATE TABLE `ct_tag_code_ability_new_0` (
  `id` bigint(20) unsigned NOT NULL  auto_increment COMMENT '主键id',
  `sequence` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '序列号',
  `account_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '学生账号ID',
  `user_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '用户ID',
  `subject_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '学科编号',
  `course_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '课程ID',
  `chapter_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '章节ID',
  `session_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '章节缓存id',
  `chapter_type` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '1.常规章 2.试听课',
  `module_code` varchar(32) NOT NULL DEFAULT '' COMMENT '模块code(模块类型编码)',
  `module_type` varchar(32) NOT NULL DEFAULT '' COMMENT '模块类型，枚举：TEST/STUDY',
  `kmodule_code` varchar(32) NOT NULL DEFAULT '' COMMENT '知识模块编码(语法/词汇/全学科)',
  `tag_code` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点编码',
  `infer_tag_code` varchar(32) not null default '' comment '推测源知识点编码',
  `init_master_status` varchar(32) NOT NULL DEFAULT '' COMMENT '初始化掌握状态',
  `master_status` varchar(32) NOT NULL DEFAULT '' COMMENT '掌握状态',
  `init_master_reason` varchar(32) NOT NULL DEFAULT '' COMMENT '初始化掌握原因',
  `master_reason` varchar(32) NOT NULL DEFAULT '' COMMENT '掌握原因',
  `grade` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '知识点年级',
  `stage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '学段',
  `difficult` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '知识点难度',
  `weight` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点考评',
  `learn_type` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点类型',
  `tag` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点标签（BASIC/COMPREHENSIVE/EXTEND）',
  `user_tag` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '学生水平（0学苗，1学中，2学霸，4学种)',
  `date` date DEFAULT NULL COMMENT '日期，精确到天',
  `init_ability` float unsigned NOT NULL DEFAULT '0' COMMENT '初始能力值',
  `final_ability` float unsigned NOT NULL DEFAULT '0' COMMENT '最终能力值',
  `map_code` varchar(32) NOT NULL DEFAULT '' COMMENT '知识图谱code',
  `map_version` varchar(32) NOT NULL DEFAULT '' COMMENT '知识图谱版本',
  `map_type` varchar(32) NOT NULL DEFAULT '' COMMENT '图谱类型',
  `memorize_remain` float(10,2) unsigned NOT NULL DEFAULT '0.00' COMMENT '遗忘复习遗忘量',
  `is_central_lo` tinyint(1) not null default 0 comment '是否为中枢知识点 1为是',
  `origin_lo` tinyint(1) not null default 0 comment '是否章内知识点，1为是',
  `consume_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '消费算法日志流时间',
  `created` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY`idx_account_user_course`(`account_id`,`user_id`,`course_id`,`date`,`chapter_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='归一化-知识点状态过程表';
```

## 建表语句

```Bash
--先创建存储过程

drop PROCEDURE sp_create_tab;

create procedure sp_create_tab()
begin
set @str = "(
  `id` bigint(20) unsigned NOT NULL auto_increment  COMMENT '主键id',
        `unique_key` varchar(255) not null default '' comment '唯一键',
  `sequence` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '序列号',
  `account_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '学生账号ID',
  `user_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '用户ID',
  `subject_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '学科编号',
  `course_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '课程ID',
  `chapter_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '章节ID',
  `session_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '章节缓存id',
  `chapter_type` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '1.常规章 2.试听课',
  `module_code` varchar(32) NOT NULL DEFAULT '' COMMENT '模块code(模块类型编码)',
  `module_type` varchar(32) NOT NULL DEFAULT '' COMMENT '模块类型，枚举：TEST/STUDY',
  `kmodule_code` varchar(32) NOT NULL DEFAULT '' COMMENT '知识模块编码(语法/词汇/全学科)',
  `tag_code` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点编码',
  `infer_tag_code` varchar(32) not null default '' comment '推测源知识点编码',
  `init_master_status` varchar(32) NOT NULL DEFAULT '' COMMENT '初始化掌握状态',
  `master_status` varchar(32) NOT NULL DEFAULT '' COMMENT '掌握状态',
  `init_master_reason` varchar(32) NOT NULL DEFAULT '' COMMENT '初始化掌握原因',
  `master_reason` varchar(32) NOT NULL DEFAULT '' COMMENT '掌握原因',
  `grade` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '知识点年级',
  `stage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '学段',
  `difficult` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '知识点难度',
  `weight` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点考评',
  `learn_type` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点类型',
  `tag` varchar(32) NOT NULL DEFAULT '' COMMENT '知识点标签（BASIC/COMPREHENSIVE/EXTEND）',
  `user_tag` tinyint(3) unsigned NOT NULL DEFAULT '0' COMMENT '学生水平（0学苗，1学中，2学霸，4学种)',
  `date` date DEFAULT NULL COMMENT '日期，精确到天',
  `init_ability` float unsigned NOT NULL DEFAULT '0' COMMENT '初始能力值',
  `final_ability` float unsigned NOT NULL DEFAULT '0' COMMENT '最终能力值',
  `map_code` varchar(32) NOT NULL DEFAULT '' COMMENT '知识图谱code',
  `map_version` varchar(32) NOT NULL DEFAULT '' COMMENT '知识图谱版本',
  `map_type` varchar(32) NOT NULL DEFAULT '' COMMENT '图谱类型',
  `memorize_remain` float(10,2) unsigned NOT NULL DEFAULT '0.00' COMMENT '遗忘复习遗忘量',
  `is_central_lo` tinyint(1) not null default 0 comment '是否为中枢知识点 1为是',
  `origin_lo` tinyint(1) not null default 0 comment '是否章内知识点，1为是',
  `consume_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '消费算法日志流时间',
  `created` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
        key idx_unique_key(unique_key),    KEY`idx_account_user_course`(`account_id`,`user_id`,`course_id`,`date`,`chapter_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='归一化-知识点状态过程表'";
set @j = 0;
while @j < 1000 do
set @table = concat('ct_tag_code_ability_new_',@j);
set @sql_t = concat("CREATE TABLE ",@table,@str);
prepare sql_t from @sql_t;
execute sql_t;
set @j = @j + 1;
end while;
end;




--后执行存储过程
call sp_create_tab;
```

## 预发布环境执行流程

![img](https://b0mzss973k.feishu.cn/space/api/box/stream/download/asynccode/?code=ODZlMGZkODI3ZDdhODk4MzBiOTNlOTBhOGUxOGJiNzBfYklsbWpPYkR4R3dNR1k5aEVrNmp4bG9CUTdFWGtWTEJfVG9rZW46RTJrMmJzZ200b2RMNU94TkVVSWNYQnAybnNiXzE3MzA1MjkzNzY6MTczMDUzMjk3Nl9WNA)

![img](https://b0mzss973k.feishu.cn/space/api/box/stream/download/asynccode/?code=MDJjNDk4YmU5NjhkYmIzMjg1YmZlMzZjYzAxNjI0ODRfVW4xTkpOdmxzQ0N0TThVSmV3Sk4zSHprMXNRWkprb2ZfVG9rZW46SjZjUmJCaGFTbzE4dkF4OGVnWmNmQkxCbnhiXzE3MzA1MjkzNzY6MTczMDUzMjk3Nl9WNA)

预发环境先发版offline_data_v2,执行脚本清洗流程

注：索引累计数据量3亿左右，数据大小101个G

![img](https://b0mzss973k.feishu.cn/space/api/box/stream/download/asynccode/?code=ZGZlODI3ZDg0MjdjZWI3NTE5Zjk2MDE3NTBiYWNhZDJfTXJSeHphQ3hwaENrMkZwd0k2Z0J4UnM5eW1mUFAweHZfVG9rZW46VHM0cmJ1Z01mb1RUVXB4NXQydmNLdUZHbkllXzE3MzA1MjkzNzY6MTczMDUzMjk3Nl9WNA)

### 脚本数据清洗流程

1、预发环境先跑单个索引；看看性能和耗时，评估总时长

2、恢复源代码后,执行后续全量索引清洗，将yx_supervise_lo_nor索引数据通过mq的方式写到mysql新表中

```Bash
offline_data_v2项目
cd ./sdk/Console/ElasticSearch    //脚本目录


 nohup  php SyncLoNorIndexDataToDb.php 202003 2 >> /tmp/SyncLoNorIndexDataToDb.txt 2>&1 & 
 执行202401到202404的索引   
 
 
 nohup  php SyncLoNorIndexDataToDb.php 202008  >> /tmp/SyncLoNorIndexDataToDb.202008.log  2>&1 &
执行202008单个索引
```

3、上线前把当月的增量数据写入mysql中，再执行第1步的操作即可

### 查询及写入流程

再次执行offline_data_v2脚本清洗流程，清洗当月索引，查增量数据写入到mysql

脚本数据清洗完成后，再发版api-stat和

## 日志查看

游标查询日志：cat  basic-be-offlinedatav2-system.2024052014.0.60008.log |grep -i "select_data_list_count"|grep -i "yx_supervise_lo_nor_202010"  单页1000条数据插入查询日志：tail -f basic-be-offlinedatav2-system.2024052014.0.60008.log|grep -i "yx_supervise_lo_nor_202110"|grep -i "MqSyncLoNorIndexDataToDbExecutor:method"

## 添加字段及更新字段脚本

```Bash
分支：feature/bug_fix_20240529

 cd ./sdk/Console/prepare
 
 添加字段执行脚本：
 php AlterSqlExcute.php 
 
 
 
 更新字段执行脚本：
 php UpdateSqlExcute.php
 
```

## 删除4-5月份入学测数据

```Bash
分支：feature/bug_fix_20240529

cd ./sdk/Console/ElasticSearch

 nohup php DeleteLoNorEntranceTestData.php 1 >> /tmp/deleteLoNorEntranceTestData.1.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 2 >> /tmp/deleteLoNorEntranceTestData.2.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 3 >> /tmp/deleteLoNorEntranceTestData.3.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 4 >> /tmp/deleteLoNorEntranceTestData.4.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 5 >> /tmp/deleteLoNorEntranceTestData.5.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 6 >> /tmp/deleteLoNorEntranceTestData.6.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 7 >> /tmp/deleteLoNorEntranceTestData.7.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 8 >> /tmp/deleteLoNorEntranceTestData.8.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 9 >> /tmp/deleteLoNorEntranceTestData.9.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 10 >> /tmp/deleteLoNorEntranceTestData.10.log  2>&1 & 
```

## 更新2024-05-29上线后的module_code=TEST_MODULE,master_reason=RELATED数据

```Bash
分支：feature/bug_fix_20240529

##更新代码
git pull 

cd  ./sdk/Console/ElasticSearch


##更新5月份数据
nohup  php SyncLoNorIndexDataToDb.php 202405  >>/tmp/SyncLoNorIndexDataToDb.2024050604.log  2>&1 &
 
 

##更新6月份数据
nohup  php SyncLoNorIndexDataToDb.php 202406  >>/tmp/SyncLoNorIndexDataToDb.2024050604.log  2>&1 &
```

## 删除1-3月份入学测数据

```Bash
分支：feature/bug_fix_20240529


#拉取最新代码
git pull


cd ./sdk/Console/ElasticSearch


 nohup php DeleteLoNorEntranceTestData.php 1 >> /tmp/deleteLoNorEntranceTestData.06051.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 2 >> /tmp/deleteLoNorEntranceTestData.06052.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 3 >> /tmp/deleteLoNorEntranceTestData.06053.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 4 >> /tmp/deleteLoNorEntranceTestData.06054.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 5 >> /tmp/deleteLoNorEntranceTestData.06055.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 6 >> /tmp/deleteLoNorEntranceTestData.06056.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 7 >> /tmp/deleteLoNorEntranceTestData.06057.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 8 >> /tmp/deleteLoNorEntranceTestData.06058.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 9 >> /tmp/deleteLoNorEntranceTestData.06059.log  2>&1 & 
 
 nohup php DeleteLoNorEntranceTestData.php 10 >> /tmp/deleteLoNorEntranceTestData.060510.log 2>&1 & 
```

## 更新24年1-3月份入学测数据

```Bash
分支：feature/bug_fix_20240529


cd  ./sdk/Console/ElasticSearch


##更新1月份数据
nohup  php SyncLoNorIndexDataToDb.php 202401  >>/tmp/SyncLoNorIndexDataToDb.2024010605.log  2>&1 &
 
 

##更新2月份数据
nohup  php SyncLoNorIndexDataToDb.php 202402  >>/tmp/SyncLoNorIndexDataToDb.2024020605.log  2>&1 &



##更新3月份数据
nohup  php SyncLoNorIndexDataToDb.php 202403  >>/tmp/SyncLoNorIndexDataToDb.2024030605.log  2>&1 &
```