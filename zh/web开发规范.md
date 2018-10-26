# web开发规范

> --- 规则用于遵守，不用于质疑，规范于长远，放眼于全局。
> --- 下文如有谬误，请及时斧正

## JavaScript开发规避

1. 标识符声明规范，符合js变量标识符规范(驼峰规则)的前提下，需要遵守如下标准:
   * 拥抱es6/7/8，避免var声明
   * 长度不小于4个，语义化，使用英文、全拼
   * DOM对象以`d`开头，如: `var dBody=document.querySelector('body');`
   * jQuery、zepto对象以`$`开头，如: `var $body=$('body');`
   * JS对象统一以`o`开头，如: `var oTemp={};`
   * 数组类型以`a`开头，如: `var aHello=[];`
   * 字符串类型以`s`开头，如: `var sHello='';`
   * 数字类型以`n`开头，如: `var nMax=1000;`
   * symbol类型以`l`开头，如: `var lHello=symbol('hello');`
   * boolean类型以`b`开头，如: `var bHello=false;`
   * 正则以`r`开头，如: `var rNum=/\d/;`
   * 常量采用全大写下划线连接的方式命名，如: `var NODE_ENV='production';`
   * 值类型须用字面量声明，禁止使用 new constructor
   * 静态字符串一律使用单引号或反引号，动态字符串使用反引号
   * 数组成员对变量赋值时，优先使用解构赋值
   * 使用扩展运算符(...)拷贝数组。
   * 优先箭头函数，在需要使用函数表达式的场合，尽量用箭头函数代替
   * 循环变量可以简写，比如：i，j，k等。
2. 函数参数尽可能少于3个，不能将函数内部的Context外泄，即形参对象不能传入外部函数
   * 类名首字母大写, 驼峰式命名，统一使用形容词+名词形式
   * 函数名首字母小写，驼峰式命名，统一使用动词或者动词+名词形式
   * 内部函数，使用_+动词+名词形式，内部函数必需在函数最后定义。
   * 对象方法与事件响应函数：对象方法命名使用+动词+名词形式；
   * 事件响应函数：触发事件对象名+事件名或者模块名
   * 函数参数及对象键名驼峰化，首字母小写
   * 函数做参数时，应位于参数末尾
   * 优先引用传递，其次共享传递
   * 异步callback须遵守`first-error`原则
   * new Function 优先于 eval
   * 函数的参数如果是对象的成员，优先使用解构赋值
   * 函数方法常用的动词：
     * get 获取/set 设置,
     * add 增加/remove 删除
     * create 创建/destory 移除
     * start 启动/stop 停止
     * open 打开/close 关闭,
     * read 读取/write 写入
     * load 载入/save 保存,
     * create 创建/destroy 销毁
     * begin 开始/end 结束,
     * backup 备份/restore 恢复
     * import 导入/export 导出,
     * split 分割/merge 合并
     * inject 注入/extract 提取,
     * attach 附着/detach 脱离
     * bind 绑定/separate 分离,
     * view 查看/browse 浏览
     * edit 编辑/modify 修改,
     * select 选取/mark 标记
     * copy 复制/paste 粘贴,
     * undo 撤销/redo 重做
     * insert 插入/delete 移除,
     * add 加入/append 添加
     * clean 清理/clear 清除,
     * index 索引/sort 排序
     * find 查找/search 搜索,
     * increase 增加/decrease 减少
     * play 播放/pause 暂停,
     * launch 启动/run 运行
     * compile 编译/execute 执行,
     * debug 调试/trace 跟踪
     * observe 观察/listen 监听,
     * build 构建/publish 发布
     * input 输入/output 输出,
     * encode 编码/decode 解码
     * encrypt 加密/decrypt 解密,
     * compress 压缩/decompress 解压缩
     * pack 打包/unpack 解包,
     * parse 解析/emit 生成
     * connect 连接/disconnect 断开,
     * send 发送/receive 接收
     * download 下载/upload 上传,
     * refresh 刷新/synchronize 同步
     * update 更新/revert 复原,
     * lock 锁定/unlock 解锁
     * check out 签出/check in 签入,
     * submit 提交/commit 交付
     * push 推/pull 拉,
     * expand 展开/collapse 折叠
     * begin 起始/end 结束,
     * start 开始/finish 完成
     * enter 进入/exit 退出,
     * abort 放弃/quit 离开
     * obsolete 废弃/depreciate 废旧,
     * collect 收集/aggregate 聚集
3. 禁止使用with语句，使用try{}catch(err){}时，try或catch中代码块必须使用函数包裹，禁止在try或catch中声明变量
4. 优先使用原生API，对于存在兼容性问题的API，应该明确指出兼容范畴并给出兼容解决方案，如果不能做到此二者，请考虑其他解决方案
5. 作用域链的深度不大于2，防止闭包作用域缓存不必要的(大)对象(大字符串等)
6. 统一使用单引号,分号结尾
7. 避免隐式类型转换，比较时优先使用`===， !==`类的运算符
8. 禁止使用HTML行内事件
9. 在兼容性许可时，优先使用CSS3动画，避免使用JavaScript动画或gif
10. Promise必须处理reject
11. 函数过长或逻辑复杂时，必须考虑功能分割，函数编码行数不大于50
12. 禁止使用代码片实现某一功能
13. DOM节点多次使用时必须缓存
14. 避免在页面初始化过程中使用脚本多次修改计算页面布局、影响布局的css样式

## HTML(模板)书写规范

1. HTML文档须符合w3c标准，[关于w3c标准](http://www.chinaw3c.org/standards.html)
2. 标签属性必须使用双引号包裹
3. 标签自定义属性必须定义到dataset中，如：data-attr="aaa"
4. js的钩子值统一以`js-`开头,如: id="js-nav"
   * 多匹配时，优先使用class，如: class="js-tab"
   * JS钩子不能用于定义css样式，避免在样式做删改时影响js功能
5. css的钩子优先使用class属性，class属性值须以`-`连接,不允许使用id,如: class=(tab-nav(
6. 布局标签与`内容标签`层次鲜明，布局标签不能与行内(块)元素并列，如下布局就属于错误布局，很明显的标签分类混乱
    ```html
       <i class="icon icon-title"></i>
       <h1>标题</>
       <span>辅助说明</span>
    ```
7. img标签: 必须外包于一层块级标签，通过父级块级元素占位，不t推荐使用css强行修改其(display)属性
8. a标签: 移动端中 target 属性统一使用默认值，如果没有特殊需求，禁止使用a标签打开新的页面。 在表示空链接或死链接时，推荐使用 #### 代替 javascript:;或javascript:void(0)等这种`js伪协议`，这里请参考相应的html标准文档，[个人推荐](http://www.cnblogs.com/Qian123/p/5345697.html)。a标签不能包含标题标签、table系列标签、列表系列标签、表单系列标签、段落标签等
9. 行内、文本、字体、超链接、图像等标签不能参与文档布局
10. SEO优化，除HTML5语义化布局标签外，页面只能有一个h1 img必须有alt属性，a标签须有title属性,明确a的reflow

## (S)CSS书写规范

1. 类名统一使用`-`连接，scss中禁止使用`&-`连接类名
2. 样式叠加规范，统一遵守`bootstrap`定义样式的规范，例如：`<button class=(btn btn-sm btn-primary(>按钮</button>`，其中`btn`约定俗称的按钮类名，`btn-sm`定义按钮的尺寸，`btn-primary`定义按钮的主题(颜色)
3. 禁止使用ID来定义样式，禁止使用复杂的css选择器
4. 优先使用flex布局，避免使用position，float等
5. css样式尽可能脱离对HTML结构的依赖，可公用、复用
6. 禁止使用HTML行内样式
7. 使用CSS3新选择器时应考虑其本身的兼容性，须要提供相应的无兼容问题的类名
8. 优先工具类，其次考虑业务类，为了减少css代码量
9. 省略值为 0 时的单位
10. CSS 可以做到，就不要使用 JS
11. 适当缩写值，提高可读性，节省了字节,并使阅读更加一目了然
12. 属性声明顺序 `显示(布局)属性` > `自身属性` > `文本属性和其他修饰`,即:
    `display > visibility > position > float > clear > list-style > top(left/bottom/right) > width > height > margin > padding > border > overflow > min-width(max-...) > font > text-align > text-decoration > vertical-align > white-space > color > background`
13. 元素选择器应该避免在 scoped 中出现 在 scoped 样式中，类选择器比元素选择器更好，因为大量使用元素选择器是很慢的
14. css模块化,模块化命名规范:
  
    1. 前缀
       * 布局 ((grid) (.g-)
       * 模块 (module) (.m-)
       * 元件 (unit) (.u-)
       * 功能 (function) (.f-)；
       * 皮肤 (skin) (.s-)；
       * 状态 (status) (.z-)。
       * 文档 (doc) (.doc-)
       * 头部 (head) (.hd-)
       * 主体 (body) (.bd-)
       * 尾部 (foot) (.ft-)
       * 主栏 (main) (.mn-)
       * 主栏子容器 (mainc) (.mnc-)
       * 侧栏 (side) (.sd-)
       * 侧栏子容器 (sidec) (.sdc-)
       * 盒容器 (wrap/box) (.wrap/box-)
    2. 模块(.m-)
       * 导航 (nav) (nav)
       * 子导航 (subnav) (snav)
       * 面包屑 (crumb) (crm)
       * 菜单 (menu) (menu),
       * 选项卡 (tab) (tab)
       * 标题区 (head/title) (hd/tt)
       * 内容区 (body/content) (bd/ct)
       * 列表 (list) (lst),
       * 表格 (table) (tb),
       * 表单 (form) (fm)
       * 热点 (hot) (hot)
       * 排行 (top) (top),
       * 登录 (login) (log),
       * 标志 (logo) (logo)
       * 广告 (advertise) (ad)
       * 搜索 (search) (sch)
       * 幻灯 (slide) (sld)
       * 提示 (tips) (tips)
       * 帮助 (help) (help)
       * 新闻 (news) (news)
       * 下载 (download) (dld),
       * 注册 (regist) (reg)
       * 投票 (vote) (vote)
       * 版权 (copyright) (cprt)
       * 结果 (result) (rst)
       * 标题 (title) (tt)
       * 按钮 (button) (btn)
       * 输入 (input) (ipt)
    3. 功能（.f-）
       * 浮动清除 (clearboth) (cb)
       * 向左浮动 (floatleft) (fl)
       * 向右浮动 (floatright) (fr)
       * 内联块级 (inlineblock) (ib)
       * 文本居中 (textaligncenter) (tac)
       * 文本居右 (textalignright) (tar)
       * 文本居左 (textalignleft) (tal)
       * 垂直居中 (verticalalignmiddle) (vam)
       * 溢出隐藏 (overflowhidden) (oh)
       * 完全消失 (displaynone) (dn)
       * 字体大小 (fontsize) (fs)
       * 字体粗细 (fontweight) (fwx)
    4. 皮肤（.s-）
       * 字体颜色 (fontcolor) (fc)
       * 背景 (background (bg)
       * 背景颜色 (backgroundcolor (bgc)
       * 背景图片 (backgroundimage (bgi)
       * 背景定位 (backgroundposition (bgp)
       * 边框颜色 (bordercolor (bdc)
    5. 状态（.z-）
       * 选中 (selected) (sel)
       * 当前 (current) (crt)
       * 显示 (show) (show)
       * 隐藏 (hide) (hide)
       * 打开 (open) (open)
       * 关闭 (close) (close)
       * 出错 (error) (err)
       * 不可用 (disabled) (dis)

## 注释规范

1. `画龙点睛`，要求注释无错别字、段落层次清晰、语言描述精确凝练、突显业务块关键词，方便快速定位、读懂业务点
2. JS中统一遵循jsDoc规范，注释与业务代码遵守就近原则，注释中包含对业务逻辑的探讨、突出开发思路

    ```javascript
      /**
      *文件用途说明
      *作者姓名、联系方式
      *制作日期
      **/

      /**
       * 函数注释
       * @function functionName 函数名称
       * @description 函数描述
       * @param {*} args... - 参数介绍
       * @return {*} 返回值说明
       */

      /**
       * 类注释
       * @constructor ClassName 类名
       * @description 函数描述
       * @property {*} args... 属性说明
       * @method methodName 方法说明
       * @public
       * @private 统一下划线开头
       * @static
       */

       var name = '111' // 单行注释

       /** 注释块必须以(至少两个星号)开头 **/
    ```
3. HTML(模板)中统一使用如下格式的注释: `<!-- 业务块名称、关键词、描述 --> ... <!-- 业务块名称、关键词、描述 END --> `，注释与业务块之间无空行，业务块与业务块之间以空行相隔
4. (S)CSS中对功能块注释需要从用途、支持业务点等方面进行描述，统一使用: `/*-- 业务块名称、关键词、描述 END */`或 `//-- 注释语句`

## 文件命名与应用程序标识符

1. 例如:`html中: <button id=(btn-submit(> ===> JS中: const $btnSubmit = $('#btn-submit');`，相应的文件名:`btn-submit.html ===> btn-submit.js`，到后端对应业务处理`controller/btn-submit.js ===> model/btn-submit.js`，业务块导出对象统一使用文件名去`-`后驼峰化
2. 图片、icon名称语义化，例如：`logo-2x.png logo-3x.png`等
3. 综上两点，文件名需要体现业务的科属、类别、动作，应该是统一使用`-`连接的述补短语

## vue开发规范

> 在如上规范的基础上

1. 如果模块只有一个输出值，就使用 export default，如果模块有多个输出值，就不使用 export default，export default 与普通的 export 不要同时使用
2. 指令有缩写一律采用缩写形式
3. v-for 循环必须加上 key 属性，在整个 for 循环中 key 需要唯一
4. 避免 v-if 和 v-for 同时用在一个元素上(性能问题)
   * 将数据替换为一个计算属性，让其返回过滤后的列表
   * 将 v-if 移动至容器元素上
5. Props 定义应该尽量详细
6. 避免 this.$parent
7. 调试信息 console.log() 尽可能使用 $log 、或者统一的 Logger
8. 优先三目运算、switch
9. 对用页面级组件样式，应该是有作用域的BEM规范, 对于公用组件或者全局组件库工具类

## resource

1. [css BEM](https://github.com/Tencent/tmt-workflow/wiki/%E2%92%9B-%5B%E8%A7%84%E8%8C%83%5D--CSS-BEM-%E4%B9%A6%E5%86%99%E8%A7%84%E8%8C%83)
