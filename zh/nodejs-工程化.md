# nodejs 工程化想法

1. 目的

   - 净化项目依赖，项目进入生产执行阶段后，没有第三方依赖
   - 不同项目的同一依赖，提取到项目顶层的依赖池里

2. 构想结构

``` xml
    <!-- 工作空间 -->
    <nodejs>
        <!-- 全局缓存 -->
        <node_caches />

        <!-- 依赖池 -->
        <node_modules />

        <!-- 工作区 -->
        <workspace>
            <!-- 项目组: 同一个项目的按需分层 -->
            <group>
                <project-A/>
                <project-B/>
            </group>

            <!-- 项目 -->
            <project />
        </workspace>

        <!-- 开发者清单 -->
        <developer />

        <!-- 依赖池清单 -->
        <package.json />
    </nodejs>
```

