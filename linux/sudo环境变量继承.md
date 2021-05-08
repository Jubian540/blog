# sudo环境变量继承

sudo中默认配置会重置环境变量，所以使用sudo的时候需会出现很多操蛋问题，比如找不到命令啥的。

## 解决方法

1.编辑/etc/sudoers文件把Defaults env_reset改成Defaults !env_reset

2.编辑.bashrc,最后添加`alias sudo='sudo env PATH=$PATH'`

3.`source ~/.bashrc`