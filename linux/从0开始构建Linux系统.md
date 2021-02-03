# 模拟标准PC 从源代码构建Linux系统

本文介绍怎样从0使用源代码构建一个可以运行的Linux系统，并使用QEMU模拟一台标准x86_64 PC运行构建完成的系统镜像。

## 标准PC的启动流程

从打开电源到开始操作，PC的启动是一个非常复杂的过程。但是简而言之可以简化成以下几个步骤。

1.进入BIOS程序完成自检

2.从启动介质中读取系统镜像文件

3.运行启动文件进入系统

其中1，2两步是固化在标准PC主板Flash上的程序，本文讲的是构建一个可以运行的系统镜像文件。

## 模拟标准PC

本文使用QEMU模拟标准PC，当然你也可以使用物理机器来验证，使用模拟器的好处在于不用去拔插和折腾硬件节省这部分的时间来重点学习系统的构建。

摘自QEMU官网介绍：`QEMU is a generic and open source machine emulator and virtualizer.(QEMU是一个开源的机器和仿真器)`，你可以从QEMU官网获取它的源代码和安装包，以下是摘自QEMU官网的安装方法：

### Linux系统

QEMU is packaged by most Linux distributions:

- **Arch:** `pacman -S qemu`
- **Debian/Ubuntu:** `apt-get install qemu`
- **Fedora:** `dnf install @virtualization`
- **Gentoo:** `emerge --ask app-emulation/qemu`
- **RHEL/CentOS:** `yum install qemu-kvm`
- **SUSE:** `zypper install qemu`

### Mac系统

QEMU can be installed from **Homebrew**:

```
brew install qemu
```

QEMU can be installed from **MacPorts**:

```
sudo port install qemu
```

QEMU requires Mac OS X 10.5 or later, but it is recommended to use Mac OS X 10.7 or later.

### Windows系统

直接下载安装包 [点击下载32位安装包](https://qemu.weilnetz.de/w32/) ， [点击下载64位安装包](https://qemu.weilnetz.de/w64/) 。

### 启动模拟器

安装好QEMU后使用以下命令可以模拟一台标准PC:

```shell
qemu-system-x86_64 -nographic
```

效果和没有插上硬盘或者硬盘没有安装系统的物理机器开机效果类似，下面是笔者机器上的实际输出log:

```shell
SeaBIOS (version rel-1.14.0-0-g155821a1990b-prebuilt.qemu.org)


iPXE (http://ipxe.org) 00:03.0 CA00 PCI2.10 PnP PMM+07F8F290+07EEF290 CA00
                                                                               


Booting from Hard Disk...
Boot failed: could not read the boot disk

Booting from Floppy...
Boot failed: could not read the boot disk

Booting from DVD/CD...
Boot failed: Could not read from CDROM (code 0003)
Booting from ROM...
iPXE (PCI 00:03.0) starting execution...ok
iPXE initialising devices...ok



iPXE 1.20.1+ (g4bd0) -- Open Source Network Boot Firmware -- http://ipxe.org
Features: DNS HTTP iSCSI TFTP AoE ELF MBOOT PXE bzImage Menu PXEXT

net0: 52:54:00:12:34:56 using 82540em on 0000:00:03.0 (open)
  [Link:up, TX:0 TXE:0 RX:0 RXE:0]
```

可以

看见在BIOS完成自检后检查各个启动介质，Hard Disk、Floppy、DVD/CD、ROM找不到可以启动的镜像后就停止下面的启动流程了。

## 获取并编译linux内核源代码

这个章节的一系列操作均在笔者的x86_64 Ubuntu20.04 focal LTS机器上运行，当然你也可以在其它发行版上完成以下操作，但是部分软件包和命令会有所不同。

### 安装编译所需的依赖软件包

```shell
sudo apt-get update
sudo apt-get install -y git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison
```

### 获取linux内核源代码

这里使用最新的mainline内核。

```shell
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
```

### 配置并编译源代码

在内核源代码目录下运行:

```shell
make defconfig
make -j8
```

编译时间长短与编译代码的机器配置有关系，笔者使用的I510500 16G内存机器实际编译时间在3分钟左右。

### 模拟启动linux内核

```shell
qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage
```

可以看到内核被成功启动，但是卡在了最后的:

```shell
Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0, 0)
```

这个错误的意思是内核无法挂载根文件系统，没法正常继续启动。

## 制作根文件系统

根文件系统是一种文件系统，该文件系统不仅具有普通文件系统的存储数据文件的功能，但是相对于普通的文件系统，它的特殊之处在于，它是内核启动时所挂载（mount）的第一个文件系统，内核代码的映像文件保存在根文件系统中，系统引导启动程序会在根文件系统挂载之后从中把一些初始化脚本（如rcS,inittab）和服务加载到内存中去运行。

### 制作ubuntu-base镜像

这里仅介绍最小ubuntu-base镜像的制作方法。

#### 获取ubuntu-base文件系统

```shell
mkdir rootfs
cd rootfs
wget http://cdimage.ubuntu.com/ubuntu-base/focal/daily/current/focal-base-amd64.tar.gz
tar xvzf focal-base-amd64.tar.gz
rm xvzf focal-base-amd64.tar.gz
```

#### 制作文件系统镜像

```shell
sudo dd if=/dev/zero of=rootfs.img bs=1M count=300
sudo mkfs.ext4 rootfs.img
mkdir tmp
sudo mount rootfs.img tmp
sudo cp -rf rootfs/* tmp/
sudo umount tmp
sudo e2fsck -p -f rootfs.img
sudo resize2fs -M rootfs.img
```

## 启动系统

```shell
sudo qemu-system-x86_64 -kernel linux/arch/x86_64/boot/bzImage -hda rootfs.img -append 'root=/dev/sda'
```

![模拟的系统](https://gitee.com/dresky/images/raw/master/images/Screenshot%20from%202021-02-03%2019-19-27.png)

一个简化版本的ubuntu系统就启动了。