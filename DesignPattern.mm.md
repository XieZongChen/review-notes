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

### 观察者模式的实现

```javascript
// 被观察者对象
class Subject {

  constructor() {
    this.observerList = []; // 观察者列表
  }

  // 添加观察者
  addObserver(observer) {
    this.observerList.push(observer);
  }

  // 删除观察者
  removeObserver(observer) {
    const index = this.observerList.findIndex(o => o.name === observer.name);
    this.observerList.splice(index, 1);
  }

  // 通知观察者
  notifyObservers(message) {
    const observers = this.observerList;
    observers.forEach(observer => observer.notified(message));
  }

}

// 观察者
class Observer {

  constructor(name, subject) {
    this.name = name;
    if (subject) {
      // 如果需要观察其他对象，需要将自己添加到被观察对象的观察者列表里
      subject.addObserver(this);
    }
  }

  // 收到被观察者通知时的实现，函数名需要与被观察者约定好
  notified(message) {
    console.log(this.name, 'got message', message);
  }

}

// 使用
function main() {
  const subject = new Subject();
  const observerA = new Observer('observerA', subject);
  const observerB = new Observer('observerB', subject);
  subject.notifyObservers(`Hello from subject at ${new Date()}`);
  console.log('------------------------')

  setTimeout(() => {
    const observerC = new Observer('observerC');
    const observerD = new Observer('observerD');
    subject.addObserver(observerC);
    subject.addObserver(observerD);
    subject.notifyObservers(`Hello from subject at ${new Date()}`);
    console.log('------------------------')
  }, 1000)

  setTimeout(() => {
    subject.removeObserver(observerA);
    subject.notifyObservers(`Hello from subject at ${new Date()}`);
    console.log('------------------------')
  }, 2000)
}

main();

// observerA got message Hello from subject at Fri Jun 25 2021 16:25:09 GMT+0800 (GMT+08:00)
// observerB got message Hello from subject at Fri Jun 25 2021 16:25:09 GMT+0800 (GMT+08:00)
// ------------------------
// observerA got message Hello from subject at Fri Jun 25 2021 16:25:10 GMT+0800 (GMT+08:00)
// observerB got message Hello from subject at Fri Jun 25 2021 16:25:10 GMT+0800 (GMT+08:00)
// observerC got message Hello from subject at Fri Jun 25 2021 16:25:10 GMT+0800 (GMT+08:00)
// observerD got message Hello from subject at Fri Jun 25 2021 16:25:10 GMT+0800 (GMT+08:00)
// ------------------------
// observerB got message Hello from subject at Fri Jun 25 2021 16:25:11 GMT+0800 (GMT+08:00)
// observerC got message Hello from subject at Fri Jun 25 2021 16:25:11 GMT+0800 (GMT+08:00)
// observerD got message Hello from subject at Fri Jun 25 2021 16:25:11 GMT+0800 (GMT+08:00)
// ------------------------
```

### 发布订阅模式的实现

```javascript
// 发布订阅中心
class Broker {
  constructor() {
    this.messages = {}; // 消息列表，是一个对象数组，key 为消息类型，value 为当前类型的消息列表
    this.listeners = {}; // 订阅者列表，是一个对象数组，key 为消息类型，value 为订阅当前类型的订阅者列表
  }

  // 发布消息
  publish(type, content) {
    const existContent = this.messages[type];
    if (!existContent) {
      this.messages[type] = [];
    }
    this.messages[type].push(content);
  }

  // 订阅消息
  subscribe(type, cb) {
    const existListener = this.listeners[type];
    if (!existListener) {
      this.listeners[type] = [];
    }
    this.listeners[type].push(cb);
  }

  // 通知消息
  notify(type) {
    const messages = this.messages[type];
    const subscribers = this.listeners[type] || [];
    // 这里可以将当前类型的整个消息列表传入，让订阅者自行处理
    subscribers.forEach((cb) => cb(messages));
  }
}

// 发布者
class Publisher {
  constructor(name, context) {
    this.name = name;
    this.context = context;
  }

  publish(type, content) {
    this.context.publish(type, content);
  }
}

// 订阅者
class Subscriber {
  constructor(name, context) {
    this.name = name;
    this.context = context;
  }

  subscribe(type, cb) {
    this.context.subscribe(type, cb);
  }
}

function main() {
  const TYPE_A = 'music';
  const TYPE_B = 'movie';
  const TYPE_C = 'novel';

  const broker = new Broker();

  const publisherA = new Publisher('publisherA', broker);
  publisherA.publish(TYPE_A, 'we are young');
  publisherA.publish(TYPE_B, 'the silicon valley');
  const publisherB = new Publisher('publisherB', broker);
  publisherB.publish(TYPE_A, 'stronger');
  const publisherC = new Publisher('publisherC', broker);
  publisherC.publish(TYPE_B, 'imitation game');

  const subscriberA = new Subscriber('subscriberA', broker);
  subscriberA.subscribe(TYPE_A, (res) => {
    console.log('subscriberA received', res);
  });
  const subscriberB = new Subscriber('subscriberB', broker);
  subscriberB.subscribe(TYPE_C, (res) => {
    console.log('subscriberB received', res);
  });
  const subscriberC = new Subscriber('subscriberC', broker);
  subscriberC.subscribe(TYPE_B, (res) => {
    console.log('subscriberC received', res);
  });

  broker.notify(TYPE_A);
  broker.notify(TYPE_B);
  broker.notify(TYPE_C);
}

main();

// subscriberA received [ 'we are young', 'stronger' ]
// subscriberC received [ 'the silicon valley', 'imitation game' ]
// subscriberB received undefined
```































