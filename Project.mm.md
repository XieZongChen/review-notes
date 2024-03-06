# 工程化相关复习

## Babel



### plugin 和 Preset

所谓 Preset 就是一些 Plugin 组成的合集，可以将 Preset 理解为就是一些 Plugin 整合成的一个包。

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










