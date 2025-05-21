# Vue 相关复习

## 双向绑定

双向绑定是由 DOM Listeners 和 Data Bindings 组成的。
从 View 侧看，ViewModel 中的 DOM Listeners 工具会监测页面上 DOM 元素的变化，如果有变化，则更改Model中的数据；
从 Model 侧看，当更新 Model 中的数据时，Data Bindings 工具会更新页面中的 DOM 元素。

## Vue 是如何实现数据双向绑定的

- 实现一个监听器 Observer：对数据对象进行遍历，包括子属性对象的属性，利用 Object.defineProperty() 对属性都加上 setter 和 getter。这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化
- 实现一个解析器 Compile：解析 Vue 模板指令，将模板中的变量都替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，调用更新函数进行数据更新
- 实现一个订阅者 Watcher：Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁 ，主要的任务是订阅 Observer 中的属性值变化的消息，当收到属性值变化的消息时，触发解析器 Compile 中对应的更新函数
- 实现一个订阅器 Dep：订阅器采用 发布-订阅 设计模式，用来收集订阅者 Watcher，对监听器 Observer 和订阅者 Watcher 进行统一管理

## Vue 响应式原理

### Object.defineProperty 响应式实现的原理

1. 调用 observer 方法，使用 defineProperty 依次对 data 中的属性实现监听
2. 如果是数组，则重写原生方法，用户修改数据数组后，先触发更新，再触发原生方法
3. 如果是对象，则依次遍历对象属性，并调用 defineReactive 方法
4. defineReactive 方法中，先调用 observer 方法，实现对象的深度监听，然后使用 defineProperty 监听属性

### Object.defineProperty 的缺点

- 复杂对象需要深度监听，一次性监听到底，计算量比较大
- 对于对象的新增/删除属性的操作，无法监听，需要 Vue.$set、Vue.$delete 辅助
- 需要重写数组原生方法实现数组监听。实现原理是，重写数组原生方法，用户修改数据数组后，先触发更新，再触发原生方法

### Proxy 的优势

- Proxy 可以直接监听对象而非属性
- Proxy 可以直接监听数组的变化
- Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的
- Proxy 返回的是一个新对象，可以只操作新的对象达到目的，而 Object.defineProperty 只能遍历对象属性直接修改
- Proxy 作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利

### vm.$set 的实现原理

如果目标是数组，直接使用数组的 splice 方法触发响应式

如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理（ defineReactive 方法就是 Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）