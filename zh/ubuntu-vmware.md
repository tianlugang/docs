# Ubuntu安装和卸载.bundle格式的VMware

> 文中用于演示的.bundle 文件是 VMware-Workstation-Full-15.5.0-14665864.x86_64，执行操作的系统平台是Ubuntu Linux.

**具体步骤如下**：

0. 将VMware的.bundle 文件放在当前用户的根目录下（和桌面同级）。
1. 赋予.bundle 文件执行权限

``` sh
  sudo chmod +x VMware-Workstation-Full-15.5.0-14665864.x86_64.bundle
```

2. 安装

``` sh
    sudo ./VMware-Workstation-Full-15.5.0-14665864.x86_64.bundle
```

3. 卸载

``` sh
    sudo vmware-installer -u vmware-workstation
```

**注**：
 * 1和2为安装步骤，3为安装完成后的卸载步骤。
 * 如果在Ubuntu系统上安装VMware时没有自动弹出图形化安装界面或者安装VMware后无法打开虚拟网络编辑器的界面，可以参考我的这篇博文解决Ubuntu系统下的VMware Workstation无法打开虚拟网络编辑器界面的问题。

# 资源

[VMware官网](https://www.vmware.com/)

