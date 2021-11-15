# mysql事务

事务：是指mysql的一组数据操作，事务内的操作要么全部成功，要么全部失败。



## mysql事务的四大特性

```
原子性（Atomicity）
一致性（Consistency）
隔离性（Isolation）
持久性（Durability）
```

简称***ACID***,缺一不可。





## 事务隔离级别需要实际解决的问题

***脏读***

事务A读取了事务B修改但是还没有提交的数据，因事务B还未提交，可能会回滚。导致读到了并不存在的数据，这就是脏读。

----

***不可重复读***

事务A不同时刻读取到的数据不一致，可能是受到其他事务修改数据的影响，如事务B更新了数据。通常是对数据的更新（update）操作。

----

***幻读***

事务A修改了数据，但是还未提交，此时事务B插入了数据，并且在是事务A提交前先提交，然后事务A去查询时发现有些数据的修改好象没有成功，但其实是事务B插入了新的数据，让用户感觉出现了幻觉，这就是幻读，幻读是针对数据的插入（insert）操作。



## 事务隔离级别
事务隔离是为了解决上面提到的脏读、不可重复读、幻读。

MSYQL 四类事务隔离级别：

读未提交（READ UNCOMMITTED）

读提交（READ COMMITTED）

可重复读（REPEATABLE READ）

串行化（SERIALIZABLE）

下表展示了四种隔离级别对这三个问题的解决程度：

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
| -------- | ---- | ---------- | ---- |
| 读未提交 | 是   | 是         | 是   |
| 读提交   | 否   | 是         | 是   |
| 可重复读 | 否   | 否         | 是   |
| 串行化   | 否   | 否         | 否   |

从上往下，隔离强度逐渐增强，性能逐渐下降，***可重复读***是MySQL默认隔离级别。





**设置MYSQL事务隔离方法：**

```
set session transaction isolation level read uncommitted;

flush privileges;

show variables like '%tx_isolation%';
```

![image-20211115140827847](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115140827847.png)





**1）读未提交：出现脏读**

窗口A查询：

![image-20211115153629352](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115153629352.png)

窗口B修改数据：

![image-20211115153708592](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115153708592.png)



窗口A再次查询：

![image-20211115153743490](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115153743490.png)



窗口B修改回退：

![image-20211115153822951](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115153822951.png)

窗口A再次查询：

![image-20211115153850310](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115153850310.png)









**2）读提交：不可重复读**

窗口B事务更新数据：

![image-20211115155155665](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115155155665.png)



窗口A查询数据：

![image-20211115155231154](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115155231154.png)





窗口B提交事务更新：

![image-20211115155303856](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115155303856.png)

窗口A再次查询数据：

![image-20211115155337811](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115155337811.png)



因窗口B的事务修改，窗口A的两次查询的结果不一致。





**3）可重复读：幻读**

可重复读有效解决了不可重复读的。在一些场景中，一个事务只能读取到另一个已提交事务修改过的数据，但是第一次读过某条记录后，即使其它事务修改了该记录的值并且提交，之后该事务再读该条记录时，读到的仍是第一次读到的值，而不是每次都读到不同的数据。那么这种隔离级别就称之为可重复读。



窗口A查询:

![image-20211115165328899](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115165328899.png)



窗口B插入数据：

![image-20211115165423840](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115165423840.png)



窗口A插入数据失败并查询数据为空：

![image-20211115165502005](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211115165502005.png)





4）串行化 

如果一个事务先根据某些条件查询出一些记录，之后另一个事务又向表中插入了符合这些条件的记录，原先的事务再次按照该条件查询时，能把另一个事务插入的记录也读出来。那么这种隔离级别就称之为串行化。

SERIALIZABLE 是最高的事务隔离级别，主要通过强制事务排序来解决幻读问题。简单来说，就是在每个读取的数据行上加上**共享锁**实现，这样就避免了脏读、不可重复读和幻读等问题。但是该事务隔离级别执行效率低下，且性能开销也最大，所以一般情况下不推荐使用。









