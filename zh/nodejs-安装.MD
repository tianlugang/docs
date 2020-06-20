# Node.js 安装

* [使用`n`](https://github.com/tj/n)

(这种方式不支持windows平台)

```sh
  curl -L https://git.io/n-install | bash
  n latest # n <version>
```

* [使用`nvm`](https://github.com/creationix/nvm)

在Windows平台上, 请直接下载 [nvm-windows](https://github.com/coreybutler/nvm-windows/releases)
而对于其他平台, 你可以使用如下命令来安装`nvm`:

```sh
  # use curl
  curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash

  # use wget
  wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

* `n` 或 `nvm` 的使用，可以通过 `--help` 来查看