# npm install 如何工作 —— node_modules 目录结构

npm install 执行完毕后，可以在 `node_modules` 中看到所有依赖的包。虽然使用者无需关注这个目录里的文件夹结构细节，只管在业务代码中引用依赖包即可，但了解 `node_modules` 的内容可以帮使用者更好理解 npm 如何工作，了解从 npm 2 到 npm 5 有哪些变化和改进。

### npm2 树结构

在安装依赖包时，采用简单的递归安装方法。执行 npm install 后，npm 2 依次递归安装 webpack 和 nconf 两个包到 node_modules 中。执行完毕后，会看到 ./node_modules 这层目录只含有这两个子目录。

``` sh
    ./node_modules/
        nconf/
            node_modules/
                async/
        webpack/
            node_modules/
                async/
```

进入更深一层 nconf 或 webpack 目录，将看到这两个包各自的 node_modules 中，已经由 npm 递归地安装好自身的依赖包。每一个包都有自己的依赖包，每个包自己的依赖都安装在了自己的 node_modules 中。依赖关系层层递进，构成了一整个依赖树，这个依赖树与文件系统中的文件结构树刚好层层对应。

可以通过 `npm ls` 列出包的依赖树，但这种方式存在弊端：

* 对复杂的工程, node_modules 内目录结构可能会太深，导致深层的文件路径过长而触发 windows 文件系统中，文件路径不能超过 260 个字符长的错误。
* 部分被多个包所依赖的包，很可能在应用 node_modules 目录中的很多地方被重复安装。随着工程规模越来越大，依赖树越来越复杂，这样的包情况会越来越多，造成大量的冗余。

### npm3 偏平结构

主要为了解决以上问题，npm3 的 node_modules 目录改成了更加扁平状的层级结构。文件系统中 webpack, nconf, async 的层级关系变成了平级关系，处于同一级目录中，这得益于 node 的模块加载机制。

``` sh
    ./node_modules/
        nconf/
        webpack/
        async/
```

在复杂依赖中，会有很多同名但版本不同的包存在于不同的依赖层级，对这些复杂的情况, npm3 会在安装时遍历整个依赖树，计算出最合理的文件夹安装方式，使得所有被重复依赖的包都可以去重安装

要查看直接依赖项，要通过 `npm ls` 命令指定 --depth 参数来查看

``` sh
    npm ls --depth 1
```

### npm5 package-lock文件

依然沿用 npm3 之后扁平化的依赖包安装方式，此外最大的变化是增加了 package-lock.json 文件, 用于锁定依赖安装结构，如果查看这个 json 的结构，会发现与 node_modules 目录的文件层级结构是一一对应的，文件记录 node_modules 里所有包的结构、层级和版本号甚至安装源，它也就事实上提供了 “保存” node_modules 状态的能力，只要有这样一个 lock 文件，不管在那一台机器上执行 npm install 都会得到完全相同的 node_modules 结果

```sh
    npm config set package-lock false 
```
