# 批量创建mysql数据表

```
--先创建存储过程(执行一次就行了)
create procedure sp_create_tab()
begin
set @str = "(
  `id` bigint(20) unsigned NOT NULL auto_increment  COMMENT '主键id',
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
  `created` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY`idx_account_user_course`(`account_id`,`user_id`,`course_id`,`date`,`chapter_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='归一化-知识点状态过程表'";
set @j = 0;
while @j < 1000 do
set @table = concat('ct_tag_code_ability_new_',@j);
set @sql_t = concat("CREATE TABLE ",@table,@str);
prepare sql_t from @sql_t;
execute sql_t;
set @j = @j + 1;
end while;
end

```



```
--后执行存储过程
call sp_create_tab;
```

批量创建1000个