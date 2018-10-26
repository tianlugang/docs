## JS模块化规范
> 在此，我会具体阐述模块化规则与实现，会具体到代码设计，但也绝非面面俱到，如有谬误（错误、不合理）之处，敬请及时斧正

<br/>

### 模块化概念
> 网页程序越来越复杂，变得难以管理，而一个复杂的项目肯定有很多相似的功能模块，如果每次都需要重新编写模块肯定既费时又耗力，因此模块化是非常重要的，通过提取优质公网资源、结合项目所需，定制属于自己公司的代码积累，易于再利用、维护等，而引用别人编写模块的前提是要有统一的“导入姿态”，如果每个人有各自的写法，那么肯定会乱套，模块化就成了项目更迭的绊脚石，根本不会带来优势，而在js的生涯里存在好几种模块化的规范 ，此处，我们使用ES6模块化规范，其优势很明显，一旦浏览器js引擎支持，我们的代码就不再依赖babel等转换工具，将来的代码改动会非常小，它的优势很明显,尽管nodejs和原生浏览器端还没有实现该标准

<br>
  1. 容易进行静态分析（静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量）
  2. 面向未来的EcmaScript标准

### 模块化规则
>我们选择了ES6模块化规范，就必须遵循严格的EcmaScript标准
<br>

1. 包（模块）的命名规范
    * 可顾名思义，使用英文单词或汉语拼音，须符合‘述补短语’规则 ，词组合时须遵守驼峰规则
    * 包名中不能包含特殊字符、汉字，符合JS标识符命名规则
    * 同一个开发者在英文和拼音中首选一个后，后续包名必须使用首选的规则，不能一会儿拼音一会儿英文
    <br>
2. 包的严格构成
    * 包许可证文件（LICENSE），每个包必须有，内容不可修改，统一由脚手架工具管理
    * 包介绍文件（README.MD），须有严格的包作用、使用、bug、功能展望说明
    * 包开发设计文档，需要在README.MD提供入口，文档包含：包的设计思想、实现步骤、示例代码
    * 包API说明文档，需要在README.MD提供人口，完整列出包提供的API，给出完整的参数说明（类型、作用）、使用方法、性能评估（从使用时应该注意的事项上说明）
    * 包API更迭文档（changeLog）,API Bug修复 、API新增、废弃、更名等，要求以流水方式
    * 源码目录统一名为:lib，内部结构自建，目录命名与包名规范一致
    * 可执行文件目录统一名为:bin, 同上一点
    * 单元测试目录统一名为:test, 同上，test内的目录结构与源码目录结构相同
    * 包需要视图展示时，统一使用原生HTML格式，默认目录名：www，如果需要后端ssr，模板自定，服务自建，模板目录名：view， 静态资源目录: assets， 服务源码目录：src， 后期会提供相关的展示功能， 可以直观展示该功能
    * eslintrc、editorconfig的内容不允许修改，为了保证编码风格统一，gitignore可酌情修改，如果存在babelrc文件，可根据需求修改 ，.gitattributes文件根据自己的需求进行修改
    <br>
3. 包的分类
    * 根据兼容环境，大体分为浏览器环境和nodejs环境，但这样的话，代码无法共享，因此这不能成为合理的分类规则
    * 根据个人开发经验而言，使用JS全栈开发，前后端代码共享是很爽的事，因此包的分类规则就出来的
        1. 工具类包1，兼容所有原生浏览器、nodejs环境，不能在nodejs环境中写浏览器端兼容代码，反之亦然，尊重原生，这方法通常体现在String、Math、Date等javascript基础类型中
        2. 工具类包2，只运行在浏览器端，需要兼容PC机、Mobile时，代码分开写，方便在不同平台下切换使用
        3. 工具类包3，只运行在nodejs环境中
        4. 功能类包1，全栈功能包
        5. 功能类包2，web端功能包
        6. 功能类包3，nodejs端功能包
4. 包模板的分类
    * 包分类的分类决定了包的模板的分类，脚手架将内置此类模板
    * 包模板与包分类后在后续的脚手架管理中心提供视图展示
5. 具体编码规范
    * 统一 EcmacScript 规范
    * 标识符命名规则与包命名规则相同
    * 优先IIFE,避免Lift效应

## 模块化实现
* 需要在公司私有的Git Server上有注册账号，每个模块在该服务器上建有仓库
    1. 每个开发者在自己的账号下新建模块仓库
    2. 代码要提交到该Server的同时，还要publish的私有的NPM仓库，方便项目使用
* 关于Git 与 npm的使用，这些都是基础，如有问题，请自己补充
<br/>

### 相关网络资源
1. [阮一峰老师](http://www.ruanyifeng.com/home.html)
    * [《Javascript模块化编程一》](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html)
    * [《Javascript模块化编程二》](http://www.ruanyifeng.com/blog/2012/10/asynchronous_module_definition.html)
    * [《Javascript模块化编程三》](http://www.ruanyifeng.com/blog/2012/11/require_js.html)
2. [JavaScript编码规范](https://github.com/fex-team/styleguide/blob/master/javascript.md)
3. [JavaScript 编码规范指南](https://github.com/hustcer/jStyle)
4. [Google JavaScript 编码规范指南](http://alloyteam.github.io/JX/doc/specification/google-javascript.xml)
5. [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript/blob/master/README.md)
6. [述补短语](https://baike.baidu.com/item/%E8%BF%B0%E8%A1%A5%E7%9F%AD%E8%AF%AD)
7. [自定义 Git - Git属性](https://git-scm.com/book/zh/v1/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git%E5%B1%9E%E6%80%A7)
