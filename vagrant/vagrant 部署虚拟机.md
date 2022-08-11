# vagrant 部署虚拟机

## 1、下载软件

###  安装vagrant

在[vagrant](https://www.vagrantup.com/downloads)官网下载最新的版本，根据自己的操作系统选择对应的版本下载即可。

注意，Vagrant 是没有图形界面的，所以安装完成后也没有桌面快捷方式。具体使用方法，接下来会详细说明。

Vagrant 的安装程序会自动把安装路径加入到 PATH 环境变量，所以，这时候可以通过命令行执行 `vagrant version` 检查是否安装成功：

```
$ vagrant version
Installed Version: 2.2.18
Latest Version: 2.2.19
```



### 安装virtualbox

进入 VirtualBox 的[主页](https://www.virtualbox.org/wiki/Downloads)，点击大大的下载按钮，即可进入下载页面。

VirtualBox 是一个跨平台的虚拟化工具，支持多个操作系统，根据自己的情况选择对应的版本下载即可。

![image-20220810103633885](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20220810103633885.png)







## 2、配置虚拟机

创建虚拟机会占用较多的磁盘空间，在 Windows 系统下默认的虚机创建位置是在 C 盘，所以最好配置到其它地方。

**配置 VirtualBox**

启动 VirtualBox 后，通过菜单 `管理` -> `全局设定`，或者按下快捷键 `Ctrl + g`，在全局设定对话框中，修改 `默认虚拟电脑位置`，指定一个容量较大的磁盘。

![image-20220810110217987](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20220810110217987.png)



**配置 Vagrant**

通过 Vagrant 创建虚机需要先导入镜像文件，也就是 `box`，它们默认存储的位置在用户目录下的 `.vagrant.d` 目录下，对于 Windows 系统来说，就是 `C:\Users\用户名\.vagrant.d`。

如果后续可能会用到较多镜像，或者你的 C 盘空间比较紧缺，可以通过设置环境变量 `VAGRANT_HOME` 来设置该目录。

在 Windows 系统中，可以这样操作：新建系统环境变量，环境变量名为 `VAGRANT_HOME`，变量值为 `E:\VirtualBox\.vagrant.d`

> **注意**，最后这个 `.vagrant.d` 目录名称不是必须的，但是建议保持一致，这样一眼看上去就能知道这个目录是做什么用处的了

![image-20220810110247074](https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20220810110247074.png)



## 下载虚机镜像box

使用 Vagrant 创建虚机时，需要指定一个镜像，也就是 `box`。开始这个 box 不存在，所以 Vagrant 会先从网上下载，然后缓存在本地目录中。

下面我给出最常用的 CentOS 操作系统镜像的下载地址：

**CentOS**

CentOS 的镜像下载网站是： [http://cloud.centos.org/centos/](https://link.zhihu.com/?target=http%3A//cloud.centos.org/centos/)

在其中选择自己想要下载的版本，列表中有一个 `vagrant` 目录，里面是专门为 vagrant 构建的镜像。选择其中的 `.box` 后缀的文件下载即可。这里可以使用下载工具，以较快的速度下载下来。

这里我们选择下载的是 [CentOS 7 的最新版本](http://cloud.centos.org/centos/7/vagrant/x86_64/images/)







## 添加 box

接下来我们需要将下载后的 `.box` 文件添加到 vagrant 中。

Vagrant 没有 GUI，只能从命令行访问，先启动一个命令行，然后执行:

```text
$ vagrant box list
There are no installed boxes! Use `vagrant box add` to add some.
```

提示现在还没有 box。如果这是第一次运行，此时 `VAGRANT_HOME` 目录下会自动生成若干的文件和文件夹，其中有一个 `boxes` 文件夹，这就是要存放 box 文件的地方。

执行 `vagrant box add` 命令添加 box:

```text
$ vagrant box add e:\Downloads\CentOS-7.box --name centos-7
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'centos-7' (v0) for provider:
    box: Unpacking necessary files from: file:///e:/Downloads/CentOS-7.box
    box:
==> box: Successfully added box 'centos-7' (v0) for 'virtualbox'!
```

命令后面跟着的是下载的文件路径，并且通过 `--name centos-7` 为这个 box 指定一个名字。

后面创建虚机都需要指定这个名字，所以尽量把名字取得简短一点，同时也要能标识出这个镜像的信息（我们后面会定制自己的基础镜像，所以这里可以简单点）。

再次查询，可以看到有了一个 box：

```text
$ vagrant box list
centos-7 (virtualbox, 0)
```



## 3、Vagrant 基本操作

### 新建虚机

创建一个目录，先执行 `vagrant init`：

```text
$ mkdir vagrant
$ cd vagrant
$ vagrant init centos-7
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

其中的 `centos-7` 就是我们要使用的 `box` 名字。

这个命令只是为我们生成一个 `Vagrantfile`，所以，这里的名字没指定或者写错了都没关系，后面会介绍如何编辑这个 `Vagrantfile` 来修改。

### 启动虚机

我们等会再来细看这个文件，现在直接按照提示执行 `vagrant up`：

```text
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'centos-7'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: demo_default_1588406874156_65036
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
```

正常的情况下，不到一分钟应该就能启动成功了。

> 这里我遇到点问题，2222 端口转发出现未知错误，造成 vagrant 的启动超时。检查了这个端口并没被占用，同时更改大一点的端口可以转发成功，所以应该是系统哪里有点问题。在后面我介绍了如何处理，如果你也遇到和我一样的情况，可先跳到下面查看。

注意到这里包含的信息：

- 虚机名称：`demo_default_1588406874156_65036`（想改？最后有提）
- 网卡：`Adapter 1: nat`，第一块网卡，NAT 模式，这是固定的
- 端口转发：`22 (guest) => 2222 (host) (adapter 1)`，把虚机的 22 端口，映射到宿主机的 2222 端口上，这样就可以通过 `127.0.0.1:2222` 访问虚拟机了
- SSH 用户名：`vagrant`，这里使用 `private key` 登录

> 密码也是 `vagrant`，但是密码方式仅供直接登录，是不能通过 SSH 登录的。





### 查看虚机状态

执行下面的命令可以查看虚机的状态：

```text
vagrant status

Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

该命令还提示了如何操作虚机，我们继续一一介绍

### 停止虚机

执行下面的命令可以关闭虚机：

```text
vagrant halt
```

直接在 VirtualBox 上关闭虚机，或者直接在虚机内部执行 `poweroff` 命令也都是可以的。

### 暂停虚机

执行下面的命令可以暂停虚机：

```text
vagrant suspend
```

### 恢复虚机

执行下面的命令把暂停状态的虚机恢复运行：

```text
vagrant resume
```

**注意：** 不管虚机是关闭还是暂停状态，甚至是 error 状态，都可以执行 `vagrant up` 来让虚机恢复运行。

### 重载虚机

执行下面的命令会重启虚机，并且重新加载 `Vagrantfile` 中的配置信息：

```text
vagrant reload
```

### 删除虚机

最后，执行下面的命令可以彻底删除虚机，包括整个虚机文件：

```text
vagrant destroy
```

**注意：** 在当前这个小例子中，上面所有的 `vagrant` 命令都需要在 `Vagrantfile` 所在的目录下执行。





## 4、初识 Vagrantfile

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos-7"
  Encoding.default_external = 'UTF-8'

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
   config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
   config.vm.synced_folder "../../phpStudy/PHPTutorial/WWW", "/home/www"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = 2048
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end

```

这是一个 Ruby 语法的文件，因为 Vagrant 就是用 Ruby 编写的。如果编辑器没有语法高亮可以手动设置文件类型为 Ruby。

这个缺省文件内容几乎都是注释，提示有哪些配置项可以修改，我们不需要去学 Ruby 编程也可以照葫芦画瓢的完成基本的配置。

> 当然，如果会 Ruby 编程的可以在此实现更高级的作用，但是绝大多数人用不着。

刨除注释，这个文件的实际生效内容实际只有 3 行:

```text
Vagrant.configure("2") do |config|
  config.vm.box = "centos-7"
end
```

首尾两行组成一个代码块结构，不要去动它，除非你知道自己在干什么。我们平常只需要编辑这其中的配置项。

这里的 `config.vm.box` 对应的就是虚机的镜像，也就是 box 文件，这是唯一必填的配置项。



## 自定义配置 Vagrantfile

下面我将针对这份默认的 `Vagrantfile` 内容，逐个讲解其中的配置含义和如何根据实际情况修改。

### 配置私有网络

下面这段配置用来配置私有网络，实际上对应的是 VirtualBox 的主机网络，也就是 HostOnly 网络。

```text
  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
```

取消注释最下面一行，就可以为虚机设置指定的私有网络地址：

```text
  config.vm.network "private_network", ip: "192.168.33.10"
```

### 配置同步文件夹

下面的配置项用来配置同步文件夹:

```text
  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
   config.vm.synced_folder "../../phpStudy/PHPTutorial/WWW", "/home/www"
```

> **说明**:  Vagrant 提供了多种同步方式，在使用 VirtualBox 的时候，缺省同步类型vboxsf 挂载文件系统，它需要在虚拟机内部安装客户机增强包，也就是 `VirtualBox Guest Additions`。





## 5、定制带客户机增强的 box

### 下载 Guest Addition

VirtualBox 的[下载页面](https://link.zhihu.com/?target=https%3A//www.virtualbox.org/wiki/Downloads)并没有直接给出 Guest Addtion 的下载链接，我们先在 VirtualBox 的任一下载链接上右键，复制链接地址，例如得到 `https://download.virtualbox.org/virtualbox/6.1.36/VirtualBox-6.1.36-152435-Win.exe`，去掉最后的文件名，把其中的路径 `https://download.virtualbox.org/virtualbox/6.1.36` 在浏览器中打开，就能看到所有可下载的版本，在其中找到 `VBoxGuestAdditions_6.1.6.iso` 直接下载。



### 安装 Guest Addition

重新使用 Vagrant 从原始的镜像启动一个干净的虚机。

`VBoxGuestAdditions_6.1.6.iso` 需要以光盘的形式挂载到虚机上，但是默认启动的这个虚机是没有光驱的。添加虚拟光驱需要先将虚机关闭。

然后在 VirtualBox 界面上操作，打开 `设置`，选择 `存储`，点击 `添加虚拟光驱`，点击 `控制器： IDE`，选择 `VBoxGuestAdditions_6.1.6.iso`，点击 `OK`

直接在 VirtualBox 上启动虚机，如果你在虚机菜单上选择 `设备` - `安装增强功能`，大概率是会遇到下面这样的错误，别管它，我们手动来安装。

使用 `vagrant/vagrant` 登录到虚机内，切换到 `root` 用户，查看虚拟光盘是否已经挂载上来了：

```text
$ sudo -i
# lsblk
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda      8:0    0  40G  0 disk
└─sda1   8:1    0  40G  0 part /
sr0     11:0    1  57M  0 rom
```

下面的 `sr0` 就是光盘设备，要把它挂载到文件系统中

```text
# mount /dev/sr0 /mnt/
mount: /dev/sr0 is write-protected, mounting read-only
# cd /mnt/
# ls
AUTORUN.INF  cert  OS2           TRANS.TBL                VBoxDarwinAdditionsUninstall.tool  VBoxSolarisAdditions.pkg        VBoxWindowsAdditions.exe
autorun.sh   NT3x  runasroot.sh  VBoxDarwinAdditions.pkg  VBoxLinuxAdditions.run             VBoxWindowsAdditions-amd64.exe  VBoxWindowsAdditions-x86.exe
```

在 Linux 系统中要运行 `VBoxLinuxAdditions.run`，这时候直接运行仍然会报错：

<img src="https://raw.githubusercontent.com/Peanut-tdd/Picture/main/image-20220810114234703.png" alt="image-20220810114234703"  />



大致意思是要它需要内核的头文件来构建。

头文件通过安装 `kernel-devel` 就可以了，但是直接安装的版本是最新版本的，可能会当前的内核版本不一致，仍然是没用的。例如：

```text
[root@localhost mnt]# uname -r
3.10.0-1062.12.1.el7.x86_64
[root@localhost mnt]# yum info kernel-devel
Installed Packages
Name        : kernel-devel
Arch        : x86_64
Version     : 3.10.0
Release     : 1127.el7
Size        : 38 M
Repo        : installed
From repo   : base
```

这里 `kernel-devel` 的 `Release` 版本号和系统内核版本不一致，所以仍然不行。

需要先更新一把，然后再安装（也可以先安装再更新）：

```text
# yum update -y
# yum install -y gcc kernel-devel
```

升级内核后需要重启虚机，然后再次尝试安装 Additions：

```text
[root@localhost mnt]# ./VBoxLinuxAdditions.run
Verifying archive integrity... All good.
Uncompressing VirtualBox 6.1.6 Guest Additions for Linux........
VirtualBox Guest Additions installer
Removing installed version 6.1.6 of VirtualBox Guest Additions...
Copying additional installer modules ...
Installing additional modules ...
VirtualBox Guest Additions: Starting.
VirtualBox Guest Additions: Building the VirtualBox Guest Additions kernel
modules.  This may take a while.
VirtualBox Guest Additions: To build modules for other installed kernels, run
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup <version>
VirtualBox Guest Additions: or
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup all
VirtualBox Guest Additions: Building the modules for kernel
3.10.0-1127.el7.x86_64.
VirtualBox Guest Additions: Running kernel modules will not be replaced until
the system is restarted

# 检查模块是否已经加载了
[root@localhost mnt]# lsmod |grep vbox
vboxvideo              35867  1
ttm                    96673  1 vboxvideo
drm_kms_helper        186531  1 vboxvideo
drm                   456166  4 ttm,drm_kms_helper,vboxvideo
vboxguest             349038  1
[root@localhost mnt]#
```

能够看到 `vboxguest` 就代表安装成功了。











