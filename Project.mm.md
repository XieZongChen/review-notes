# 工程化相关复习

## 前端模块化

模块化的开发方式可以提供代码复用率，方便进行代码的管理。通常来说，**一个文件就是一个模块，有自己的作用域，只向外暴露特定的变量和函数**。目前流行的 js 模块化规范有 CommonJS、AMD、CMD、UMD 以及 ES6 的模块系统。

### ES6 Module

ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，旨在成为浏览器和服务器通用的模块解决方案。其模块功能主要由两个命令构成：export 和 import。export 命令用于规定模块的对外接口，import 命令用于输入其他模块提供的功能。

```javascript
/** 定义模块 math.js **/
var total = 0;
var add = function (a, b) {
    return a + b;
};
export { total, add };

/** 引用模块 **/
import { total, add } from './math';
function test(ele) {
    ele.textContent = add(99 + total);
}
```

使用 import 命令的时候，用户需要知道所要加载的变量名或函数名。ES6 还提供了 export default 命令，为模块指定默认输出，对应的 import 语句不需要使用大括号。

```javascript
/** export default **/
//定义输出
export default { basicNum, add };

//引入
import math from './math';
function test(ele) {
    ele.textContent = math.add(99 + math.basicNum);
}
```

**ES6 的模块不是对象，import 命令会被 JavaScript 引擎静态分析，在编译时就引入模块代码，而不是在代码运行时加载，所以无法实现条件加载**。也正因为这个，使得静态分析成为可能。

**ES6 模块的特征**：
- 严格模式：ES6 的模块自动采用严格模式
- import read-only 特性： import 的属性是只读的，不能赋值，类似于 const 的特性
- export/import 提升： import/export 必须位于模块顶级，不能位于作用域内；其次对于模块内的 import/export 会提升到模块顶部，这是在编译阶段完成的

### CommonJS

NodeJS 是 CommonJS 规范的主要实践者，它有四个重要的环境变量为模块化的实现提供支持：module、exports、require、global。实际使用时，用 module.exports 定义当前模块对外输出的接口（不推荐直接用 exports），用 require 加载模块。

```javascript
// 定义模块math.js
var total = 10;
function add(a, b) {
  return a + b;
}
// 需要向外暴露的函数、变量
module.exports = {
  add: add,
  total: total
}

/** 必须加./路径，不加的话只会去 node_modules 文件找 **/
// 引用自定义的模块时，参数包含路径，可省略.js
var math = require('./math');
math.add(2, 5);

// 引用核心模块时，不需要带路径
var http = require('http');
http.createService(...).listen(3000);
```

CommonJS 模块输出的是一个值的拷贝，也就是 exports 对象，这是模块内外的唯一关联。CommonJS 输出的内容，就是 exports 对象的属性。CommonJS 模块是运行时加载，模块运行结束就确定了 exports 对象的属性。

CommonJS 用同步的方式加载模块。**在服务端，模块文件都存放在本地磁盘，读取非常快，所以这样做不会有问题。但是在浏览器端，限于网络原因，更合理的方案是使用异步加载**。

exports 和 module.export 区别：
- exports：对于本身来讲是一个变量（对象），它不是 module 的引用，它是 `{}` 的引用，它指向 module.exports 的 `{}` 模块。只能使用 `.` 语法向外暴露变量。
- module.exports：module 是一个变量，指向一块内存，exports 是 module 中的一个属性，存储在内存中，然后 exports 属性指向 `{}` 模块。既可以使用 `.` 语法，也可以使用 `=` 直接赋值。

### AMD 和 require.js

AMD 规范采用 **异步方式加载模块**，模块的加载不影响它后面语句的运行。**所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行**。

这里介绍用 require.js 实现 AMD 规范的模块化：用 require.config() 指定引用路径等，用 definde() 定义模块，用 require() 加载模块。

首先我们需要引入 require.js 文件和一个入口文件 main.js。main.js 中配置 require.config() 并规定项目中用到的基础模块。

```javascript
/** 网页中引入 require.js 及 main.js **/
<script src="js/require.js" data-main="js/main"></script>

/** main.js 入口文件/主模块 **/
// 首先用config()指定各模块路径和引用名
require.config({
  baseUrl: "js/lib",
  paths: {
    "jquery": "jquery.min",  //实际路径为js/lib/jquery.min.js
    "underscore": "underscore.min",
  }
});
// 执行基本操作
require(["jquery","underscore"],function($,_){
  // some code here
});
```

引用模块的时候，我们将模块名放在 `[]` 中作为 reqiure() 的第一参数；如果我们定义的模块本身也依赖其他模块,那就需要将它们放在 `[]` 中作为 define() 的第一参数。

```javascript
// 定义math.js模块
define(function () {
    var basicNum = 0;
    var add = function (x, y) {
        return x + y;
    };
    return {
        add: add,
        basicNum :basicNum
    };
});

// 定义一个依赖underscore.js的模块
define(['underscore'],function(_){
  var classify = function(list){
    _.countBy(list,function(num){
      return num > 30 ? 'old' : 'young';
    })
  };
  return {
    classify :classify
  };
})

// 引用模块，将模块放在[]内
require(['jquery', 'math'],function($, math){
  var sum = math.add(10,20);
  $("#sum").html(sum);
});
```

### CMD 和 sea.js

CMD 是另一种 js 模块化方案，它与 AMD 很类似，不同点在于：**AMD 推崇依赖前置、提前执行，CMD 推崇依赖就近、延迟执行**。此规范其实是在 sea.js 推广过程中产生的。

```javascript
/** AMD 写法 **/
define(["a", "b", "c", "d", "e", "f"], function(a, b, c, d, e, f) { 
    // 等于在最前面声明并初始化了要用到的所有模块
    if (false) {
      // 即便没用到某个模块 b，但 b 还是提前执行了。**这就CMD要优化的地方**
      b.foo()
    } 
});

/** CMD写法 **/
define(function(require, exports, module) {
    var a = require('./a'); //在需要时申明
    a.doSomething();
    if (false) {
        var b = require('./b');
        b.doSomething();
    }
});

/** sea.js **/
// 定义模块 math.js
define(function(require, exports, module) {
    var $ = require('jquery.js');
    var add = function(a,b){
        return a+b;
    }
    exports.add = add;
});

// 加载模块
seajs.use(['math.js'], function(math){
    var sum = math.add(1+2);
});
```

### UMD（Universal Module Definition - 通用模块定义）

UMD 是 AMD 和 CommonJS 的一个糅合。AMD 是浏览器优先，异步加载；CommonJS 是服务器优先，同步加载。UMD 先判断是否支持 node.js 的模块，存在就使用 node.js；再判断是否支持 AMD（define是否存在），存在则使用 AMD 的方式加载。

```javascript
((root, factory) => {
  if (typeof define === 'function' && define.amd) {
    //AMD
    define(['jquery'], factory);
  } else if (typeof exports === 'object') {
    //CommonJS
    var $ = requie('jquery');
    module.exports = factory($);
  } else {
    //都不是，浏览器全局定义
    root.testModule = factory(root.jQuery);
  }
})(this, ($) => {
  //do something...  这里是真正的函数体
});
```

### ES6 模块与 CommonJS 模块的差异

1. **CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用**
  - CommonJS 模块输出的是值的拷贝，也就是说，**一旦输出一个值，模块内部的变化就影响不到这个值**。
  - ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令 import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。换句话说，ES6 的 import 有点像 Unix 系统的“符号连接”，原始值变了，import 加载的值也会跟着变。因此，**ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块**。
2. **CommonJS 模块是运行时加载，ES6 模块是编译时输出接口**
  - 运行时加载: CommonJS 模块就是对象；即在输入时是先加载整个模块，生成一个对象，然后再从这个对象上面读取方法，这种加载称为“运行时加载”。
  - 编译时加载: ES6 模块不是对象，而是通过 export 命令显式指定输出的代码，import 时采用静态命令的形式。即在 import 时可以指定加载某个输出值，而不是加载整个模块，这种加载称为“编译时加载”。模块内部引用的变化，会反应在外部。

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

### 文件监听原理

在发现源码发生变化时，自动重新构建出新的输出文件。Webpack开启监听模式，有两种方式：
- 启动 webpack 命令时，带上 --watch 参数
- 在配置 webpack.config.js 中设置 watch:true

原理：轮询判断文件的最后编辑时间是否变化，如果某个文件发生了变化，并不会立刻告诉监听者，而是先缓存起来，等 aggregateTimeout 后再执行。

缺点：每次需要手动刷新浏览器。想解决就用热更新。

### 热更新原理

Webpack 的热更新又称热替换（Hot Module Replacement），缩写为 HMR。 这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

![image](https://github.com/XieZongChen/review-notes/assets/46394163/6ec38821-e4ec-4a12-9950-55d7b5c46440)

上图是 webpack 配合 webpack-dev-server 进行应用开发的模块热更新流程图。绿色的方框是 webpack 代码控制的区域。蓝色方框是 webpack-dev-server 代码控制的区域，洋红色的方框是文件系统，而青色的方框是应用本身。

上图显示了我们修改代码到模块热更新完成的一个周期，通过深绿色的阿拉伯数字符号已经将 HMR 的整个过程标识了出来：
1. 第一步，在 webpack 的 watch 模式下，文件系统中某一个文件发生修改，webpack 监听到文件变化，根据配置文件对模块重新编译打包，并将打包后的代码通过简单的 JavaScript 对象保存在内存中。
2. 第二步是 webpack-dev-server 和 webpack 之间的接口交互，而在这一步，主要是 dev-server 的中间件 webpack-dev-middleware 和 webpack 之间的交互，webpack-dev-middleware 调用 webpack 暴露的 API 对代码变化进行监控，并且告诉 webpack，将代码打包到内存中。
3. 第三步是 webpack-dev-server 对文件变化的一个监控，这一步不同于第一步，并不是监控代码变化重新打包。当我们在配置文件中配置了 [devServer.watchContentBase](https://webpack.js.org/configuration/dev-server/#devserver-watchcontentbase) 为 true 的时候，Server 会监听这些配置文件夹中静态文件的变化，变化后会通知浏览器端对应用进行 live reload。注意，这儿是浏览器刷新，和 HMR 是两个概念。
4. 第四步也是 webpack-dev-server 代码的工作，该步骤主要是通过 [sockjs](https://github.com/sockjs/sockjs-client)（webpack-dev-server 的依赖）在浏览器端和服务端之间建立一个 websocket 长连接，将 webpack 编译打包的各个阶段的状态信息告知浏览器端，同时也包括第三步中 Server 监听静态文件变化的信息。浏览器端根据这些 socket 消息进行不同的操作。当然服务端传递的最主要信息还是新模块的 hash 值，后面的步骤根据这一 hash 值来进行模块热替换。
5. webpack-dev-server/client 端并不能够请求更新的代码，也不会执行热更模块操作，而把这些工作又交回给了 webpack，webpack/hot/dev-server 的工作就是根据 webpack-dev-server/client 传给它的信息以及 dev-server 的配置决定是刷新浏览器呢还是进行模块热更新。当然如果仅仅是刷新浏览器，也就没有后面那些步骤了。
6. HotModuleReplacement.runtime 是客户端 HMR 的中枢，它接收到上一步传递给他的新模块的 hash 值，它通过 JsonpMainTemplate.runtime 向 server 端发送 Ajax 请求，服务端返回一个 json，该 json 包含了所有要更新的模块的 hash 值，获取到更新列表后，该模块再次通过 jsonp 请求，获取到最新的模块代码。这就是上图中 7、8、9 步骤。
7. 而第 10 步是决定 HMR 成功与否的关键步骤，在该步骤中，HotModulePlugin 将会对新旧模块进行对比，决定是否更新模块，在决定更新模块后，检查模块之间的依赖关系，更新模块的同时更新模块间的依赖引用。
8. 最后一步，当 HMR 失败后，回退到 live reload 操作，也就是进行浏览器刷新来获取最新打包代码。

简单来说，热更新的完整流程分为两个阶段：
1. 启动阶段：
   1. 在文件系统中进行编译
   2. 通过 Webpack Compiler 进行打包
   3. 将打包好的文件传输给 Bundle Server
   4. 启动服务，使浏览器以 Server 的形式访问 bundle.js
2. 文件更新阶段：
   1. 文件系统中的内容发生变化
   2. 通过 Webpack Compiler 进行打包
   3. 将打包好的文件传输给 HMR Server，分析哪些代码发生了改变
   4. HMR Server（服务端） 将改变通知到 HMR Runtime（客户端）
   5. HMR Runtime 更新 bundle.js 中的代码

### 文件指纹是什么

文件指纹是打包后输出的文件名的后缀。有三种指纹：
- Hash：和整个项目的构建相关，只要项目文件有修改，整个项目构建的 hash 值就会更改
- Chunkhash：和 Webpack 打包的 chunk 有关，不同的 entry 会生出不同的 chunkhash
- Contenthash：根据文件内容来定义 hash，文件内容不变，则 contenthash 不变 

### 如何保证各个 loader 按照预想方式工作

可以使用 enforce 强制执行 loader 的作用顺序：
- pre 优先处理
- normal 正常处理（默认）
- inline 其次处理（官方不推荐）
- post 最后处理

### 如何优化构建速度

- 使用高版本的 Webpack 和 Node.js
- 多进程/多实例构建：HappyPack(不维护了)、thread-loader
- 压缩代码
  - 多进程并行压缩
    - webpack-paralle-uglify-plugin
    - uglifyjs-webpack-plugin 开启 parallel 参数 (不支持ES6)
    - terser-webpack-plugin 开启 parallel 参数
  - 通过 mini-css-extract-plugin 提取 Chunk 中的 CSS 代码到单独文件，通过 css-loader 的 minimize 选项开启 cssnano 压缩 CSS。
- 图片压缩
  - 使用基于 Node 库的 imagemin (很多定制选项、可以处理多种图片格式)
  - 配置 image-webpack-loader
- 缩小打包作用域：
  - exclude/include (确定 loader 规则范围)
  - resolve.modules 指明第三方模块的绝对路径 (减少不必要的查找)
  - resolve.mainFields 只采用 main 字段作为入口文件描述字段 (减少搜索步骤，需要考虑到所有运行时依赖的第三方模块的入口文件描述字段)
  - resolve.extensions 尽可能减少后缀尝试的可能性
  - noParse 对完全不需要解析的库进行忽略 (不去解析但仍会打包到 bundle 中，注意被忽略掉的文件里不应该包含 import、require、define 等模块化语句)
  - IgnorePlugin (完全排除模块)
  - 合理使用alias
- 提取页面公共资源：
  - 基础包分离：
    - 使用 html-webpack-externals-plugin，将基础包通过 CDN 引入，不打入 bundle 中
    - 使用 SplitChunksPlugin 进行(公共脚本、基础包、页面公共文件)分离(Webpack4内置) ，替代了 CommonsChunkPlugin 插件
- DLL：
  - 使用 DllPlugin 进行分包，使用 DllReferencePlugin(索引链接) 对 manifest.json 引用，让一些基本不会改动的代码先打包成静态资源，避免反复编译浪费时间。
  - HashedModuleIdsPlugin 可以解决模块数字id问题
- 充分利用缓存提升二次构建速度：
  - babel-loader 开启缓存
  - terser-webpack-plugin 开启缓存
  - 使用 cache-loader 或者 hard-source-webpack-plugin
- Tree shaking
  - 打包过程中检测工程中没有引用过的模块并进行标记，在资源压缩时将它们从最终的bundle中去掉(只能对ES6 Modlue生效) 开发中尽可能使用ES6 Module的模块，提高tree shaking效率
  - 禁用 babel-loader 的模块依赖解析，否则 Webpack 接收到的就都是转换过的 CommonJS 形式的模块，无法进行 tree-shaking
  - 使用 PurifyCSS(不在维护) 或者 uncss 去除无用 CSS 代码
    - purgecss-webpack-plugin 和 mini-css-extract-plugin 配合使用(建议)
- Scope hoisting
  - 构建后的代码会存在大量闭包，造成体积增大，运行代码时创建的函数作用域变多，内存开销变大。Scope hoisting 将所有模块的代码按照引用顺序放在一个函数作用域里，然后适当的重命名一些变量以防止变量名冲突
  - 必须是ES6的语法，因为有很多第三方库仍采用 CommonJS 语法，为了充分发挥 Scope hoisting 的作用，需要配置 mainFields 对第三方模块优先采用 jsnext:main 中指向的ES6模块化语法
- 动态 Polyfill
  - 建议采用 polyfill-service 只给用户返回需要的 polyfill，社区维护。 (部分国内奇葩浏览器UA可能无法识别，但可以降级返回所需全部polyfill)

### 代码分割

   待补充
   
### 简单描述一下编写 Loader 的思路

Loader 支持链式调用，所以开发上需要严格遵循“单一职责”，每个 Loader 只负责自己需要负责的事情。

[Loader 的 API](https://www.webpackjs.com/api/loaders/)

- Loader 运行在 Node.js 中，我们可以调用任意 Node.js 自带的 API 或者安装第三方模块进行调用
- Webpack 传给 Loader 的原内容都是 UTF-8 格式编码的字符串，当某些场景下 Loader 处理二进制文件时，需要通过 exports.raw = true 告诉 Webpack 该 Loader 是否需要二进制数据
- 尽可能的异步化 Loader，如果计算量很小，同步也可以
- Loader 是无状态的，我们不应该在 Loader 中保留状态
- 使用 loader-utils 和 schema-utils 为我们提供的实用工具
- 加载本地 Loader 方法
  - Npm link
  - ResolveLoader

### 简单描述一下编写 Plugin 的思路

webpack 在运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在特定的阶段钩入想要添加的自定义功能。Webpack 的 Tapable 事件流机制保证了插件的有序性，使得整个系统扩展性良好。

[Plugin 的 API](https://www.webpackjs.com/api/plugins/)

- compiler 暴露了和 Webpack 整个生命周期相关的钩子
- compilation 暴露了与模块和依赖有关的粒度更小的事件钩子
- 插件需要在其原型上绑定 apply 方法，才能访问 compiler 实例
- 传给每个插件的 compiler 和 compilation 对象都是同一个引用，若在一个插件中修改了它们身上的属性，会影响后面的插件
- 找出合适的事件点去完成想要的功能
  - emit 事件发生时，可以读取到最终输出的资源、代码块、模块及其依赖，并进行修改(emit 事件是修改 Webpack 输出资源的最后时机)
  - watch-run 当依赖的文件发生变化时会触发
- 异步的事件需要在插件处理完任务时调用回调函数通知 Webpack 进入下一个流程，不然会卡住




























