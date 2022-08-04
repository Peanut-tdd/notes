# laravel crontab 调度任务配置

\* * * * * php_path web_path/artisan schedule:run >> /dev/null 2>&1

- php_path ：php命令路径      whereis php查找
- web_path     项目路径