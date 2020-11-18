**git clean用法**

想批量删除branch中新加的文件(untracked files)，，git reset --hard不行～

首先确认要删除的文件
```sh
    git clean -fd -n
```

如果以上命令给出的文件列表是你想删除的， 那么接下来执行
```sh
    git clean -f -d
    # 或者
    git clean -fd
```

其中`-f`表示文件 -d表示目录, 如果还要删除.gitignore中的文件那么再加上`-x`

如果`git submodule`中也存在需要删除的文件那么需要再加个-f
```sh
    git clean -dff
```

[**详见**](http://stackoverflow.com/questions/61212/how-do-i-remove-local-untracked-files-from-my-current-git-branch)

**git删除未跟踪文件**

```bash
    # 删除 untracked files
    git clean -f

    # 连 untracked 的目录也一起删掉
    git clean -fd

    # 连 gitignore 的untrack 文件/目录也一起删掉 （慎用，一般这个是用来删掉编译出来的 .o之类的文件用的）
    git clean -xfd

    # 在用上述 git clean 前，墙裂建议加上 -n 参数来先看看会删掉哪些文件，防止重要文件被误删
    git clean -nxfd
    git clean -nf
    git clean -nfd

```
*resource*
* [MyGitee](https://gitee.com/lsgx/)
* [MyGithub](https://github.com/lsgxeva/)