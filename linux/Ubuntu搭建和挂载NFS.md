# Ubuntu搭建和挂载NFS

## 服务器上操作

安装nfs服务端

```shell
$ sudo apt install nfs-kernel-server
```

创建nfs共享目录

```shell
$ sudo mkdir /nfs
```

配置nfs服务

使用命令`sudo vim /etc/exports `编辑配置文件，此处给个参考：

```shell
/nfs *(rw,sync,no_subtree_check,no_root_squash)
```

重启nfs服务

```shell
$ sudo service nfs-kernel-server restart
```

## 客户端上操作

安装nfs客户端

```shell
$ sudo apt install nfs-common
```

查看NFS服务器上的共享目录

```shell
$ sudo showmount -sudo showmount -ee 192.168.3.167 #192.168.3.167为服务器ip
```

挂载NFS共享目录

```shell
$ mkdir nfs
$ sudo mount -t nfs 192.168.3.167:/nfs ~/nfs
```

