# Tree-shaking
`Tree-shaking` 的实现，整个过程用到了 `Webpack` 的两个优化功能：

1. `usedExports` - 打包结果中只导出外部用到的成员；
2. `minimize` - 压缩打包结果。

如果把我们的代码看成一棵大树，那你可以这样理解：
1. `usedExports` 的作用就是标记树上哪些是枯树枝、枯树叶；
2. `minimize` 的作用就是负责把枯树枝、枯树叶摇下来。