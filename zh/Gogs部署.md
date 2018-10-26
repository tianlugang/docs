## Gogs 部署

1. gogs默认使用git用户运行，因此需要在服务器上创建git用户，命令如下:
    ```shell
        sudo adduser git
    ```
    此时，会在服务器home目录下生成 git 文件夹，分配Git到sudoers，通过 vim 编辑 /etc/sudoers，在 Allow root to run any commands anywhere 下面添加 ：
    ```shell
        git     ALL=(root)      ALL,!/usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root #禁止git修改root密码
    ```
    还需要让git对/home/git有可读可写可执行的权限（700），命令如下：
    ```shell
        chmod -R 700 /home/git # 其语法格式为：chmod [who] [opt] [mode] 文件/目录名
	    chown -R git:git /home/git/* #其语法格式为：chmod [who] [opt] [mode] 文件/目录名
    ```
    <br/>
2. 下载安装 go 、 gogs 、 sqllite3 ,此过程简单 
    <br/>

3. 运行gogs服务
	切换到git用户
    ```shell
        su - git
    ```
	到gogs目录中执行 ./gogs web , 首次运行安装比较简单，在浏览器里运行，因此不需要说明，首次运行结束后，会在gogs运行下生成custom/conf/app.ini,具体配置请参考[点我](https://gogs.io/docs/advanced/configuration_cheat_sheet)
    <br>

4. 将gogs配置为Linux服务，并开机启动
	- 在gogs项目下的scripts/文件夹里有对应系统的服务脚本，此处我们用到的是init/centos/gogs,将其复制到/etc/init.d/下面
        ```shell
            cp /home/git/gogs/scripts/init/centos/gogs /etc/init.d/
        ```
	- 切换到root用户的/etc/init.d/目录，赋予gogs脚本可执行权限：
        ```shell
            chmod +x gogs
        ```
	- 使用服务管理命令管理服务：
	    ```shell
            service gogs start/stop/restart/status
        ```
	- 添加开机启动：
	    ```shell
            chkconfig  —add gogs
        ```
    <br>
  5. 以上步骤结束后，gogs可以完美运行了，gogs项目管理与git相同，此处我启用的是非注册模式，普通用户只能通过管理员账号来创建，而且我禁用了ssh模式。事情并没有因此而结束，我们需要在服务器防火墙里开通gogs服务的端口，此处选择不开启，使用NGINX反向代理出去，关于Nginx反向代理部署，请自行百度，事情到此告一段落，gogs可以完整使用，后期升级维护等将在后期续写

<br>
## 相关资源
1. [gogs配置](https://gogs.io/docs/advanced/configuration_cheat_sheet)