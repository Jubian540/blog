# SSL error when connecting to the Jack server.

编译AOSP时出现Jack报错。

## 解决方法

编辑 **~/.jack-settings**,修改:

```shell
SERVER_PORT_SERVICE=8078
SERVER_PORT_ADMIN=8079
```

编辑 **~/.jack-server/config.properties**,修改:

```shell
jack.server.service.port=8078
jack.server.admin.port=8079
```

修改目标权限

```shell
$ chmod 600 ~/.jack-settings
$ chmod 700 ~/.jack-server
```

## 启动jack服务

```shell
$ jack-admin start-server
```

