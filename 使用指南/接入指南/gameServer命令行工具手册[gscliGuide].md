## 命令行工具简介
matchvs命令行工具是由Matchvs开发平台提供的命令行执行工具，可以在Windows和Linux系统中使用。
该命令行工具提供了以下功能：创建gameServer、启动服务、重启服务、查看CPS的运行日志。



## 安装说明：
### Windows环境
- 在D盘创建目录 matchvs （这里创建的目录仅仅是个示例，用户可以使用任意的目录名，可以在任何路径下）  

- 将下载的可执行文件 matchvs.exe 放到 `D:\matchv` 目录下
- 修改环境变量：PATH，将 `D:\matchvs` 追加在变量值后面，与前面的内容用 ` ;`隔开    

![image](http://imgs.matchvs.com/static/mvs1.png)

- 安装完成后，打开DOS窗口，执行 `matchvs`；结果如下，表示安装成功。    

![image](http://imgs.matchvs.com/static/mvs02.png)

### Linux环境  
- 将下载的可执行文件matchvs放到随意的目录，比如 `/home/test/mvs`
- 添加可执行权限：`chmod +x matchvs`
- 修改环境变量：
  执行命令：`export PATH=$PATH:/home/test/mvs` （这种每次打开终端都要执行）
  为了在启动终端时都有效，在帐号根目录的文件 .bash_profile 中添加，然后执行 `source .bash_profile`    

![image](http://imgs.matchvs.com/static/mvs3.png)

- 添加完成后，执行`matchvs help`，如下图效果表示安装成功    

![image](http://imgs.matchvs.com/static/gs_linuxhelp.png)



## 开发者帐号登录
`matchvs login`  

使用在Matchvs官网注册的账号和密码进行登录，如果还没有账号，请前往[Matchvs官网](http://www.matchvs.com/vsRegister)进行注册。

根据提示输入邮箱（手机号）和密码进行登录，成功登录后如下图：
![image](http://imgs.matchvs.com/static/gs_login.png)



## 添加SSH Key

`matchvs keys  C:\Users\Administrator\.ssh\id_rsa.pub `

上述`C:\Users\Administrator\.ssh\id_rsa.pub`为SSH Key的文件路径，SSH Key 的生成方法参考[SSH Key手册](/service?page=SSH Key手册)。

添加成功后如下图：

![image](http://imgs.matchvs.com/static/gs_addkey.png)

## 查看gameServer列表
`matchvs list`  
查看帐号下的gameServer列表  
![image](http://imgs.matchvs.com/static/gs_list.png)



## 查看gameServer详情

`matchvs info <GS_key>` 

查看指定gameServer的详细信息

![image](http://imgs.matchvs.com/static/gs_info.png)



## 创建gameServer

`matchvs create <GS_name> <gameID>`

指定游戏ID和gameServer名称，创建一个gameServer

![image](http://imgs.matchvs.com/static/gs_create.png)

## 发布gameServer

`matchvs publish <GS_key>`

发布gameServer到镜像仓库，`GS_key`可通过`matchvs list` 查看。

**注意：** 操作前需要将代码上传至Matchvs指定的git仓库。

![image](http://imgs.matchvs.com/static/gs_publishCLI.png)



## 启停gameServer

`matchvs run start <GS_key>`

启动gameServer服务

**注意 ：** 官网控制台上的启动操作已经包含了发布过程。但命令行工具在gameServer第一次启动时，请务必确认有执行`matchvs publish <GS_key>`。

![image](http://imgs.matchvs.com/static/gs_start.png)



`matchvs run stop <GS_key>`

停止gameServer服务

![image](http://imgs.matchvs.com/static/gs_stop.png)



`matchvs run restart <GS_key>`

重启gameServer服务

![image](http://imgs.matchvs.com/static/gs_restartCLI.png)



## 查看日志

`matchvs logs <GS_key>`

查询gameServer最新200行日志。

 ![image](http://imgs.matchvs.com/static/gs_logs.png)

## 开启调试模式

`matchvs debug <GS_key>`

Matchvs 提供了gameServer的本地调试功能，通过以上命令可以开启调试模式。

![image](http://imgs.matchvs.com/static/gs_debug.png)