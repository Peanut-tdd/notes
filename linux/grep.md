# grep 查询



### grep 目录查询

```
ll|grep "xxxx" 
```





### grep文件关键词查询

```
grep -rn "关键词" 路径
grep "xxxxxx" ./*
```

- `-i`：忽略大小写进行匹配。

- `-n`：显示匹配行的行号。

- `-r`：递归查找子目录中的文件。

- `-l`：只打印匹配的文件名。

- `-c`:   显示匹配到的结果的总数

- `-w`    精确查找，不模糊

### grep 上下文查询

```
grep -C 5  file  显示file文件中匹配test字串那行以及上下5行
grep -B 5  file  显示test及前5行
grep -A 5  file  显示test及后5行
```







