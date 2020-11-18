# ubuntu postgresql 安装使用

## 安装

```sh
    sudo apt-get update
    sudo apt-get install postgresql
```

安装完成后的默认信息:

* 创建名为"`postgres`"的Linux用户
* 创建名为"`postgres`"、不带密码的默认数据库账号作为数据库管理员
* 创建名为"`postgres`"的表
* 配置文件: config `/etc/postgresql/10/main`
* 数据:  data `/var/lib/postgresql/10/main`
* 本地化: locale `en_US.UTF-8`
* 服务默认端口: port `5432`
* socket `/var/run/postgresql`

## 终端操作

```bash
    sudo -u postgres psql
```

`sudo -u postgres psql` 是 `psql -U dbuser -d exampledb -h 127.0.0.1 -p 5432`的简化, 其中:

* `-U` 指定用户名, 如果当前Linux系统用户，同时也是PostgreSQL用户，则可以省略用户名（-U参数的部分）
* `-d`　指定数据库, 如果PostgreSQL内部还存在与当前系统用户同名的数据库，则数据库名也可以省略
* `-h` 指定ip
* `-p` 指定端口号

---

* 修改当前用户为`postgres`的登录密码

```bash
    alter user postgres with password ’123456‘
    alter role postgres with password ‘123456’
```

* 删除该管理员的密码，则可用命令

```bash
    sudo -u postgres psql -d postgres
```

* `\password`：设置密码
* `\q`：退出
* `\h`：查看SQL命令的解释，比如\h select。
* `?`：查看psql命令列表。
* `\l`：列出所有数据库。
* `\c [database_name]`：连接其他数据库。
* `\d`：列出当前数据库的所有表格。
* `\d [table_name]`：列出某一张表格的结构。
* `\du`：列出所有用户。
* `\e`：打开文本编辑器。
* `\conninfo`：列出当前数据库和连接的信息。

---

* 重启数据库

```bash
    sudo /etc/init.d/postgresql restart
```

## 配置数据库以允许远程连接

安装完成后，默认只能本地才能连接数据库，其他机子访问不了，需要进行配置。

1. `sudo gedit /etc/postgresql/pgsql版本号/main/postgresql.conf`　将 `#listen_addresses = ‘localhost’` 的注释去掉并改为 `listen_addresses = ‘*’`
2. `sudo gedit /etc/postgresql/pgsql版本号/main/pg_hba.conf` 在文件末尾添加： `host all all 0.0.0.0/0 md5` ，表示运行任何IP连接
3. 重启数据库

## 添加新用户与数据库基本操作命令

* 创建用户并绑定数据库

  ```bash
    # 进入客户端：
    sudo -u postgres psql

    # 创建用户并设置密码
    create user pg_user with password ‘123456’

    # 创建数据库 demo ，所有者为 pg_user
    create database demo owner pg_user

    # 将 demo 数据库的所有权限赋予 pg_user，否则 pg_user 只能登录psql，没有任何数据库操作权限：
    grant all privileges on database demo to pg_user
  ```

* 创建新表

    ```sql
        CREATE TABLE users(name VARCHAR(20), login_at DATE);
    ```

* 插入数据

    ```sql
        INSERT INTO users(name, login_at) VALUES(‘张三’, ‘2013-12-22’);
    ```

* 选择记录

    ```sql
        SELECT * FROM users;
    ```

* 更新数据

    ```sql
        UPDATE users set name = ‘李四’ WHERE name = ‘张三’;
    ```

* 删除记录

    ```sql
        DELETE FROM users WHERE name = ‘李四’ ;
    ```

* 添加栏位

    ```sql
        ALTER TABLE users ADD email VARCHAR(40);
    ```

* 更新结构

    ```sql
        ALTER TABLE users ALTER COLUMN login_at SET NOT NULL;
    ```

* 更名栏位

    ```sql
        ALTER TABLE users RENAME COLUMN login_at TO logout_at;
    ```

* 删除栏位

    ```sql
        ALTER TABLE users DROP COLUMN email;
    ```

* 表格更名

    ```sql
        ALTER TABLE users RENAME TO backup_tbl;
    ```

* 删除表格

    ```sql
        DROP TABLE IF EXISTS backup_tbl;
    ```
