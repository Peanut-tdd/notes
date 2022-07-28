# php文件not found 问题

是因为开启了linux系统的selinux设置

## 查看selinux

```
sestatus
enabled状态表示已开启
```







## 永久关闭selinux

```
cat /etc/selinux/config







# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

将SELINUX设置为disabled，并重启服务器。

