####  gnome-terminal方法

###### 间接启动法：

新建终端时，终端会自动执行`~/.bashrc`，应用该方法可实现开机自启动

```bash
# 将命令写入~/.bashrc
source ~/.bashrc
```

启动ubuntu应用程序首选项管理：

```bash
gnome-session-properties
```

![在这里插入图片描述](ubuntu开机启动脚本.assets/20200805152811451.png)
命令(M) 写入：`gnome-terminal`，→添加，重启即可生效。

###### 直接启动法：

![在这里插入图片描述](ubuntu开机启动脚本.assets/20200805152811451.png)
命令(M) 写入：

```bash
gnome-terminal -x bash -c "/home/User/Desktop/test.sh"
```

→添加，重启即可生效。

> 使用工控机时，可设置为用户自动登录