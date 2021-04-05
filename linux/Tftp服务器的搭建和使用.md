# Tftp服务器的搭建和使用

本文章操作均在Ubuntu 18.04 LTS bionic上验证通过。

## 服务端操作

### 安装tftp服务器

```shell
$ sudo apt-get install tftpd-hpa
```

### 修改配置文件

使用命令`sudo vim /etc/default/tftpd-hpa `修改tftp服务配置文件，此处给出参考：

```shell
TFTP_USERNAME = "tftp"
TFTP_DIRCTORY = "/tftpboot"
TFTP_ADDRESS = "0.0.0.0:69"
TFTP_OPTIONS = "-l -c -s"
```

解释：

```shell
TFTP_USERNAME = "tftp"				#用户名
TFTP_DIRCTORY = "/tftpboot"			#tftp共享目录
TFTP_ADDRESS = "0.0.0.0:69"			#允许所有ip登入
TFTP_OPTIONS = "-l -c -s"			
```

### 创建tftp共享目录

```shell
$ sudo mkdir /tftp		#和上面的配置文件对应
$ sudo chmod 777 /tftp 	#修改权限
```

### 重启tftp服务

```shell
$ sudo service tftpd-hpa restart
```

## 客户端上操作

### 安装客户端

```shell
$ sudo apt-get install tftp-hpa
```

### 连接tftp服务器

```shell
$ tftp 192.168.22.10 #192.168.22.10为服务器ip
```

成功则会出现

```shell
tftp>
```

打入`help`可查看帮助