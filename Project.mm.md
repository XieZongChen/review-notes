# 工程化相关复习

## Babel

### 基本知识

Babel 是一个 JavaScript 的转译器（transcompiler），用于将包含有高级语言特性的 JavaScript 代码，比如 ES9，转换为标准的 ES5 代码。

Babel 工作流程：
1. Parse：这个阶段的目的是将代码字符串转换成机器能读懂的 AST，这个过程分为词法分析和语法分析
   - 词法分析是通过扫描器（Scanner）将代码语句按照一定的规则将其拆分成一个个词（token）
   - 预发分析是将词法分析中得到的所有 token 组装成 AST 的过程。之后的流程都是对 AST 进行操作的。
2. Transform：这个阶段 Babel 会递归遍历整个 AST 树，遍历过程中不同 AST 节点会调用对应的节点处理函数来处理。Babel 使用了 [访问者模式](https://github.com/XieZongChen/review-notes/blob/main/DesignPattern.mm.md#%E8%AE%BF%E9%97%AE%E8%80%85%E6%A8%A1%E5%BC%8Fvisitor) 将 AST 节点和节点处理函数分离，用户定义的处理函数中会接收到 path 和 state 两个形参。
   - path：AST 通常会有许多节点，Path 是表示两个节点之间连接的对象。path 中不止有 parent、node、key、scope 等信息，还会有一些处理方法——`traverse(visitor, state)` 遍历当前节点的子节点
   - state: 是遍历过程中 AST 节点之间传递数据的方式。可以在遍历的过程中在 state 中存一些状态信息，用于后续的 AST 处理。
4. Generate：这个阶段 Babel 会将 AST 转化为生成代码。转化过程中可以选择是否生成 sourcemap，sourcemap 可以将生成代码关联到源代码。一般使用 sourcemap 的目的有两个
   - 调试代码时定位到源码：生成的 sourcemap 一般作为有特殊标记的注释放在文件末尾，调试工具都会支持解析这个注释，以方便调试时定位到源代码。
   - 线上报错定位到源码：一般不会将 sourcemap 上传到生产环境，而是单独上传到错误收集平台，比如 sentry 就提供了一个插件支持在打包完成后把 sourcemap 自动上传到 sentry 的后台，然后本地 sourcemap 删掉。

### plugin 和 Preset

所谓 Preset 就是一些 Plugin 组成的合集，可以将 Preset 理解为就是一些 Plugin 整合成的一个包。

这里需要注意的是 plugins 和 presets 顺序，总结如下：
1. 先执行完所有 Plugin，再执行 Preset
2. 多个 plugin，按照声明次序执行
3. 多个 preset，按照声明次序逆序执行

常见 Preset：
- `@babel/preset-env` 这个预设可以将高版本 JavaScript 代码根据内置的规则转译成为低版本的 JavaScript 代码。`@babel/preset-env` 仅仅针对语法阶段的转译，比如转译箭头函数、const/let 语法。针对一些 Api 或者 Es6 内置模块的 polyfill 是无法进行转译的。`@babel/preset-env` 不会包含任何低于 Stage 3 的 JavaScript 语法提案。如果需要兼容低于 Stage 3 阶段的语法则需要额外引入对应的 Plugin 进行兼容。
- `@babel/preset-react` 这个预设起到的就是将 jsx 进行转译的作用。React 17 前 jsx 会被 `@babel/preset-react` 编译成 React.createElement 函数的调用，用来创建虚拟 DOM 元素。React 17 后 `@babel/preset-react` 直接通过将 `react/jsx-runtime` 引入对 jsx 语法进行转换而不依赖 React.createElement，从而不用为 jsx 导入 React 了。删除 createElement 的可以减少捆绑文件的大小、减少动态属性查询。
- `@babel/preset-typescript` 将 TypeScript 代码编译成为 JavaScript 代码，和 tsc 作用相同。

常见 Plugin:
- `@babel/runtime` 不是一个 plugin，但它是 `@babel/plugin-transform-runtime` 插件的依赖。它可以将 `@babel/preset-env` 转译后的 polyfill 代码从文件中硬编码方式变为运行时的模块注入，从而（在某些条件下，比如重复代码过多时）缩小编译后的代码体积。可以将 @babel/runtime 理解成为 @babel/preset-env 的扩展工具库，虽然这只是针对 ECMA 语法部分的转译来说。
- `@babel/plugin-transform-runtime` 提供了三个功能：
  - 当我们需要一种不污染全局环境的 polyfill 时，我们可以通过 `@babel/plugin-transform-runtime` 来帮我们提供，这对于类库的打包起到了非常好的作用（通过 corejs 选项开启）。
  - 提供了自动删除内敛 Babel helper 并使用 `@babel/runtime/helpers` 来进行运行时注入（可使用 helpers 选项切换）。
  - 代码中使用 generators/async 函数时，它会自动根据 @babel/runtime/regenerator 进行运行时注入（可通过 regenerator 选项切换）。
 
### 最佳实践

#### 业务

在日常业务开发中，对于 **全局环境污染的问题往往并不是那么重要**。而业务代码最终的承载大部分是浏览器端，所以如果针对不同的目标浏览器支持度从而引入相应的 polyfill 无疑会对我们的代码体积产生非常大的影响，此时 **选择 `@babel/preset-env` 开启 useBuiltIns 的方式会更好一些**。

关于业务项目中究竟应该选择 useBuiltIns 中的 entry 还是 usage:
- entry: 它表示我们告诉 Babel 我们需要使用 polyfill ，使用 polyfill 的方式是在入口文件中引入 polyfill。通常在使用 Babel 时会将 Babel 编译排除 node_modules 目录（第三方模板），此时如果使用 usage，如果依赖的一些第三方包中使用到了一些比较新的 ES 内置模块比如 Promise，那没有 Polyfill 浏览器并不认识这个东西。而 entry 可以避免这种情况。
- usage: 它会分析我们的源代码，判断如果目标浏览器不支持并且我们代码中使用到的情况下才会在模块内进行引入对应的 polyfill。

为什么不用 `@babel/plugin-transform-runtime` 或 `@babel/runtime`?
`@babel/plugin-transform-runtime` 与环境无关，它并不会因为我们的页面的目标浏览器动态调整 polyfill 的内容，而 useBuiltIns 则会根据配置的目标浏览器而决定是否需要引入相应的 polyfill。

#### 类库

在我们开发类库时往往和浏览器环境无关，所以在进行 polyfill 时最主要考虑的应该是不污染全局环境，此时选择 `@babel/runtime` 无疑更加合适。

在类库开发中仅仅开启 `@babel/preset-env` 的语法转化功能配合 `@babel/runtime` 提供一种不污染全局环境的 polyfill 可能会更加适合你的项目场景。

## Webpack

### Webpack 构建流程

Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：
1. 初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数
2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件（Plugin），执行对象的 run 方法开始执行编译
3. 确定入口：根据配置中的 entry 找出所有的入口文件
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
5. 完成模块编译：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统 

在以上过程中，Webpack 会在特定的时间点广播出特定的事件，不同插件（Plugin）在监听到对应的事件后会执行相应的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

流程简单来说：
1. 初始化：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler
2. 编译：从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理
3. 输出：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中 

### Loader 和 Plugin 的区别

- Loader 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。 因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。
- Plugin 就是插件，基于事件流框架 Tapable，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。
- Loader 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。
- Plugin 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

### 常见 Loader、plugin

Loader
- file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件 (处理图片和字体)
- url-loader：与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)
- image-loader：加载并且压缩图片文件
- svg-inline-loader：将压缩后的 SVG 内容注入代码中
- babel-loader：把 ES6 转换成 ES5
- ts-loader: 将 TypeScript 转换成 JavaScript
- sass-loader：将SCSS/SASS代码转换成CSS
- css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
- style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS
- postcss-loader：扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前缀
- eslint-loader：通过 ESLint 检查 JavaScript 代码
- tslint-loader：通过 TSLint检查 TypeScript 代码
- cache-loader: 可以在一些性能开销较大的 Loader 之前添加，目的是将结果缓存到磁盘里 

Plugin
- uglifyjs-webpack-plugin：不支持 ES6 压缩 (Webpack4 以前)
- terser-webpack-plugin: 支持压缩 ES6 (Webpack4)
- webpack-parallel-uglify-plugin: 多进程执行代码压缩，提升构建速度
- speed-measure-webpack-plugin：简称 SMP，分析出 Webpack 打包过程中 Loader 和 Plugin 的耗时，有助于找到构建过程中的性能瓶颈。
- size-plugin：监控资源体积变化，尽早发现问题 

其他
- webpack-dashboard：可以更友好的展示相关打包信息。
- webpack-merge：提取公共配置，减少重复配置代码

### sourcemap 是什么？生产环境怎么用？

sourcemap 是将编译、打包、压缩后的代码映射回源代码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要 soucremap。

map 文件只要不打开开发者工具，浏览器是不会加载的。

线上环境一般有三种处理方案：
- hidden-source-map：借助第三方错误监控平台 Sentry 使用
- nosources-source-map：只会显示具体行数以及查看源代码的错误栈。安全性比 sourcemap 高
- sourcemap：通过 nginx 设置将 `.map` 文件只对白名单开放(公司内网) 

注意：避免在生产中使用 `inline-` 和 `eval-` devtool 配置选项，因为它们会增加 bundle 体积大小，并降低整体性能。一般推荐使用诸如 source-map 或 hidden-source-map 这样的 devtool 选项，这些选项可以生成单独的 source map 文件，而不会增加 bundle 文件的大小。







































