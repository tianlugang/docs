# webpack

> 以下分析，来源于最新版的webpack(4.19.0),如果谬误，请即时斧正

## webpack函数主执行流程

   ```js
      webpack('[webpack.config.object]','[webpack.complied.callback]')
   ```

1. webpack启动并寻找`webpack.config.js`，使用`validateSchema`与`webpackOptionsSchema`校验传入的`options`对象，当配置中存在错误时，抛出一个`WebpackOptionsValidationError`错误。

2. 根据`options`类型实例化`Complier`,`options`只能是对象`object`与数组`array`类型，此外将会抛出错误提示语`Invalid argument: options`.当`options`类型为数组时，循环数组递归调用`webpack`函数，得到`MultiCompiler`实例

   1. 调用`WebpackOptionsDefaulter`处理`options`(`options = new WebpackOptionsDefaulter().process(options)`);
   2. 调用`options.context`实例化`Compiler`得到实例`complier`，并将`options`引用到`complier`;
   3. 调用`NodeEnvironmentPlugin`插件，设置文件系统（`inputFileSystem`与`outputFileSystem`），默认的文件系统基于[enhanced-resolve](https://github.com/webpack/enhanced-resolve),并且为`compiler.hooks.beforeRun`注册`NodeEnvironmentPlugin`
   4. 加载插件。遍历`options.plugins`，为每个插件传入`complier`实例，预执行这些插件
   5. 以上步骤结束，webpack基础环境配置完成，接下来触发`environment`与`afterEnvironment`；
   6. 调用`WebpackOptionsApply`重新处理`complier.options`(`new WebpackOptionsApply().process(options, compiler)`)，触发内置的插件集,本阶段将会读取编译缓存`CachedInputFileSystem`;
3. 根据`callback`决定使用启动编译，`callback`非函数时抛出错误
   * 当`options.watch === true`时，启动监听模式`watch`,如: `compiler.watch(watchOptions, callback);`
   * 当`options.watch !== true`时，执行`run`编译，如: `compiler.run(callback);`;
4. 返回`complier`实例。

## webpack内置hooks: 同步hook，只能在插件中使用

1. `complier.hooks`

   ```js
        // 以下代码片段，来自webpack 4.19.0 源码, 书写顺序不代表执行顺序
        this.hooks = {
            /** @type {SyncBailHook<Compilation>} */
            shouldEmit: new SyncBailHook(["compilation"]),
            /** @type {AsyncSeriesHook<Stats>} */
            done: new AsyncSeriesHook(["stats"]),
            /** @type {AsyncSeriesHook<>} */
            additionalPass: new AsyncSeriesHook([]),
            /** @type {AsyncSeriesHook<Compiler>} */
            beforeRun: new AsyncSeriesHook(["compiler"]),
            /** @type {AsyncSeriesHook<Compiler>} */
            run: new AsyncSeriesHook(["compiler"]),
            /** @type {AsyncSeriesHook<Compilation>} */
            emit: new AsyncSeriesHook(["compilation"]),
            /** @type {AsyncSeriesHook<Compilation>} */
            afterEmit: new AsyncSeriesHook(["compilation"]),

            /** @type {SyncHook<Compilation, CompilationParams>} */
            thisCompilation: new SyncHook(["compilation", "params"]),
            /** @type {SyncHook<Compilation, CompilationParams>} */
            compilation: new SyncHook(["compilation", "params"]),
            /** @type {SyncHook<NormalModuleFactory>} */
            normalModuleFactory: new SyncHook(["normalModuleFactory"]),
            /** @type {SyncHook<ContextModuleFactory>}  */
            contextModuleFactory: new SyncHook(["contextModulefactory"]),

            /** @type {AsyncSeriesHook<CompilationParams>} */
            beforeCompile: new AsyncSeriesHook(["params"]),
            /** @type {SyncHook<CompilationParams>} */
            compile: new SyncHook(["params"]),
            /** @type {AsyncParallelHook<Compilation>} */
            make: new AsyncParallelHook(["compilation"]),
            /** @type {AsyncSeriesHook<Compilation>} */
            afterCompile: new AsyncSeriesHook(["compilation"]),

            /** @type {AsyncSeriesHook<Compiler>} */
            watchRun: new AsyncSeriesHook(["compiler"]),
            /** @type {SyncHook<Error>} */
            failed: new SyncHook(["error"]),
            /** @type {SyncHook<string, string>} */
            invalid: new SyncHook(["filename", "changeTime"]),
            /** @type {SyncHook} */
            watchClose: new SyncHook([]),
            // TODO the following hooks are weirdly located here
            // TODO move them for webpack 5
            /** @type {SyncHook} */
            environment: new SyncHook([]),
            /** @type {SyncHook} */
            afterEnvironment: new SyncHook([]),
            /** @type {SyncHook<Compiler>} */
            afterPlugins: new SyncHook(["compiler"]),
            /** @type {SyncHook<Compiler>} */
            afterResolvers: new SyncHook(["compiler"]),
            /** @type {SyncBailHook<string, EntryOptions>} */
            entryOption: new SyncBailHook(["context", "entry"])
        };
   ```

2. `compilation.hooks`

   ```js
      // 以下代码片段，来自webpack 4.19.0 源码, 书写顺序不代表执行顺序

      this.hooks = {
        /** @type {SyncHook<Module>} */
        buildModule: new SyncHook(["module"]),
        /** @type {SyncHook<Module>} */
        rebuildModule: new SyncHook(["module"]),
        /** @type {SyncHook<Module, Error>} */
        failedModule: new SyncHook(["module", "error"]),
        /** @type {SyncHook<Module>} */
        succeedModule: new SyncHook(["module"]),
        /** @type {SyncWaterfallHook<DependencyReference, Dependency, Module>} */
        dependencyReference: new SyncWaterfallHook([
           "dependencyReference",
           "dependency",
           "module"
        ]),
        /** @type {SyncHook<Module[]>} */
        finishModules: new SyncHook(["modules"]),
        /** @type {SyncHook<Module>} */
        finishRebuildingModule: new SyncHook(["module"]),
        /** @type {SyncHook} */
        unseal: new SyncHook([]),
        /** @type {SyncHook} */
        seal: new SyncHook([]),
        /** @type {SyncHook} */
        beforeChunks: new SyncHook([]),
        /** @type {SyncHook<Chunk[]>} */
        afterChunks: new SyncHook(["chunks"]),
        /** @type {SyncBailHook<Module[]>} */
        optimizeDependenciesBasic: new SyncBailHook(["modules"]),
        /** @type {SyncBailHook<Module[]>} */
        optimizeDependencies: new SyncBailHook(["modules"]),
        /** @type {SyncBailHook<Module[]>} */
        optimizeDependenciesAdvanced: new SyncBailHook(["modules"]),
        /** @type {SyncBailHook<Module[]>} */
        afterOptimizeDependencies: new SyncHook(["modules"]),
        /** @type {SyncHook} */
        optimize: new SyncHook([]),
        /** @type {SyncBailHook<Module[]>} */
        optimizeModulesBasic: new SyncBailHook(["modules"]),
        /** @type {SyncBailHook<Module[]>} */
        optimizeModules: new SyncBailHook(["modules"]),
        /** @type {SyncBailHook<Module[]>} */
        optimizeModulesAdvanced: new SyncBailHook(["modules"]),
        /** @type {SyncHook<Module[]>} */
        afterOptimizeModules: new SyncHook(["modules"]),
        /** @type {SyncBailHook<Chunk[], ChunkGroup[]>} */
        optimizeChunksBasic: new SyncBailHook(["chunks", "chunkGroups"]),
        /** @type {SyncBailHook<Chunk[], ChunkGroup[]>} */
        optimizeChunks: new SyncBailHook(["chunks", "chunkGroups"]),
        /** @type {SyncBailHook<Chunk[], ChunkGroup[]>} */
        optimizeChunksAdvanced: new SyncBailHook(["chunks", "chunkGroups"]),
        /** @type {SyncHook<Chunk[], ChunkGroup[]>} */
        afterOptimizeChunks: new SyncHook(["chunks", "chunkGroups"]),
        /** @type {AsyncSeriesHook<Chunk[], Module[]>} */
        optimizeTree: new AsyncSeriesHook(["chunks", "modules"]),
        /** @type {SyncHook<Chunk[], Module[]>} */
        afterOptimizeTree: new SyncHook(["chunks", "modules"]),
        /** @type {SyncBailHook<Chunk[], Module[]>} */
        optimizeChunkModulesBasic: new SyncBailHook(["chunks", "modules"]),
        /** @type {SyncBailHook<Chunk[], Module[]>} */
        optimizeChunkModules: new SyncBailHook(["chunks", "modules"]),
        /** @type {SyncBailHook<Chunk[], Module[]>} */
        optimizeChunkModulesAdvanced: new SyncBailHook(["chunks", "modules"]),
        /** @type {SyncHook<Chunk[], Module[]>} */
        afterOptimizeChunkModules: new SyncHook(["chunks", "modules"]),
        /** @type {SyncBailHook} */
        shouldRecord: new SyncBailHook([]),
        /** @type {SyncHook<Module[], any>} */
        reviveModules: new SyncHook(["modules", "records"]),
        /** @type {SyncHook<Module[]>} */
        optimizeModuleOrder: new SyncHook(["modules"]),
        /** @type {SyncHook<Module[]>} */
        advancedOptimizeModuleOrder: new SyncHook(["modules"]),
        /** @type {SyncHook<Module[]>} */
        beforeModuleIds: new SyncHook(["modules"]),
        /** @type {SyncHook<Module[]>} */
        moduleIds: new SyncHook(["modules"]),
        /** @type {SyncHook<Module[]>} */
        optimizeModuleIds: new SyncHook(["modules"]),
        /** @type {SyncHook<Module[]>} */
        afterOptimizeModuleIds: new SyncHook(["modules"]),
        /** @type {SyncHook<Chunk[], any>} */
        reviveChunks: new SyncHook(["chunks", "records"]),
        /** @type {SyncHook<Chunk[]>} */
        optimizeChunkOrder: new SyncHook(["chunks"]),
        /** @type {SyncHook<Chunk[]>} */
        beforeChunkIds: new SyncHook(["chunks"]),
        /** @type {SyncHook<Chunk[]>} */
        optimizeChunkIds: new SyncHook(["chunks"]),
        /** @type {SyncHook<Chunk[]>} */
        afterOptimizeChunkIds: new SyncHook(["chunks"]),
        /** @type {SyncHook<Module[], any>} */
        recordModules: new SyncHook(["modules", "records"]),
        /** @type {SyncHook<Chunk[], any>} */
        recordChunks: new SyncHook(["chunks", "records"]),
        /** @type {SyncHook} */
        beforeHash: new SyncHook([]),
        /** @type {SyncHook<Chunk>} */
        contentHash: new SyncHook(["chunk"]),
        /** @type {SyncHook} */
        afterHash: new SyncHook([]),
        /** @type {SyncHook<any>} */
        recordHash: new SyncHook(["records"]),
        /** @type {SyncHook<Compilation, any>} */
        record: new SyncHook(["compilation", "records"]),
        /** @type {SyncHook} */
        beforeModuleAssets: new SyncHook([]),
        /** @type {SyncBailHook} */
        shouldGenerateChunkAssets: new SyncBailHook([]),
        /** @type {SyncHook} */
        beforeChunkAssets: new SyncHook([]),
        /** @type {SyncHook<Chunk[]>} */
        additionalChunkAssets: new SyncHook(["chunks"]),
        /** @type {AsyncSeriesHook} */
        additionalAssets: new AsyncSeriesHook([]),
        /** @type {AsyncSeriesHook<Chunk[]>} */
        optimizeChunkAssets: new AsyncSeriesHook(["chunks"]),
        /** @type {SyncHook<Chunk[]>} */
        afterOptimizeChunkAssets: new SyncHook(["chunks"]),
        /** @type {AsyncSeriesHook<CompilationAssets>} */
        optimizeAssets: new AsyncSeriesHook(["assets"]),
        /** @type {SyncHook<CompilationAssets>} */
        afterOptimizeAssets: new SyncHook(["assets"]),
        /** @type {SyncBailHook} */
        needAdditionalSeal: new SyncBailHook([]),
        /** @type {AsyncSeriesHook} */
        afterSeal: new AsyncSeriesHook([]),
        /** @type {SyncHook<Chunk, Hash>} */
        chunkHash: new SyncHook(["chunk", "chunkHash"]),
        /** @type {SyncHook<Module, string>} */
        moduleAsset: new SyncHook(["module", "filename"]),
        /** @type {SyncHook<Chunk, string>} */
        chunkAsset: new SyncHook(["chunk", "filename"]),
        /** @type {SyncWaterfallHook<string, TODO>} */
        assetPath: new SyncWaterfallHook(["filename", "data"]), // TODO MainTemplate
        /** @type {SyncBailHook} */
        needAdditionalPass: new SyncBailHook([]),
        /** @type {SyncHook<Compiler, string, number>} */
        childCompiler: new SyncHook([
            "childCompiler",
            "compilerName",
            "compilerIndex"
        ]),
        // TODO the following hooks are weirdly located here
        // TODO move them for webpack 5
        /** @type {SyncHook<object, Module>} */
        normalModuleLoader: new SyncHook(["loaderContext", "module"]),
        /** @type {SyncBailHook<Chunk[]>} */
        optimizeExtractedChunksBasic: new SyncBailHook(["chunks"]),
        /** @type {SyncBailHook<Chunk[]>} */
        optimizeExtractedChunks: new SyncBailHook(["chunks"]),
        /** @type {SyncBailHook<Chunk[]>} */
        optimizeExtractedChunksAdvanced: new SyncBailHook(["chunks"]),
        /** @type {SyncHook<Chunk[]>} */
        afterOptimizeExtractedChunks: new SyncHook(["chunks"])
        }

   ```

## webpack处理options事件流

> 此过程由`WebpackOptionsApply`插件完成，将用户传入的`options`引用到编译实例`compiler`上
1. 处理`outputPath` `recordsPath` `name` `dependencies`
2. 根据`target`加载相应的插件，如果没有此配置，则在WebpackOptionsDefaulter模块会被自动初始化为web
3. 处理`library`与`externals`
4. 处理`devtool`,根据其值处理使用相应的插件来处理`compiler`
5. 处理`javascript` `json` `webassemd`
6. 加载EntryOptionPlugin插件并触发`entry-option`的事件流
7. 处理`options.mode`
8. 处理`optimization`,加载相应的插件
9. 处理`options.cache`
10. 触发`compiler.hoooks.afterPlugins` 插件配置结束
11. 处理`compiler.resolves`，触发`compiler.hooks.afterResolvers`

## webpack编译`run`的生命周期

1. `compiler.hooks.environment` Node基础环境准备完成后触发，此阶段主要定义`inputFileSystem`和`outputFileSystem`
2. `compiler.hooks.afterEnvironment` 在`environment`,二者皆为同步hook
3. `compiler.hooks.beforeRun.callAsync` 开发运行之前触发同步'NodeEnvironmentPlugin',用于清除`inputFileSystem`与其监听器(定时器)
4. `compiler.hooks.run.callAsync` 什么都没做，一个空的hooks,用于插件开发
5. `compiler.readRecords` 在缓存`inputFileSystem`查找`recordsInputPath`对应的缓存资源
6. `compiler.hooks.normalModuleFactory` 调用插件'NormalModuleFactory'
7. `compiler.hooks.contextModuleFactory` 调用插件'ContextModuleFactory'
8. `compiler.hooks.beforeCompile.callAsync(params)`什么都没做
9. `compiler.hooks.compile.call(params)`什么都没做
10. `compiler.hooks.thisCompilation.call(compilation, params)`
11. `compiler.hooks.compilation.call(compilation, params')`
12. `compiler.hooks.make.callAsync(compilation)` 从入口点`entry`分析模块及其依赖的模块，创建这些模块对象,如果是打包dll，就调用的是插件'DllEntryPlugin'
13. `build-module` 构建模块,调用`loader`处理资源，调用`acorn`解析处理后的文件生产`AST`,深度遍历`AST`,构建该模块所依赖的模块
14. `after-compile` 完成构建
15. `seal` 封装构建结果，逐次对每个 module 和 chunk 进行整理，生成编译后的源码，合并，拆分，生成 hash，封装模块，生成assets
16. `emit` 把各个chunk输出到`outputFileSystem`
17. `after-emit` 完成输出
18. `done` 处理结束