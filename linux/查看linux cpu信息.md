# linux cpu信息

1. 查看cpu型号

   ```
   cat /proc/cpuinfo
   
   cat /proc/cpuinfo |grep name |sort|uniq
   ```

![image-20211012163706432](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211012163706432.png)

2. 查看物理cpu数目

   ```
   cat /proc/cpuinfo|grep "physical id"
   ```

   ![image-20211012163810442](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211012163810442.png)

   所有physical id都是0，可知有1个物理CPU;也用管道排序去重后直接输出物理cpu的个数； 

   ```
   cat /proc/cpuinfo|grep "physical id"|sort|uniq|wc -l
   ```

   ![image-20211012164012585](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211012164012585.png)

   

3. 查看每个物理cpu的core（即核数）

   ```
   cat /proc/cpuinfo|grep "cpu cores"|sort|uniq
   ```

   

   ![image-20211012164201356](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211012164201356.png)

4. 查看逻辑cpu数目

   ```
   cat /proc/cpuinfo|grep "processor"|sort|wc -l
   ```

   ![image-20211012164318467](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211012164318467.png)





5. 如果不想自己算，也可以直接lscpu

   ```
   lscpu
   ```

   ![image-20211012164522373](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20211012164522373.png)
