# mysql保证持久性一致性原子性



innodb保证持久性

```
redolog（重做日志）
```



保证数据的一致性

```
binlog(归档日志)
```



保证数据的原子性

```
undolog(回滚日志)
```





redolog两阶段提交

目的：保证redolog和binlog日志之间的逻辑一致；

我们以update语句为例，假设id=2的记录，字段c值是0，把字段c值更新成1，SQL语句为update T set c=1 where id=2。假设执行过程中写完redo log日志后，binlog日志写期间发生了异常，会出现什么情况呢？

由于binlog没写完就异常，这时候binlog里面没有对应的修改记录。因此，之后用binlog日志恢复数据时，就会少这一次更新，恢复出来的这一行c值是0，而原库因为redo log日志恢复，这一行c值是1，最终数据不一致。



为了解决两份日志之间的逻辑一致问题，InnoDB存储引擎使用两阶段提交方案。

原理很简单，**将redo log的写入拆成了两个步骤prepare和commit，这就是两阶段提交**。

场景一：

**使用两阶段提交后，写入binlog时发生异常也不会有影响，因为MySQL根据redo log日志恢复数据时，发现redo log还处于prepare阶段，并且没有对应binlog日志，就会回滚该事务**





场景二：

再看一个场景，redo log设置commit阶段发生异常，那会不会回滚事务呢？

**并不会回滚事务，虽然redo log是处于prepare阶段，但是能通过事务id找到对应的binlog日志，所以MySQL认为是完整的，就会提交事务恢复数据。**





