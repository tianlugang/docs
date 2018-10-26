# 私有npm仓库部署、使用、管理说明

## 使用

1. npm私服部署在公司内网服务器(IP = 172.16.2.214) , 对外端口 ：4873， 可通过[lmn](http://172.16.2.214:4873/)进行访问
2. 通过私服链接，童鞋们应该对其有相应的了解，它的使用几乎与npm一样

    ```sh
        npm i -g nrm # npm下载源管理包
        nrm add lmn http://172.16.2.214:4873 # 添加私服源
        nrm ls # 查看npm当前已有的下载源
        nrm use lmn # 使用私有源
        npm adduser  # 创建用户
        npm whoami # 查看当前登录的npm用户

        # ...
        # 后续使用与以往的使用完全一致，当lmn无法使用时，可用nrm切换到淘宝镜像
    ```

## 部署

1. 安装nodejs
    ```sh
        ls /etc/yum.repos.d/ # 查看已有的下载源

        # 添加一个下载源，例如NGINX
        vim /etc/yum.repos.d/nginx.repo # 会创建 nginx.repo文件

        # nginx.repo文件的内容
        [nginx]
        name=nginx repo
        baseurl=http://nginx.org/packages/centos/6/$basearch/
        gpgcheck=0
        enabled=1

        # 通过yum安装nginx
        yum install nginx -y
    ```
    nodejs的安装可以与上文提到的nginx安装相同，但是这里大可不必如此麻烦，如下：
    ```sh
       wget https://nodejs.org/dist/v9.0.0/node-v9.0.0.tar.gz # 该地址为Source Code下载地址
       tar -xvf node-v9.0.0.tar.gz # 解压
       mv node-v9.0.0 node9 # 改名
       ls node-v9.0.0/bin #查看node、npm , 此时的nodejs不是全局的
       ln -s /home/node9/bin/node /usr/local/node # 软连接node到全局
       ln -s /home/node9/bin/npm /usr/local/npm # 软连接npm到全局
       node -v #验证node是否安装成功
    ```

2. 通过npm安装sinopia
    ```sh
        npm i -g sinopia # 全局安装
        sinopia # 执行sinopia会启动一个本地服务：http://localhost:4873, 此服务目前只能本机访问
        # sinopia执行结束后会在用户根目录下生成两个文件夹 , .config 与 .local
        ls -a # 查看此两个文件夹
        ls .config/sinopia # 查看sinopia的config
        ls -a .local/share/sinopia/storage/ # 查看sinopia存储的npm包
        vim .config/sinopia/config.yaml # 修改sinopia配置
        vim .config/sinopia/htpasswd # 查看sinopia里的用户
    ```
    sinopia的完整配置请看此处，[点我](https://github.com/rlidwka/sinopia/blob/master/conf/full.yaml)

3. 通过pm2托管sinopia服务
    ```sh
        npm i -g pm2 # 全局安装，此时安装的pm2并非全局，需要软连一下才会全局
        ln -s /home/node9/bin/pm2 /usr/local/pm2 # 全局化
        pm2 start `which sinopia` # fork sinopia进程
        pm2 list # 查看sinopia服务
        pm2 kill sinopia # 杀死sinopia服务
        pm2 reload sinopia # 重启sinopia
        pm2 stop sinopia # 停止sinopia
    ```
    目前为止sinopia服务就搭建好了，但pm2无法开启启动，此处因为系统的问题，pm2 startup centos 需要centos的版本不小于7，除此之外的开机启动方案可能需要更高技术水平的支持，我目前无法实现

## 管理说明

> sinopia年久失修，在公网里也找不到更优秀的替代方案，有人可能认为直接同步npm代码，我个人没有这么做过，如果你觉得可以，那么请自己去尝试；再者就是cnpm，阿里的这帮童鞋实现npm的私服，但是它需要MySQL数据库，配置繁琐，也就是使用复杂，结果你懂得，而且功能上与sinopia相比较，并非出其右！sinopia直接存储的是文件夹，很直观，可以通过sftp到服务器上查看。

## 更新日志

* 2018年1月10日
  1. 鉴于 sinopia 年久失修，最近看到`verdaccio`，是从sinopia中fork出来的，有一个团队来维护
  2. verdaccio的安装部署与sinopia相同，上述管理方案不变

* 私服有了，我们需要把自己写的npm发布到改私服上，此处我需要禁止其他人来注册，只有admin用户具有npm publish权利 , 其他用户由管理员手动操作sinopia的httpwd文件进行添加

## 相关资源链接

> 1. [sinopia](https://github.com/rlidwka/sinopia)
> 2. [verdaccio](https://github.com/verdaccio/verdaccio)