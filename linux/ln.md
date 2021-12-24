# 创建硬链接和软链接

软链接，全称软链接文件，symbolic link。 这类文件类似于Windows里的快捷方式，这个软链接的内容，其实时另一个文件的路径和名称。



硬链接，全称硬链接文件，hard link。这类文件比较特殊，会拥有自己的inode节点和名称，其inode会指向文件内容所在的数据块，与此同时，该文件内容所在的数据块引用计数会加1。当此数据块的引用计数大于等于2时，表示有多个文件同时指向这个数据块。



建立硬链接

```
echo "hello world" >> source.txt
ln source.txt hard_source.txt //创建硬链接

ls -il source.txt hard_source.txt         //查看两个文件的inode，发现完全相同  -i 表示列出每个文件的inode节点的id
5990541 -rw-r--r-- 2 root root 12 Nov 24 06:31 hard_source.txt
5990541 -rw-r--r-- 2 root root 12 Nov 24 06:31 source.txt
```

不可建立硬链接目录



建立软链接

```
ln -s source.txt s_source.txt      //建立软链接文件

[root@localhost /]# ls -il source.txt  s_source.txt    //查看两个文件的inode 5990541\5990547
5990541 -rw-r--r-- 2 root root 12 Nov 24 06:31 source.txt
5990547 lrwxrwxrwx 1 root root 10 Nov 24 06:49 s_source.txt -> source.txt

ln -s var lvar                      //建立软链接文件
lrwxrwxrwx    1 root    root             3 Nov 24 06:57 lvar -> var

```

如果这个时候，我们删除了 source.txt 文件，则软链接 softsource.txt 就会变成红色字体。这表示警告，说明这是一个有问题的文件，无法找到它所标识的目标文件 source.txt 啦。







