# golang安装

- `gvm`是`golang`的版本管理工具

```bash
    # go的版本管理工具
    bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)

    # gvm 使用
    gvm list

    # 安装 go
    gvm install go1.15.2 -B

```

- 对于我国用户而言，这个工具提供的二进制包的下载源是： `https://storage.googleapis.com/golang`， 翻墙不成功，就无法下载，此时有两种方案：
   1. 去翻墙
   2. 修改下`gvm`的相关代码,`gvm`本身是通过`shell`实现的，修改起来很简单

    ```bash
        # 这句代码表示go源码的下载源
	    GO_BINARY_BASE_URL=${GO_BINARY_BASE_URL:-"https://golang.google.cn/dl"} #"https://storage.googleapis.com/golang"}
    ```