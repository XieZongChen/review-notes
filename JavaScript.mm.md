# JavaScript 复习

# 数据类型

## JavaScript有哪些数据类型，它们的区别？

JavaScript 共有八种数据类型，分别是 7 种原始（undefined、boolean、number、string、bigInt、symbol、null）一种引用类型（object）

其中 Symbol 和 BigInt 是ES6 中新增的数据类型：
- Symbol 代表创建后独一无二且不可变的数据类型，它主要是为了解决可能出现的全局变量冲突的问题。
- BigInt 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这个数已经超出了 Number 能够表示的安全整数范围。

这些数据可以分为原始数据类型和引用数据类型：
- 栈：原始数据类型（Undefined、Null、Boolean、Number、String）
- 堆：引用数据类型（对象、数组和函数）

两种类型的区别在于存储位置的不同：
- 原始数据类型直接存储在栈（stack）中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；
- 引用数据类型存储在堆（heap）中的对象，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。

堆和栈的概念存在于数据结构和操作系统内存中，在数据结构中：
- 在数据结构中，栈中数据的存取方式为先进后出。
- 堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。

在操作系统中，内存被分为栈区和堆区：
- 栈区内存由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
- 堆区内存一般由开发着分配释放，若开发者不释放，程序结束时可能由垃圾回收机制回收。

## 数据类型检测的方式有哪些

1. typeof，`typeof 2 === 'number'`，数组、对象、null 都会被判断为 object，其他判断都正确
2. instanceof，`2 instanceof Number === true`，可以正确判断对象的类型，**其内部运行机制是判断在其原型链中能否找到该类型的原型**。instanceof 只能正确判断引用数据类型，而不能判断基本数据类型。instanceof 运算符可以用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性
3. constructor，`(2).constructor === Number`，constructor 有两个作用，一是判断数据的类型，二是对象实例通过 constrcutor 对象访问它的构造函数。需要注意，如果创建一个对象来改变它的原型，constructor 就不能用来判断数据类型了
4. Object.prototype.toString.call()，`Object.prototype.toString.call(2) === '[object Number]'`，使用 Object 对象的原型方法 toString 来判断数据类型。
   - 同样是检测对象 obj 调用t oString 方法，obj.toString() 的结果和 Object.prototype.toString.call(obj) 的结果不一样，这是为什么？这是因为 toString 是 Object 的原型方法，而 **Array、function 等类型作为 Object 的实例，都重写了 toString 方法**。不同的对象类型调用 toString 方法时，根据原型链的知识，调用的是对应的重写之后的 toString 方法（function 类型返回内容为函数体的字符串，Array 类型返回元素组成的字符串…），而不会去调用 Object 上原型 toString 方法（返回对象的具体类型），所以采用 obj.toString() 不能得到其对象类型，只能将 obj 转换为字符串类型；因此，在想要得到对象的具体类型时，应该调用 Object 原型上的 toString 方法。
  
## 判断数组的方式有哪些

1. 通过 Object.prototype.toString.call() 做判断，`Object.prototype.toString.call(['a']).slice(8,-1) === 'Array'`
2. 通过原型链做判断，`['a'].__proto__ === Array.prototype`
3. 通过 ES6 的 Array.isArray() 做判断，`Array.isArrray(['a'])`
4. 通过 instanceof 做判断，`['a'] instanceof Array`
5. 通过 Array.prototype.isPrototypeOf 判断，`Array.prototype.isPrototypeOf(['a'])`




























