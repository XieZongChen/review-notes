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

## 访问者模式（Visitor）

访问者模式是将对数据的操作和数据结构进行分离，将对数据中各元素的操作封装成独立的类，使其在不改变数据结构的前提下可以拓展对数据新的操作。

```javascript
// 元素类
class Student {
    constructor(name, chinese, math, english) {
        this.name = name
        this.chinese = chinese
        this.math = math
        this.english = english
    }

    accept(visitor) {
        visitor.visit(this)
    }
}

// 访问者类
class ChineseTeacher {
    visit(student) {
        console.log(`语文${student.chinese}`)
    }
}

class MathTeacher {
    visit(student) {
        console.log(`数学${student.math}`)
    }
}

class EnglishTeacher {
    visit(student) {
        console.log(`英语${student.english}`)
    }
}

// 实例化元素类
const student = new Student('张三', 90, 80, 60)

// 实例化访问者类
const chineseTeacher = new ChineseTeacher()
const mathTeacher = new MathTeacher()
const englishTeacher = new EnglishTeacher()

// 接受访问
student.accept(chineseTeacher)
student.accept(mathTeacher)
student.accept(englishTeacher)
```

## 继承

继承（inheritance）是面向对象软件技术当中的一个概念。如果一个类别 B “继承自”另一个类别 A，就把这个 B 称为“A 的子类”，而把 A 称为“B 的父类”也可以称“A 是 B 的超类”。

继承可以使得子类具有父类别的各种属性和方法，从而不需要再次编写相同的代码。在子类别继承父类别的同时，可以重新定义某些属性，并重写某些方法，即覆盖父类别的原有属性和方法，使其获得与父类别不同的功能。

JavaScripy 常见的继承方式：
- 原型链继承
- 构造函数继承（借助 call）
- 组合继承
- 原型式继承
- 寄生式继承
- 寄生组合式继承

### 原型链继承

原型链继承是比较常见的继承方式之一，其中涉及的构造函数、原型和实例，三者之间存在着一定的关系，即每一个构造函数都有一个原型对象，原型对象又包含一个指向构造函数的指针，而实例则包含一个原型对象的指针

```javascript
function Parent() {
  this.name = 'parent1';
  this.play = [1, 2, 3]
}
function Child() {
  this.type = 'child2';
}
Child1.prototype = new Parent();
console.log(new Child())
```

原型链继承存在潜在问题：

```javascript
var s1 = new Child2();
var s2 = new Child2();
s1.play.push(4);
console.log(s1.play, s2.play); // [1,2,3,4]
```

改变 s1 的 play 属性，会发现 s2 也跟着发生变化了，这是因为 **两个实例使用的是同一个原型对象，内存空间是共享的**

### 构造函数继承

借助 call 调用 Parent 函数

```javascript
function Parent(){
    this.name = 'parent1';
}

Parent.prototype.getName = function () {
    return this.name;
}

function Child(){
    Parent1.call(this);
    this.type = 'child'
}

let child = new Child();
console.log(child);  // 没问题
console.log(child.getName());  // 会报错
```

可以看到，父类原型对象中一旦存在父类之前自己定义的方法，那么子类将无法继承这些方法

相比原型链继承方式，**父类的引用属性不会被共享，优化了第一种继承方式的弊端，但是只能继承父类的实例属性和方法，不能继承原型属性或者方法**

### 组合继承

组合继承是将原型链继承方式和构造函数继承方式组合起来的继承方式

```javascript
function Parent3 () {
    this.name = 'parent3';
    this.play = [1, 2, 3];
}

Parent3.prototype.getName = function () {
    return this.name;
}
function Child3() {
    // 第二次调用 Parent3()
    Parent3.call(this);
    this.type = 'child3';
}

// 第一次调用 Parent3()
Child3.prototype = new Parent3();
// 手动挂上构造器，指向自己的构造函数
Child3.prototype.constructor = Child3;
var s3 = new Child3();
var s4 = new Child3();
s3.play.push(4);
console.log(s3.play, s4.play);  // 不互相影响
console.log(s3.getName()); // 正常输出'parent3'
console.log(s4.getName()); // 正常输出'parent3'
```

这种方式看起来就没什么问题，原型链继承方式和构造函数继承方式的问题都解决了，但是从上面代码我们也可以看到 Parent3 执行了两次，造成了 **多构造一次的性能开销**

### 原型式继承

主要借助 Object.create 方法实现普通对象的继承

```javascript
let parent4 = {
    name: "parent4",
    friends: ["p1", "p2", "p3"],
    getName: function() {
      return this.name;
    }
};

let person4 = Object.create(parent4);
person4.name = "tom";
person4.friends.push("jerry");

let person5 = Object.create(parent4);
person5.friends.push("lucy");

console.log(person4.name); // tom
console.log(person4.name === person4.getName()); // true
console.log(person5.name); // parent4
console.log(person4.friends); // ["p1", "p2", "p3","jerry","lucy"]
console.log(person5.friends); // ["p1", "p2", "p3","jerry","lucy"]
```

这种继承方式的缺点也很明显，**Object.create 方法实现的是浅拷贝，多个实例的引用类型属性指向相同的内存，存在篡改的可能**

### 寄生式继承

寄生式继承在原型式继承基础上进行优化，利用这个浅拷贝的能力再进行增强，添加一些方法

```javascript
let parent5 = {
    name: "parent5",
    friends: ["p1", "p2", "p3"],
    getName: function() {
        return this.name;
    }
};

function clone(original) {
    let clone = Object.create(original);
    clone.getFriends = function() {
        return this.friends;
    };
    return clone;
}

let person5 = clone(parent5);

console.log(person5.getName()); // parent5
console.log(person5.getFriends()); // ["p1", "p2", "p3"]
```

其优缺点也很明显，**Object.create 方法实现的是浅拷贝，多个实例的引用类型属性指向相同的内存，存在篡改的可能**

### 寄生组合式继承

寄生组合式继承，借助解决普通对象的继承问题的 Object.create 方法，在组合继承基础上进行改造，这也是所有继承方式里面相对最优的继承方式

```javascript
function clone (parent, child) {
    // 这里改用 Object.create 就可以减少组合继承中多进行一次构造的过程
    child.prototype = Object.create(parent.prototype);
    child.prototype.constructor = child;
}

function Parent6() {
    this.name = 'parent6';
    this.play = [1, 2, 3];
}
Parent6.prototype.getName = function () {
    return this.name;
}
function Child6() {
    Parent6.call(this);
    this.friends = 'child5';
}

clone(Parent6, Child6);

Child6.prototype.getFriends = function () {
    return this.friends;
}

let person6 = new Child6();
console.log(person6); //{friends:"child5",name:"child5",play:[1,2,3],__proto__:Parent6}
console.log(person6.getName()); // parent6
console.log(person6.getFriends()); // child5
```

可以看到 person6 打印出来的结果，属性都得到了继承，方法也没问题

### ES6 extends 继承

在 ES6 中，针对 class 有 extends 关键字可以直接实现继承

```javascript
class Person {
  constructor(name) {
    this.name = name
  }
  // 原型方法
  // 即 Person.prototype.getName = function() { }
  // 下面可以简写为 getName() {...}
  getName = function () {
    console.log('Person:', this.name)
  }
}
class Gamer extends Person {
  constructor(name, age) {
    // 子类中存在构造函数，则需要在使用“this”之前首先调用 super()。
    super(name)
    this.age = age
  }
}
const asuna = new Gamer('Asuna', 20)
asuna.getName() // 成功访问到父类的方法
```

利用babel工具进行转换，我们会发现 extends 实际采用的也是寄生组合继承方式，因此也证明了这种方式是较优的解决继承的方式

### 总结

![image](https://github.com/XieZongChen/review-notes/assets/46394163/7a5c8238-d8ec-45d7-b727-430bfdcf1317)

通过 Object.create 来划分不同的继承方式，最后的寄生式组合继承方式是通过组合继承改造之后的最优继承方式，而 extends 的语法糖和寄生组合继承的方式基本类似















 













