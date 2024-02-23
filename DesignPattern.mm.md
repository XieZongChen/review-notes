# 设计模式

## 单例设计模式

单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

单例设计模式的案例：
- 线程池
- 全局缓存
- 浏览器中的 window 对象
- 网页登录浮窗

### 单例设计模式的实现——面向对象

```javascript
// 单例设计模式的实现：面向对象
var Singleton = function(name) {
  this.name = name;
  this.instance = null;
}
Singleton.prototype.getName = function(){
  return this.name;
}
Singleton.getInstance = function(name) {
  if(!this.instance) {
    this.instance = new Singleton(name);
  }
  return this.instance;
}

var instance1 = Singleton.getInstance('why');
var instance2 = Singleton.getInstance('www');
console.log(instance1===instance2); // 输出true

var obj1 = new Singleton('why');
var obj2 = new Singleton('www');
console.log(obj1.getName());        // 输出why
console.log(obj2.getName());        // 输出www
```

### 单例设计模式的实现——闭包

```javascript
// 单例设计模式的实现：闭包
var Singleton = function(name) {
  this.name = name;
}
Singleton.prototype.getName = function() {
  return this.name;
}
Singleton.getInstance = (function() {
  var instance = null;
  return function(name) {
    if(!instance) {
      instance = new Singleton(name)
    }
    return instance;
  }
})()

var instance1 = Singleton.getInstance('why');
var instance2 = Singleton.getInstance('www');
console.log(instance1 === instance2); // 输出true
```

### 透明的单例设计模式

无论以上的面向对象的单例实现还是闭包的单例实现，都通过 `Singleton.getInstance` 来获取 Singleton 类的唯一对象，这增加了这个类的不透明性，使用者必须知道 Singleton 是一个单例类，然后通过 Singleton.getInstance 方法才能获取单例对象，要解决这一问题，可以使用透明的单例设计模式

```javascript
// 透明的单例模式
var CreateDiv = (function(){
  var instance = null;
  var CreateDiv = function(html) {
    if(instance) {
      return instance;
    }
    this.html = html;
    this.init();
    instance = this;
    return instance;
  }
  CreateDiv.prototype.init = function() {
    var div = document.createElement('div');
    div.innerHTML = this.html;
    document.body.appendChild(div);
  }
  return CreateDiv;
})()

var instance1 = new CreateDiv('why');
var instance2 = new CreateDiv('www');
console.log(instance1===instance2); // 输出true
```

### 用代理实现单例模式

虽然上述透明的单例设计模式解决了不用通过 `Singleton.getInstance` 来获取单例类的唯一对象，但是在透明的单例设计模式中，构造函数 CreateDiv 违反了单一职责，它不仅要负责创建对象，而且还要负责保证单例，假如某一天需求变化了，不再需要创建单例的 div，则需要改写 CreateDiv 函数，解决这种问题，可以使用代理来实现单例模式

```javascript
// 用代理实现单例模式
var CreateDiv = function(html) {
  this.html = html;
  this.init();
}
CreateDiv.prototype.init = function() {
  var div = document.createElement('div');
  div.innerHTML = this.html;
  document.body.appendChild(div);
}
var ProxyCreateDiv = (function(){
  var instance = null;
  return function(html) {
    // 惰性单例
    if(!instance) {
      instance = new CreateDiv(html);
    }
    return instance;
  }
})()
var divInstance1 = new ProxyCreateDiv('why');
var divInstance2 = new ProxyCreateDiv('www');
console.log(divInstance1===divInstance2); // 输出true
```

## 观察者模式和发布订阅模式

这两个模式对比复习会比较好。

从表面上看：
- 观察者模式里，只有两个角色 —— 观察者 + 被观察者
- 发布订阅模式里，不仅仅只有发布者和订阅者两个角色，还有一个经常被我们忽略的 —— 经纪人 Broker

往更深层次讲：
- 观察者和被观察者，是松耦合的关系
- 发布者和订阅者，则完全不存在耦合

从使用层面上讲：
- 观察者模式，多用于单个应用内部
- 发布订阅模式，则更多的是一种跨应用的模式(cross-application pattern)，比如我们常用的消息中间件

![image](https://github.com/XieZongChen/review-notes/assets/46394163/15e75265-8b15-4d2b-b3e5-117b5540bf5a)
































