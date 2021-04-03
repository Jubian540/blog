# Ubuntu英伟达显卡花屏/驱动安装

由于Ubuntu默认用nouveau开源显卡驱动，如果装了英伟达显卡开机就会有花屏问题，这篇文章记录本人解决开机花屏和安装英伟达驱动的方法。

本人使用的系统版本为ubuntu 20.04 LTS focal。

## 解决开机花屏

在安装系统时，设置完开机启动后正常进入grub引导界面，开始引导ubuntu live安装镜像时出现花屏，然和按键都没反应。解决方法如下：

当引导U盘至grub界面时按下键盘上的**e**键。编辑，在带Linux为行首的行找到`quite splash`，将其后面的"–"去掉，改为nomodeset，即改成`quite splash nomodeset`，然后按下F10，即可重新安装。

**注意**：安装完成后开机从硬盘启动仍然会出现此问题，此时只需重新一遍上诉操作即可解决。

## 安装驱动

### 下载英伟达驱动

前往官网选择对应的驱动下载：https://www.nvidia.cn/Download/index.aspx?lang=cn

### 禁用nouveau驱动

安装nvidia显卡驱动首先需要禁用nouveau，不然会碰到冲突的问题，导致无法安装nvidia显卡驱动。

使用`sudo vim /etc/modprobe.d/blacklist.conf`编辑initramfs配置，并添加以下内容禁用nouveau驱动：

```shell
blacklist nouveau
options nouveau modeset=0
```

### 使用文本模式启动

```shell
$ sudo systemctl set-default multi-user.target
```

之后使用`sudo reboot`命令重启电脑。

### 安装驱动

开机后发现不会开启图形界面了使用快捷键**ctrl+alt+F1**进入终端，登录后找到刚刚下载好的驱动（这里驱动文件名为NVIDIA-Linux-x86_64-460.67.run）

```shell
$ chmod +x NVIDIA-Linux-x86_64-460.67.run #增加可执行权限
$ sudo ./NVIDIA-Linux-x86_64-460.67.run #安装驱动
```

安装完毕后：

```shell
$ sudo systemctl set-default graphical.target #开启图形界面
$ sudo reboot #重启
```



