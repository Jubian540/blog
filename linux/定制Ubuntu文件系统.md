# 定制Ubuntu文件系统

上一篇文章介绍了如何构建一个Linux系统，里面用到了一个叫ubuntu-base的包，这个包就是个最简化的ubuntu文件系统，如果使用qemu模拟器启动这个系统会发现里面除了基础文件读写功能外啥玩意也没有，甚至没有网络？这能忍？这篇文章就介绍如何从ubuntu-base包里构建出一个功能更加完善的ubuntu系统。

## 获取Ubuntu-base

Ubuntu-base可以从Ubuntu-cdimage网站获取，网址为：http://cdimage.ubuntu.com/，如果你身处中国大陆，推荐从中科大镜像中下载该文件以加快下载速度，网址为：https://mirrors.ustc.edu.cn/ubuntu-cdimage/，选择你需要的版本和构架，笔者使用从中科大镜像站中下载amd_64 focal版本作为演示：

```shell
mkdir rootfs
cd rootfs
wget https://mirrors.ustc.edu.cn/ubuntu-cdimage/ubuntu-base/releases/focal/release/ubuntu-base-20.04.1-base-amd64.tar.gz
tar xvzf ubuntu-base-20.04.1-base-amd64.tar.gz
rm ubuntu-base-20.04.1-base-amd64.tar.gz
```

执行上面的命令之后可以得到一个rootfs目录，里面保存着Ubuntu-base的所有文件。

## 修改Ubuntu-base

```shell
cp -b /etc/resolv.conf rootfs/etc/resolv.conf #拷贝本地dns配置到rootfs，让网络可用
cp /usr/bin/qemu-*-static rootfs/usr/bin/ #拷贝模拟器到rootfs中，为下一步的模拟做准备
cp  -r /etc/skel rootfs/etc/ #拷贝用户配置
chmod 777 rootfs/tmp
```

## 模拟启动Ubuntu-base

这里的模拟启动和上篇文章介绍的不一样，不需要内核镜像文件，通过模拟环境模拟目标构架来安装或者修改系统中的信息并保存到rootfs里面。

1.挂载虚拟文件系统

linux系统里面有很多不占用硬盘空间的目录，例如/proc /dev /sys等这些目录是系统实时的运行信息，需要先在rootfs上挂载虚拟的文件系统才能正常模拟：

```shell
mount -v --bind /dev rootfs/dev
mount -vt devpts devpts rootfs/dev/pts -o gid=5,mode=620
mount -vt proc proc rootfs/proc
mount -vt sysfs sysfs rootfs/sys
mount -vt tmpfs tmpfs rootfs/run
```

## 启动模拟环境

完成上面的步骤后直接在根目录运行`sudo chroot .`就能启动模拟，在模拟环境中，刚刚修改过的rootfs就是根目录，物理机的实时环境就是模拟环境的实时环境，这事可以像普通的机器上执行命令去修改这个rootfs了，比如：

```shell
apt update && apt upgrade #更新系统
passwd root #修改root用户密码
```

完成修改后在模拟环境中运行`exit`命令就能退出模拟环境回到物理机器的终端中，退出后一定要记得**卸载虚拟文件系统**，否则在备份rootfs时会造成拷贝失败，物理机的实时运行环境也会受到影响，执行下面的命令就能卸载模拟环境：

```shell
umount -vt devpts rootfs/dev/pts
umount -vt proc rootfs/proc 
umount -vt sysfs rootfs/sys
umount -vt tmpfs rootfs/run
umount -v rootfs/dev
```

再清理掉一些垃圾文件：

```shell
rm -rf rootfs/usr/bin/qemu-*-static
rm -rf rootfs/etc/apt/sources.list.d/*.key
rm -rf rootfs/var/lib/apt/lists/*
```

## 备份制作文件系统镜像

```shell
sudo dd if=/dev/zero of=rootfs.img bs=1M count=300 #这里注意要以做好的rootfs目录大小为准定义大小bs=1M的意思是一步1M大小count=300的意思是300步，也就是做一个300M的镜像，修改这两个值就可以修改制作的镜像大小
sudo mkfs.ext4 rootfs.img
mkdir tmp
sudo mount rootfs.img tmp
sudo cp -rf rootfs/* tmp/
sudo umount tmp
sudo e2fsck -p -f rootfs.img
sudo resize2fs -M rootfs.img
```

## 快速构建

我开源了一个ubuntu文件系统的快速构建脚本，github地址：https://github.com/Jubian540/ubuntu-rootfs-build.git，项目中的README.md中有具体使用说明。