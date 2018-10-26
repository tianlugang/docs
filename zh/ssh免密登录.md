## ssh免密登录


1. ssh-keygen的命令手册，通过”man ssh-keygen“命令：
    ```sh
        # 查看ssh-keygen手册
        man ssh-keygen
        # 大概会输出如下内容

        SSH-KEYGEN(1)             BSD General Commands Manual            SSH-KEYGEN(1)

        NAME
            ssh-keygen -- authentication key generation, management and conversion

        SYNOPSIS
            ssh-keygen [-q] [-b bits] [-t dsa | ecdsa | ed25519 | rsa | rsa1]
                        [-N new_passphrase] [-C comment] [-f output_keyfile]
            ssh-keygen -p [-P old_passphrase] [-N new_passphrase] [-f keyfile]
            ssh-keygen -i [-m key_format] [-f input_keyfile]
            ssh-keygen -e [-m key_format] [-f input_keyfile]
            ssh-keygen -y [-f input_keyfile]
            ssh-keygen -c [-P passphrase] [-C comment] [-f keyfile]
            ssh-keygen -l [-v] [-E fingerprint_hash] [-f input_keyfile]
            ssh-keygen -B [-f input_keyfile]
            ssh-keygen -D pkcs11
            ssh-keygen -F hostname [-f known_hosts_file] [-l]
            ssh-keygen -H [-f known_hosts_file]
            ssh-keygen -R hostname [-f known_hosts_file]
            ssh-keygen -r hostname [-f input_keyfile] [-g]
            ssh-keygen -G output_file [-v] [-b bits] [-M memory] [-S start_point]
            ssh-keygen -T output_file -f input_file [-v] [-a rounds] [-J num_lines]
        :
        # ...
        # 上面是ssh-keygen的主要使用方法
    ```
    <br/>
2. 通过命令”ssh-keygen -t rsa“在用户的根目录生成一个“.ssh”的文件夹，进入“.ssh”会生成以下几个文件：
    ```sh
        authorized_keys : 存放远程免密登录的公钥,主要通过这个文件记录多台机器的公钥
    　　 id_rsa : 生成的私钥文件
    　　 id_rsa.pub : 生成的公钥文件
    　　 known_hosts : 已知的主机公钥清单
        ssh公钥生效需满足至少下面两个条件：
    　　 1) .ssh目录的权限必须是700
    　　 2) .ssh/authorized_keys文件权限必须是600
    ```
    <br/>
3. 将生产的id_rsa.pub拷贝到远程服务器上的authorized_keys文件里 , 方式比较多
   ```sh
        # 通过ssh-copy-id
        ssh-copy-id -i ~/.ssh/id_rsa.pub <remote_ip>

        # 通过scp将内容追加到authorized_keys文件中
        scp -p ~/.ssh/id_rsa.pub root@<remote_ip>:/root/.ssh/authorized_keys

        # 通过服务器管理员手工将本地需要远程的电脑的id_rsa.pub文件的内容拷贝至服务器的~/.ssh/authorized_keys文件中
        cat <local_pub> >> ~/.ssh/authorized_keys
   ```
