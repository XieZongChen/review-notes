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

## intanceof 操作符的实现原理及实现

instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。

```javascript
function myInstanceof(left, right) {
  // 获取对象的原型
  let proto = Object.getPrototypeOf(left)
  // 获取构造函数的 prototype 对象
  let prototype = right.prototype; 
 
  // 判断构造函数的 prototype 对象是否在对象的原型链上
  while (true) {
    if (!proto) return false;
    if (proto === prototype) return true;
    // 如果没有找到，就继续从其原型上找，Object.getPrototypeOf方法用来获取指定对象的原型
    proto = Object.getPrototypeOf(proto);
  }
}
```

## 判断数组的方式有哪些

1. 通过 Object.prototype.toString.call() 做判断，`Object.prototype.toString.call(['a']).slice(8,-1) === 'Array'`
2. 通过原型链做判断，`['a'].__proto__ === Array.prototype`
3. 通过 ES6 的 Array.isArray() 做判断，`Array.isArrray(['a'])`
4. 通过 instanceof 做判断，`['a'] instanceof Array`
5. 通过 Array.prototype.isPrototypeOf 判断，`Array.prototype.isPrototypeOf(['a'])`

## null 和 undefined 区别

首先 Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。

undefined 代表的含义是 **未定义**，null 代表的含义是 **空对象**。一般变量声明了但还没有定义的时候会返回 undefined，null 主要用于赋值给一些可能会返回对象的变量，作为初始化。

undefined 在 JavaScript 中不是一个保留字，这意味着可以使用 undefined 来作为一个变量名，但是这样的做法是非常危险的，它会影响对 undefined 值的判断。我们可以通过一些方法获得安全的 undefined 值，比如说 void 0。

当对这两种类型使用 typeof 进行判断时，Null 类型化会返回 “object”，这是一个历史遗留的问题。当使用双等号对两种类型的值进行比较时会返回 true，使用三个等号时会返回 false。undefined 转为数值时为 NaN，null 转换为数值时值为 0

## typeof null 的结果是什么，为什么

typeof null 的结果是 'object'。

在 JavaScript 第一个版本中，所有值都存储在 32 位的单元中，每个单元包含一个小的 **类型标签(1-3 bits)** 以及当前要存储值的真实数据。类型标签存储在每个单元的低位中，共有五种数据类型：

```javascript
000: object   - 当前存储的数据指向一个对象。
  1: int      - 当前存储的数据是一个 31 位的有符号整数。
010: double   - 当前存储的数据指向一个双精度的浮点数。
100: string   - 当前存储的数据指向一个字符串。
110: boolean  - 当前存储的数据是布尔值。
```

如果最低位是 1，则类型标签标志位的长度只有一位；如果最低位是 0，则类型标签标志位的长度占三位，为存储其他四种数据类型提供了额外两个 bit 的长度。

有两种特殊数据类型：
- undefined 的值是 (-2)30(一个超出整数范围的数字)；
- null 的值是机器码 NULL 指针(大多数平台下值为 0x00，所以 null 指针的值全是 0)

那也就是说null的类型标签也是000，和Object的类型标签一样，所以会被判定为Object。

## 为什么 0.1+0.2 !== 0.3，如何让其相等

在开发过程中遇到类似这样的问题：

```javascript
let n1 = 0.1, n2 = 0.2
console.log(n1 + n2)  // 0.30000000000000004
```

这里得到的不是想要的结果，要想等于0.3，就要把它进行转化：`(n1 + n2).toFixed(2) // 注意，toFixed为四舍五入`

toFixed(num) 方法可把 Number 四舍五入为指定小数位数的数字。那为什么会出现这样的结果呢？

计算机是通过二进制的方式存储数据的，所以计算机计算 0.1 + 0.2 的时候，实际上是计算的两个数的二进制的和。0.1 的二进制是 0.0001100110011001100...（1100循环），0.2 的二进制是 0.00110011001100...（1100循环），这两个数的二进制都是无限循环的数。那 JavaScript 是如何处理无限循环的二进制小数呢？

一般我们认为数字包括整数和小数，但是在 JavaScript 中只有一种数字类型：Number，它的实现遵循 IEEE754 标准，使用 64 位固定长度来表示，也就是标准的 double 双精度浮点数。在二进制科学表示法中，双精度浮点数的尾数部分（小数部分）最多只能保留 52 位，再加上前面的 1，其实就是保留 53 位有效数字，剩余的需要舍去，遵从“0 舍 1 入”的原则。

根据这个原则，0.1 和 0.2 的二进制数相加，再转化为十进制数就是：0.30000000000000004

### 双精度数是如何保存的

浮点数存储规则：`V = -1^(S) * (1 + Fraction) * 2^E`

![image](https://github.com/XieZongChen/review-notes/assets/46394163/39b71b70-b8a9-44b8-abca-935fcf87cf25)

对于0.1，它的二进制为：`0.00011001100110011001100110011001100110011001100110011001 10011...`

转为科学计数法（科学计数法的结果就是浮点数）：`1.1001100110011001100110011001100110011001100110011001*2^-4`

可以看出0.1的符号位为0，指数位为-4，小数位为：`1001100110011001100110011001100110011001100110011001`

IEEE 标准规定了一个偏移量，对于指数部分，每次都加这个偏移量进行保存，这样即使指数是负数，那么加上这个偏移量也就是正数了。由于 JavaScript 的数字是双精度数，这里就以双精度数为例，它的指数部分为 11 位，能表示的范围就是 0~2047，IEEE 固定双精度数的偏移量为 1023。
- 当指数位不全是 0 也不全是 1 时(规格化的数值)，IEEE 规定，阶码计算公式为 e-Bias。 此时 e 最小值是 1，则 1-1023= -1022，e 最大值是2046，则2 046-1023=1023，可以看到，这种情况下取值范围是-1022~1013。
- 当指数位全部是 0 的时候(非规格化的数值)，IEEE 规定，阶码的计算公式为 1-Bias，即 1-1023= -1022。
- 当指数位全部是 1 的时候(特殊值)，IEEE 规定这个浮点数可用来表示 3 个特殊值，分别是正无穷，负无穷，NaN。 具体的，小数位不为0的时候表示 NaN；小数位为 0 时，当符号位 s = 0 时表示正无穷，s = 1 时候表示负无穷。

对于上面的 0.1 的指数位为 -4，-4 + 1023 = 1019 转化为二进制就是：1111111011

所以，0.1表示为：`0 1111111011 1001100110011001100110011001100110011001100110011001`

### 如何实现 0.1 + 0.2 = 0.3

对于这个问题，一个直接的解决方法就是设置一个误差范围，通常称为“机器精度”。对 JavaScript 来说，这个值通常为 2^-52，在 ES6 中，提供了 Number.EPSILON 属性，而它的值就是 2^-52 ，只要判断 0.1 + 0.2 - 0.3 是否小于 Number.EPSILON，如果小于，就可以判断为 0.1 + 0.2 === 0.3

```javascript
function numberepsilon(arg1, arg2){                   
  return Math.abs(arg1 - arg2) < Number.EPSILON;        
}        

console.log(numberepsilon(0.1 + 0.2, 0.3)); // true
```

## 如何获取安全的 undefined 值

因为 undefined 是一个标识符，所以可以被当作变量来使用和赋值，但是这样会影响 undefined 的正常判断。表达式 void ... 没有返回值，因此返回结果是 undefined。void 并不改变表达式的结果，只是让表达式不返回值。因此可以用 void 0 来获得 undefined。

## typeof NaN 的结果是什么

NaN 指“不是一个数字”（not a number），NaN 是一个“警戒值”（sentinel value，有特殊用途的常规值），用于指出数字类型中的错误情况，即“执行数学运算没有成功，这是失败后返回的结果”

```javascript
typeof NaN; // "number"
```

NaN 是一个特殊值，它和自身不相等，是唯一一个非自反（自反，reflexive，即 x === x 不成立）的值。而 NaN !== NaN 为 true

## isNaN 和 Number.isNaN 函数的区别

- 函数 isNaN 接收参数后，会尝试将这个参数转换为数值，任何不能被转换为数值的的值都会返回 true，因此非数字值传入也会返回 true ，会影响 NaN 的判断。
- 函数 Number.isNaN 会首先判断传入参数是否为数字，如果是数字再继续判断是否为 NaN ，不会进行数据类型的转换，这种方法对于 NaN 的判断更为准确。

## 其他值到字符串的转换规则

- Null 和 Undefined 类型 ，null 转换为 "null"，undefined 转换为 "undefined"，
- Boolean 类型，true 转换为 "true"，false 转换为 "false"。
- Number 类型的值直接转换，不过那些极小和极大的数字会使用指数形式。
- Symbol 类型的值直接转换，但是只允许显式强制类型转换，使用隐式强制类型转换会产生错误。
- 对普通对象来说，除非自行定义 toString() 方法，否则会调用 toString()（Object.prototype.toString()）来返回内部属性 [[Class]] 的值，如"[object Object]"。如果对象有自己的 toString() 方法，字符串化时就会调用该方法并使用其返回值。

## 其他值到数字值的转换规则

- Undefined 类型的值转换为 NaN。
- Null 类型的值转换为 0。
- Boolean 类型的值，true 转换为 1，false 转换为 0。
- String 类型的值转换如同使用 Number() 函数进行转换，如果包含非数字值则转换为 NaN，空字符串为 0。
- Symbol 类型的值不能转换为数字，会报错。
- 对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。
  - 为了将值转换为相应的基本类型值，会使用抽象操作 ToPrimitive 首先（通过内部操作 DefaultValue）检查该值是否有 valueOf() 方法。如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用 toString() 的返回值（如果存在）来进行强制类型转换。
  - 如果 valueOf() 和 toString() 均不返回基本类型值，会产生 TypeError 错误。

## 其他值到布尔类型的值的转换规则

假值：undefined、null、false、+0、-0、NaN、""

假值的布尔强制类型转换结果为 false。从逻辑上说，假值列表以外的都应该是真值。

## || 和 && 操作符的返回值

|| 和 && 首先会对第一个操作数执行条件判断，如果其不是布尔值就先强制转换为布尔类型，然后再执行条件判断。

- 对于 || 来说，如果条件判断结果为 true 就返回第一个操作数的值，如果为 false 就返回第二个操作数的值。
- && 则相反，如果条件判断结果为 true 就返回第二个操作数的值，如果为 false 就返回第一个操作数的值。

|| 和 && 返回它们其中一个操作数的值，而非条件判断的结果

## Object.is() 与比较操作符 ==、=== 的区别

- 使用双等号 `==` 进行相等判断时，如果两边的类型不一致，则会进行强制类型转化后再进行比较。
- 使用三等号 `===` 进行相等判断时，如果两边的类型不一致时，不会做强制类型准换，直接返回 false。
- 使用 Object.is 来进行相等判断时，一般情况下和三等号的判断相同，它处理了一些特殊的情况，比如 -0 和 +0 不再相等，两个 NaN 是相等的。

## 什么是 JavaScript 中的包装类型

在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象，如：

```javascript
const a = "abc";
a.length; // 3
a.toUpperCase(); // "ABC"
```

在访问 `'abc'.length` 时，JavaScript 将 `'abc'` 在后台转换成 `String('abc')`，然后再访问其 length 属性。

JavaScript 也可以使用 Object 函数显式地将基本类型转换为包装类型：

```javascript
var a = 'abc'
Object(a) // String {"abc"}
```

也可以使用 valueOf 方法将包装类型倒转成基本类型：

```javascript
var a = 'abc'
var b = Object(a)
var c = b.valueOf() // 'abc'
```

看看如下代码会打印出什么：

```javascript
var a = new Boolean(false);
if (!a) {
    console.log("Oops"); // never runs
}
```

答案是什么都不会打印，因为虽然包裹的基本类型是false，但是false被包裹成包装类型后就成了对象，所以其非值为false，所以循环体中的内容不会运行。

## JavaScript 中如何进行隐式类型转换

首先要介绍 ToPrimitive 方法，这是 JavaScript 中每个值隐含的自带的方法，用来将值（无论是基本类型值还是对象）转换为基本类型值。如果值为基本类型，则直接返回值本身；如果值为对象，其看起来大概是这样：

```javascript
/**
* @obj 需要转换的对象
* @type 期望的结果类型
*/
ToPrimitive(obj, type)
```

type 的值为 number 或者 string。

1. 当 type 为 number 时规则如下：
   - 调用 obj 的 valueOf 方法，如果为原始值，则返回，否则下一步；
   - 调用 obj 的 toString 方法，后续同上；
   - 抛出 TypeError 异常。
2. 当 type 为 string 时规则如下：
   - 调用 obj 的 toString 方法，如果为原始值，则返回，否则下一步；
   - 调用 obj 的 valueOf 方法，后续同上；
   - 抛出 TypeError 异常。

可以看出两者的主要区别在于调用 toString 和 valueOf 的先后顺序。默认情况下：
- 如果对象为 Date 对象，则 type 默认为 string；
- 其他情况下，type 默认为 number。

总结上面的规则，对于 Date 以外的对象，转换为基本类型的大概规则可以概括为一个函数：

```javascript
var objToNumber = value => Number(value.valueOf().toString())
objToNumber([]) === 0
objToNumber({}) === NaN
```

而 JavaScript 中的隐式类型转换主要发生在 `+、-、*、/` 以及 `==、>、<` 这些运算符之间。而这些运算符只能操作基本类型值，所以在进行这些运算前的第一步就是将两边的值用 ToPrimitive 转换成基本类型，再进行操作。

以下是基本类型的值在不同操作符的情况下隐式转换的规则（对于对象，其会被 ToPrimitive 转换成基本类型，所以最终还是要应用基本类型转换规则）：
1. **`+` 操作符**：`+` 操作符的两边有至少一个 string 类型变量时，两边的变量都会被隐式转换为字符串；其他情况下两边的变量都会被转换为数字。
   - ```javascript
     1 + '23' // '123'
     1 + false // 1 
     1 + Symbol() // Uncaught TypeError: Cannot convert a Symbol value to a number
     '1' + false // '1false'
     false + true // 1
     ```
2. **`-`、`*`、`\` 操作符**
   - NaN 也是一个数字
     ```javascript
     1 * '23' // 23
     1 * false // 0
     1 / 'aa' // NaN
     ```
3. **对于 `==` 操作符**
   - 操作符两边的值都尽量转成number：
     ```javascript
     3 == true // false, 3 转为number为3，true转为number为1
     '0' == false //true, '0'转为number为0，false转为number为0
     '0' == 0 // '0'转为number为0
     ```
4. **对于 `<` 和 `>` 比较符**
   - 如果两边都是字符串，则比较字母表顺序：
     ```javascript
     'ca' < 'bd' // false
     'a' < 'b' // true
     ```
   - 其他情况下，转换为数字再比较：
     ```javascript
     '12' < 13 // true
     false > -1 // true
     ```
     
以上说的是基本类型的隐式转换，而对象会被 ToPrimitive 转换为基本类型再进行转换：

```javascript
var a = {}
a > 2 // false
```

其对比过程如下：

```javascript
a.valueOf() // {}, 上面提到过，ToPrimitive默认type为number，所以先valueOf，结果还是个对象，下一步
a.toString() // "[object Object]"，现在是一个字符串了
Number(a.toString()) // NaN，根据上面 < 和 > 操作符的规则，要转换成数字
NaN > 2 //false，得出比较结果
```

又比如：

```javascript
var a = {name:'Jack'}
var b = {age: 18}
a + b // "[object Object][object Object]"
```

运算过程如下：

```javascript
a.valueOf() // {}，上面提到过，ToPrimitive默认type为number，所以先valueOf，结果还是个对象，下一步
a.toString() // "[object Object]"
b.valueOf() // 同理
b.toString() // "[object Object]"
a + b // "[object Object][object Object]"
```

## `+` 操作符什么时候用于字符串的拼接

根据 ES5 规范，如果某个操作数是字符串或者能够通过以下步骤转换为字符串的话，+ 将进行拼接操作。

如果其中一个操作数是对象（包括数组），则首先对其调用 ToPrimitive 抽象操作，该抽象操作再调用 `[[DefaultValue]]`，以数字作为上下文。如果不能转换为字符串，则会将其转换为数字类型来进行计算。

简单来说就是，如果 + 的其中一个操作数是字符串（或者通过以上步骤最终得到字符串），则执行字符串拼接，否则执行数字加法。

那么对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字。

## 为什么会有 BigInt 的提案

JavaScript 中 Number.MAX_SAFE_INTEGER 表示最⼤安全数字，计算结果是 9007199254740991，即在这个数范围内不会出现精度丢失（⼩数除外）。但是⼀旦超过这个范围，js 就会出现计算不准确的情况，这在⼤数计算的时候不得不依靠⼀些第三⽅库进⾏解决，因此官⽅提出了 BigInt 来解决此问题。

## object.assign 和扩展运算法是深拷贝还是浅拷贝，两者区别

扩展运算符：

```javascript
let outObj = {
  inObj: {a: 1, b: 2}
}
let newObj = {...outObj}
newObj.inObj.a = 2
console.log(outObj) // {inObj: {a: 2, b: 2}}
```

Object.assign():

```javascript
let outObj = {
  inObj: {a: 1, b: 2}
}
let newObj = Object.assign({}, outObj)
newObj.inObj.a = 2
console.log(outObj) // {inObj: {a: 2, b: 2}}
```

可以看到，两者都是浅拷贝。
- Object.assign() 方法接收的第一个参数作为目标对象，后面的所有参数作为源对象。然后把所有的源对象合并到目标对象中。它会修改了一个对象，因此会触发 ES6 setter。
- 扩展操作符（…）使用它时，数组或对象中的每一个值都会被拷贝到一个新的数组或对象中。它不复制继承的属性或类的属性，但是它会复制 ES6 的 symbols 属性。

# ES6

## let、const、var 的区别

1. 块级作用域：块作用域由 `{ }` 包括，let 和 const 具有块级作用域，var 不存在块级作用域。块级作用域解决了 ES5 中的两个问题：
   - 内层变量可能覆盖外层变量
   - 用来计数的循环变量泄露为全局变量
2. 变量提升：var 存在变量提升，let 和 const 不存在变量提升，即在变量只能在声明之后使用，否在会报错。
3. 给全局添加属性：浏览器的全局对象是 window，Node 的全局对象是 global。var 声明的变量为全局变量，并且会将该变量添加为全局对象的属性，但是 let 和 const 不会。
4. 重复声明：var 声明变量时，可以重复声明变量，后声明的同名变量会覆盖之前声明的遍历。const 和 let 不允许重复声明变量。
5. 暂时性死区：在使用 let、const 命令声明变量之前，该变量都是不可用的。这在语法上，称为暂时性死区。使用 var 声明的变量不存在暂时性死区。
6. 初始值设置：在变量声明时，var 和 let 可以不用设置初始值。而 const 声明变量必须设置初始值。
7. 指针指向：let 和 const 都是 ES6 新增的用于创建变量的语法。let 创建的变量是可以更改指针指向（可以重新赋值）。但 const 声明的变量是不允许改变指针的指向。

| 区别 | var | let | const |
| --- | --- | --- | ---- |
| 是否有块级作用域 | × | ✔️ | ✔️ |
| 是否存在变量提升 | ✔️ | × | × |
| 是否添加全局属性 | ✔️ | × | × |
| 能否重复声明变量 | ✔️ | × | × |
| 是否存在暂时性死区 | × | ✔️ | ✔️ |
| 是否必须设置初始值 | × | × | ✔️ |
| 能否改变指针指向 | ✔️ | ✔️ | × |

## const 对象的属性可以修改吗

const 保证的并不是变量的值不能改动，而是 **变量指向的那个内存地址不能改动**。对于基本类型的数据（数值、字符串、布尔值），其值就保存在变量指向的那个内存地址，因此等同于常量。

但对于引用类型的数据（主要是对象和数组）来说，变量指向数据的内存地址，保存的只是一个指针，const 只能保证这个指针是固定不变的，至于它指向的数据结构是不是可变的，就完全不能控制了。

## 如果 new 一个箭头函数的会怎么样

箭头函数是 ES6 中的提出来的，它没有 prototype，也没有自己的 this 指向，更不可以使用 arguments 参数，所以不能 new 一个箭头函数。

new 操作符的实现步骤如下：
1. 创建一个对象
2. 将构造函数的作用域赋给新对象（也就是将对象的 `__proto__` 属性指向构造函数的 prototype 属性）
3. 指向构造函数中的代码，构造函数中的 this 指向该对象（也就是为这个对象添加属性和方法）
4. 返回新的对象

所以，上面的第二、三步，箭头函数都是没有办法执行的。

## 箭头函数与普通函数的区别

1. 箭头函数比普通函数更加简洁
   - 如果没有参数，就直接写一个空括号即可
   - 如果只有一个参数，可以省去参数的括号
   - 如果有多个参数，用逗号分割
   - 如果函数体的返回值只有一句，可以省略大括号
   - 如果函数体不需要返回值，且只有一句话，可以给这个语句前面加一个void关键字。最常见的就是调用一个函数：
     ```javascript
     let fn = () => void doesNotReturn();
     ```
2. 箭头函数没有自己的 this：箭头函数不会创建自己的 this， 所以它没有自己的 this，它只会在自己作用域的上一层继承 this。所以箭头函数中 this 的指向在它在定义时已经确定了，之后不会改变。
3. 箭头函数继承来的 this 指向永远不会改变
4. call()、apply()、bind() 等方法不能改变箭头函数中 this 的指向
5. 箭头函数不能作为构造函数使用
6. 箭头函数没有自己的 arguments 对象，在箭头函数中访问 arguments 实际上获得的是它外层函数的 arguments 值
7. 箭头函数没有 prototype
8. 箭头函数不能用作 Generator 函数，不能使用 yeild 关键字

## 箭头函数的this指向哪⾥

箭头函数不同于传统 JavaScript 中的函数，箭头函数并没有属于⾃⼰的 this，它所谓的 this 是捕获其所在上下⽂的 this 值，作为⾃⼰的 this 值，并且由于没有属于⾃⼰的 this，所以是不会被 new 调⽤的，这个所谓的 this 也不会被改变。

可以⽤ Babel 理解⼀下箭头函数:

```javascript
// ES6 
const obj = { 
  getArrow() { 
    return () => { 
      console.log(this === obj); 
    }; 
  } 
}
```

转化后：

```javascript
// ES5，由 Babel 转译
var obj = { 
   getArrow: function getArrow() { 
     var _this = this; 
     return function () { 
        console.log(_this === obj); 
     }; 
   } 
};
```

## 扩展运算符的作用及使用场景

1. 对象扩展运算符，对象的扩展运算符 (...) 用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中。如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。**扩展运算符对对象实例的拷贝属于浅拷贝**
2. 数组扩展运算符，数组的扩展运算符可以将一个数组转为用逗号分隔的参数序列，且每次只能展开一层数组。其有以下用法
   - 将数组转换为参数序列 `add(...numbers)`
   - 复制数组
   - 合并数组
   - 扩展运算符与解构赋值结合起来，用于生成数组 `const [first, ...rest] = [1, 2, 3, 4, 5]`。注意：如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。
   - 将字符串转为真正的数组 `[...'hello']    // [ "h", "e", "l", "l", "o" ]`
   - 任何 Iterator 接口的对象，都可以用扩展运算符转为真正的数组 `const args = [...arguments]; // 函数的 arguments 对象`

## 对对象与数组的解构的理解

解构是 ES6 提供的一种新的提取数据的模式，这种模式能够从对象或数组里有针对性地拿到想要的数值。 

1. 数组的解构。在解构数组时，以元素的位置为匹配条件来提取想要的数据的：
   ```javascript
   const [a, b, c] = [1, 2, 3]
   ```
   最终，a、b、c 分别被赋予了数组第 0、1、2 个索引位的值：数组里的 0、1、2 索引位的元素值，精准地被映射到了左侧的第 0、1、2 个变量里去，这就是数组解构的工作模式。还可以通过给左侧变量数组设置空占位的方式，实现对数组中某几个元素的精准提取：
   ```javascript
   const [a,,c] = [1, 2, 3]
   ```
   通过把中间位留空，可以顺利地把数组第一位和最后一位的值赋给 a、c 两个变量
2. 对象的解构。对象解构比数组结构稍微复杂一些，也更显强大。在解构对象时，是以属性的名称为匹配条件，来提取想要的数据的。现在定义一个对象：
   ```javascript
   const stu = {
     name: 'Bob',
     age: 24
   }
   ```
   假如想要解构它的两个自有属性，可以这样：
   ```javascript
   const { name, age } = stu
   ```
   这样就得到了 name 和 age 两个和 stu 平级的变量。注意，对象解构严格以属性名作为定位依据，所以就算调换了 name 和 age 的位置，结果也是一样的：
   ```javascript
   const { age, name } = stu
   ```

## 如何提取高度嵌套的对象里的指定属性

有时会遇到一些嵌套程度非常深的对象：

```javascript
const school = {
   classes: {
      stu: {
         name: 'Bob',
         age: 24,
      }
   }
}
```

像此处的 name 这个变量，嵌套了四层，位于 school 对象的“儿子的儿子”对象里面。要想把 name 提取出来，一种比较笨的方法是逐层解构：

```javascript
const { classes } = school
const { stu } = classes
const { name } = stu
name // 'Bob'
```

但是还有一种更标准的做法，可以用一行代码来解决这个问题：

```javascript
const { classes: { stu: { name } }} = school
       
console.log(name)  // 'Bob'
```

可以在解构出来的变量名右侧，通过 `冒号 + { 目标属性名 }` 这种形式，进一步解构它，一直解构到拿到目标数据为止。

## 对 rest 参数的理解

扩展运算符被用在函数形参上时，它还可以把一个分离的参数序列整合成一个数组：

```javascript
function mutiple(...args) {
  let result = 1;
  for (var val of args) {
    result *= val;
  }
  return result;
}
mutiple(1, 2, 3, 4) // 24
```

这里，传入 mutiple 的是四个分离的参数，但是如果在 mutiple 函数里尝试输出 args 的值，会发现它是一个数组：

```javascript
function mutiple(...args) {
  console.log(args)
}
mutiple(1, 2, 3, 4) // [1, 2, 3, 4]
```

这就是 `…rest` 运算符的又一层威力了，它可以把函数的多个入参收敛进一个数组里。这一点经常用于获取函数的多余参数，或者像上面这样处理函数参数个数不确定的情况。也可以用于取剩余参数。

## ES6 中模板语法与字符串处理

ES6 提出了“模板语法”的概念。

```javascript
var name = 'css'   
var career = 'coder' 
var hobby = ['coding', 'writing']
var finalString = `my name is ${name}, I work as a ${career} I love ${hobby[0]} and ${hobby[1]}`
```

字符串不仅更容易拼了，也更易读了，代码整体的质量都变高了。这就是模板字符串的第一个优势——允许用 `${}` 的方式嵌入变量。但这还不是问题的关键，模板字符串的关键优势有两个：
- 在模板字符串中，空格、缩进、换行都会被保留
- 模板字符串完全支持“运算”式的表达式，可以在 `${}` 里完成一些计算

除了模板语法外， ES6中还新增了一系列的字符串方法用于提升开发效率：
1. 存在性判定：在过去，当判断一个字符/字符串是否在某字符串中时，只能用 indexOf > -1 来做。现在 ES6 提供了三个方法：includes、startsWith、endsWith，它们都会返回一个布尔值来告诉你是否存在
2. 自动重复：可以使用 repeat 方法来使同一个字符串输出多次（被连续复制多次）

# JavaScript 基础

## new 操作符的实现原理

new操作符的执行过程：
1. 首先创建了一个新的空对象
2. 设置原型，将对象的原型设置为函数的 prototype 对象。
3. 让函数的 this 指向这个对象，执行构造函数的代码（为这个新对象添加属性）
4. 判断函数的返回值类型，如果是值类型，返回创建的对象。如果是引用类型，就返回这个引用类型的对象。

具体实现：

```javascript
function objectFactory() {
  let newObject = null;
  let constructor = Array.prototype.shift.call(arguments);
  let result = null;
  // 判断参数是否是一个函数
  if (typeof constructor !== "function") {
    console.error("type error");
    return;
  }
  // 新建一个空对象，对象的原型为构造函数的 prototype 对象
  newObject = Object.create(constructor.prototype);
  // 将 this 指向新建对象，并执行函数
  result = constructor.apply(newObject, arguments);
  // 判断返回对象
  let flag = result && (typeof result === "object" || typeof result === "function");
  // 判断返回结果
  return flag ? result : newObject;
}
// 使用方法
objectFactory(构造函数, 初始化参数);
```

## map 和 Object 的区别

|   | Map | Object |
| - | --- | ------ |
| 意外的键 | 默认情况不包含任何键，只包含显式插入的键 | 有一个原型, 原型链上的键名有可能和自己在对象上的设置的键名产生冲突 |
| 键的类型 | 键可以是任意值，包括函数、对象或任意基本类型 | 键必须是 String 或是 Symbol |
| 键的顺序 | key 是有序的。因此，当迭代的时候，Map 对象以插入的顺序返回键值 | 键是无序的 |
| Size | 键值对个数可以轻易地通过 size 属性获取 | 键值对个数只能手动计算 |
| 迭代 | 是 iterable 的，所以可以直接被迭代 | 迭代 Object 需要以某种方式获取它的键然后才能迭代 |
| 性能 | 在频繁增删键值对的场景下表现更好 | 在频繁添加和删除键值对的场景下未作出优化 |

## map 和 weakMap 的区别

|   | Map | WeakMap |
| - | --- | ------- |
| 键的类型 | 键可以是任意值，包括函数、对象或任意基本类型 | 只接受对象作为键名（ null 除外） |
| 回收机制 | 键名所引用的对象是强引用，即垃圾回收机制会将该引用考虑在内 | 键名所引用的对象是弱引用，即垃圾回收机制不将该引用考虑在内。只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存 |
| 方法 | size、set(key,value)、get(key)、has(key)、delete(key)、clear() | set(key,value)、get(key)、has(key)、delete(key) |

## JavaScript 有哪些内置对象

全局的对象（ global objects ）或称标准内置对象，不要和 "全局对象（global object）" 混淆。这里说的全局的对象是说在全局作用域里的对象。全局作用域中的其他对象可以由用户的脚本创建或由宿主程序提供。

标准内置对象的分类：
1. 值属性，这些全局属性返回一个简单值，这些值没有自己的属性和方法。例如 Infinity、NaN、undefined、null 字面量
2. 函数属性，全局函数可以直接调用，不需要在调用时指定所属对象，执行结束后会将结果直接返回给调用者。例如 eval()、parseFloat()、parseInt() 等
3. 基本对象，基本对象是定义或使用其他对象的基础。基本对象包括一般对象、函数对象和错误对象。例如 Object、Function、Boolean、Symbol、Error 等
4. 数字和日期对象，用来表示数字、日期和执行数学计算的对象。例如 Number、Math、Date
5. 字符串，用来表示和操作字符串的对象。例如 String、RegExp
6. 可索引的集合对象，这些对象表示按照索引值来排序的数据集合，包括数组和类型数组，以及类数组结构的对象。例如 Array
7. 使用键的集合对象，这些集合对象在存储数据时会使用到键，支持按照插入顺序来迭代元素。 例如 Map、Set、WeakMap、WeakSet
8. 矢量集合，SIMD 矢量集合中的数据会被组织为一个数据序列。 例如 SIMD 等
9. 结构化数据，这些对象用来表示和操作结构化的缓冲区数据，或使用 JSON 编码的数据。例如 JSON 等
10. 控制抽象对象 例如 Promise、Generator 等
11. 反射。例如 Reflect、Proxy
12. 国际化，为了支持多语言处理而加入 ECMAScript 的对象。例如 Intl、Intl.Collator 等
13. WebAssembly
14. 其他。例如 arguments

总结：js 中的内置对象主要指的是在程序执行前存在全局作用域里的由 js 定义的一些全局值属性、函数和用来实例化其他对象的构造函数对象。一般经常用到的如全局变量值 NaN、undefined，全局函数如 parseInt()、parseFloat() 用来实例化对象的构造函数如 Date、Object 等，还有提供数学计算的单体内置对象如 Math 对象。

## 常用的正则表达式有哪些

```javascript
// 匹配 16 进制颜色值
var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g;

// 匹配日期，如 yyyy-mm-dd 格式
var regex = /^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/;

// 匹配 qq 号
var regex = /^[1-9][0-9]{4,10}$/g;

// 手机号码正则
var regex = /^1[34578]\d{9}$/g;

// 用户名正则
var regex = /^[a-zA-Z\$][a-zA-Z0-9_\$]{4,16}$/;
```

## 对 JSON 的理解

JSON 是一种基于文本的轻量级的数据交换格式。它可以被任何的编程语言读取和作为数据格式来传递。

在项目开发中，使用 JSON 作为前后端数据交换的方式。在前端通过将一个符合 JSON 格式的数据结构序列化为 JSON 字符串，然后将它传递到后端，后端通过 JSON 格式的字符串解析后生成对应的数据结构，以此来实现前后端数据的一个传递。

因为 JSON 的语法是基于 js 的，因此很容易将 JSON 和 js 中的对象弄混，但是应该注意的是 JSON 和 js 中的对象不是一回事，JSON 中对象格式更加严格，比如说在 JSON 中属性值不能为函数，不能出现 NaN 这样的属性值等，因此大多数的 js 对象是不符合 JSON 对象的格式的。

在 js 中提供了两个函数来实现 js 数据结构和 JSON 格式的转换处理，
- JSON.stringify 函数，通过传入一个符合 JSON 格式的数据结构，将其转换为一个 JSON 字符串。如果传入的数据结构不符合 JSON 格式，那么在序列化的时候会对这些值进行对应的特殊处理，使其符合规范。在前端向后端发送数据时，可以调用这个函数将数据对象转化为 JSON 格式的字符串。
- JSON.parse() 函数，这个函数用来将 JSON 格式的字符串转换为一个 js 数据结构，如果传入的字符串不是标准的 JSON 格式的字符串的话，将会抛出错误。当从后端接收到 JSON 格式的字符串时，可以通过这个方法来将其解析为一个 js 数据结构，以此来进行数据的访问。

## JavaScript 脚本延迟加载的方式有哪些

延迟加载就是等页面加载完成之后再加载 JavaScript 文件。 js 延迟加载有助于提高页面加载速度。

一般有以下几种方式：
- defer 属性： 给 js 脚本添加 defer 属性，这个属性会让脚本的加载与文档的解析同步解析，然后在文档解析完成后再执行这个脚本文件，这样的话就能使页面的渲染不被阻塞。多个设置了 defer 属性的脚本按规范来说最后是顺序执行的，但是在一些浏览器中可能不是这样。
- async 属性： 给 js 脚本添加 async 属性，这个属性会使脚本异步加载，不会阻塞页面的解析过程，但是当脚本加载完成后立即执行 js 脚本，这个时候如果文档没有解析完成的话同样会阻塞。多个 async 属性的脚本的执行顺序是不可预测的，一般不会按照代码的顺序依次执行。
- 动态创建 DOM 方式： 动态创建 DOM 标签的方式，可以对文档的加载事件进行监听，当文档加载完成后再动态的创建 script 标签来引入 js 脚本。
- 使用 setTimeout 延迟方法： 设置一个定时器来延迟加载js脚本文件
- 让 JS 最后加载： 将 js 脚本放在文档的底部，来使 js 脚本尽可能的在最后来加载执行。

## JavaScript 类数组对象的定义

一个拥有 length 属性和若干索引属性的对象就可以被称为类数组对象，类数组对象和数组类似，但是不能调用数组的方法。常见的类数组对象有 arguments 和 DOM 方法的返回结果，函数也可以被看作是类数组对象，因为它含有 length 属性值，代表可接收的参数个数。

常见的类数组转换为数组的方法有这样几种：
1. 通过 call 调用数组的 slice 方法来实现转换 `Array.prototype.slice.call(arrayLike);`
2. 通过 call 调用数组的 splice 方法来实现转换 `Array.prototype.splice.call(arrayLike, 0);`
3. 通过 apply 调用数组的 concat 方法来实现转换 `Array.prototype.concat.apply([], arrayLike);`
4. 通过 Array.from 方法来实现转换 `Array.from(arrayLike);`

## 数组有哪些原生方法

- 数组和字符串的转换方法：toString()、toLocalString()、join() 其中 join() 方法可以指定转换为字符串时的分隔符
- 数组尾部操作的方法 pop() 和 push()，push 方法可以传入多个参数
- 数组首部操作的方法 shift() 和 unshift() 重排序的方法 reverse() 和 sort()，sort() 方法可以传入一个函数来进行比较，传入前后两个值，如果返回值为正数，则交换两个参数的位置
- 数组连接的方法 concat() ，返回的是拼接好的数组，不影响原数组
- 数组截取办法 slice()，用于截取数组中的一部分返回，不影响原数组
- 数组插入方法 splice()，影响原数组查找特定项的索引的方法，indexOf() 和 lastIndexOf() 迭代方法 every()、some()、filter()、map() 和 forEach() 方法
- 数组归并方法 reduce() 和 reduceRight() 方法

## Unicode、UTF-8、UTF-16、UTF-32 的区别

- Unicode 是编码字符集（字符集），而 UTF-8、UTF-16、UTF-32 是字符集编码（编码规则）；
- UTF-16 使用变长码元序列的编码方式，相较于定长码元序列的 UTF-32 算法更复杂，甚至比同样是变长码元序列的 UTF-8 也更为复杂，因为其引入了独特的代理对这样的代理机制；
- UTF-8 需要判断每个字节中的开头标志信息，所以如果某个字节在传送过程中出错了，就会导致后面的字节也会解析出错；而 UTF-16 不会判断开头标志，即使错也只会错一个字符，所以容错能力教强；
- 如果字符内容全部英文或英文与其他文字混合，但英文占绝大部分，那么用 UTF-8 就比 UTF-16 节省了很多空间；而如果字符内容全部是中文这样类似的字符或者混合字符中中文占绝大多数，那么 UTF-16 就占优势了，可以节省很多空间；

## 常见的位运算符有哪些？其计算规则是什么

现代计算机中数据都是以二进制的形式存储的，即0、1两种状态，计算机对二进制数据进行的运算加减乘除等都是叫位运算，即将符号位共同参与运算的运算。

| 运算符 | 描述 | 运算规则 |
| ----- | --- | ------- |
| `&` | 与 | 两个位都为 1 时，结果才为 1 |
| `\|` | 或 | 两个位只要有一个为 1，其值为 1 |
| `^` | 异或 | 两个位相同为 0，相异为 1 |
| `~` | 取反 | 0 变 1，1 变 0 |
| `<<` | 左移 | 各二进制位全部左移若干位，高位丢弃，低位补 0 |
| `>>` | 右移 | 各二进制位全部右移若干位，正数左补 0，负数左补 1，右边丢弃 |

原码、补码、反码：
计算机中的有符号数有三种表示方法，即原码、反码和补码。三种表示方法均有符号位和数值位两部分，符号位都是用 0 表示“正”，用 1 表示“负”，而三种方法数值位的表示各不相同。
1. 原码就是一个数的二进制数。例如：10 的原码为 `0000 1010`
2. 反码
   - 正数的反码与原码相同，如：10 反码为 `0000 1010`
   - 负数的反码为除符号位，按位取反，即 0 变 1，1 变 0
3. 补码
   - 正数的补码与原码相同，如：10 补码为 `0000 1010`
   - 负数的补码是原码除符号位外的所有位取反即 0 变 1，1 变 0，然后加 1，也就是反码加 1
```javascript
// 以 -10 为例
原码：1000 1010
反码：1111 0101
补码：1111 0110
```

## 为什么函数的 arguments 参数是类数组而不是数组？如何遍历类数组

arguments 是一个对象，它的属性是从 0 开始依次递增的数字，还有 callee 和 length 等属性，与数组相似；但是它却没有数组常见的方法属性，如 forEach, reduce 等，所以叫它们类数组。

要遍历类数组，有三个方法：
1. 将数组的方法应用到类数组上，这时候就可以使用 call 和 apply 方法，如：`Array.prototype.forEach.call(arguments, a => console.log(a))`
2. 使用 Array.from 方法将类数组转化成数组：‌`const arrArgs = Array.from(arguments); arrArgs.forEach(a => console.log(a));`
3. 使用展开运算符将类数组转化成数组：`const arrArgs = [...arguments]; arrArgs.forEach(a => console.log(a))`

## 什么是 DOM 和 BOM

- DOM 指的是文档对象模型，它指的是把文档当做一个对象，这个对象主要定义了处理网页内容的方法和接口。
- BOM 指的是浏览器对象模型，它指的是把浏览器当做一个对象来对待，这个对象主要定义了与浏览器进行交互的法和接口。BOM 的核心是 window，而 window 对象具有双重角色，它既是通过 js 访问浏览器窗口的一个接口，又是一个 Global（全局）对象。这意味着在网页中定义的任何对象，变量和函数，都作为全局对象的一个属性或者方法存在。window 对象含有 location 对象、navigator 对象、screen 对象等子对象，并且 DOM 的最根本的对象 document 对象也是 BOM 的 window 对象的子对象。

## 对类数组对象的理解，如何转化为数组

一个拥有 length 属性和若干索引属性的对象就可以被称为类数组对象，类数组对象和数组类似，但是不能调用数组的方法。常见的类数组对象有 arguments 和 DOM 方法的返回结果，函数参数也可以被看作是类数组对象，因为它含有 length 属性值，代表可接收的参数个数。

常见的类数组转换为数组的方法有这样几种：
- 通过 call 调用数组的 slice 方法来实现转换 `Array.prototype.slice.call(arrayLike);`
- 通过 call 调用数组的 splice 方法来实现转换 `Array.prototype.splice.call(arrayLike, 0);`
- 通过 apply 调用数组的 concat 方法来实现转换 `Array.prototype.concat.apply([], arrayLike);`
- 通过 Array.from 方法来实现转换 `Array.from(arrayLike);`

## escape、encodeURI、encodeURIComponent 的区别

- encodeURI 是对整个 URI 进行转义，将 URI 中的非法字符转换为合法字符，所以对于一些在 URI 中有特殊意义的字符不会进行转义。
- encodeURIComponent 是对 URI 的组成部分进行转义，所以一些特殊字符也会得到转义。
- escape 和 encodeURI 的作用相同，不过它们对于 unicode 编码为 0xff 之外字符的时候会有区别，escape 是直接在字符的 unicode 编码前加上 %u，而 encodeURI 首先会将字符转换为 UTF-8 的格式，再在每个字节前加上 %。

## 对 AJAX 的理解，实现一个 AJAX 请求

AJAX 是 Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。

创建 AJAX 请求的步骤：
1. 创建一个 XMLHttpRequest 对象。
2. 在这个对象上使用 open 方法创建一个 HTTP 请求，open 方法所需要的参数是请求的方法、请求的地址、是否异步和用户的认证信息。
3. 在发起请求前，可以为这个对象添加一些信息和监听函数。比如说可以通过 setRequestHeader 方法来为请求添加头信息。还可以为这个对象添加一个状态监听函数。一个 XMLHttpRequest 对象一共有 5 个状态，当它的状态变化时会触发onreadystatechange 事件，可以通过设置监听函数，来处理请求成功后的结果。当对象的 readyState 变为 4 的时候，代表服务器返回的数据接收完成，这个时候可以通过判断请求的状态，如果状态是 2xx 或者 304 的话则代表返回正常。这个时候就可以通过 response 中的数据来对页面进行更新了。
4. 当对象的属性和监听函数设置完成后，最后调用 sent 方法来向服务器发起请求，可以传入参数作为发送的数据体。

```javascript
const SERVER_URL = "/server";
let xhr = new XMLHttpRequest();
// 创建 Http 请求
xhr.open("GET", url, true);
// 设置状态监听函数
xhr.onreadystatechange = function() {
  if (this.readyState !== 4) return;
  // 当请求成功时
  if (this.status === 200) {
    handle(this.response);
  } else {
    console.error(this.statusText);
  }
};
// 设置请求失败时的监听函数
xhr.onerror = function() {
  console.error(this.statusText);
};
// 设置请求头信息
xhr.responseType = "json";
xhr.setRequestHeader("Accept", "application/json");
// 发送 Http 请求
xhr.send(null);
```

使用 Promise 封装 AJAX：

```javascript
// promise 封装实现：
function getJSON(url) {
  // 创建一个 promise 对象
  let promise = new Promise(function(resolve, reject) {
    let xhr = new XMLHttpRequest();
    // 新建一个 http 请求
    xhr.open("GET", url, true);
    // 设置状态的监听函数
    xhr.onreadystatechange = function() {
      if (this.readyState !== 4) return;
      // 当请求成功或失败时，改变 promise 的状态
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    // 设置错误监听函数
    xhr.onerror = function() {
      reject(new Error(this.statusText));
    };
    // 设置响应的数据类型
    xhr.responseType = "json";
    // 设置请求头信息
    xhr.setRequestHeader("Accept", "application/json");
    // 发送 http 请求
    xhr.send(null);
  });
  return promise;
}
```

## JavaScript 为什么要进行变量提升，它导致了什么问题

变量提升的表现是，无论在函数中何处位置声明的变量，都被提升到了函数的首部，可以在变量声明前访问到而不会报错。

造成变量声明提升的本质原因是 js 引擎在代码执行前有一个解析的过程，创建了执行上下文，初始化了一些代码执行时需要用到的对象。当访问一个变量时，会到当前执行上下文中的作用域链中去查找，而作用域链的首端指向的是当前执行上下文的变量对象，这个变量对象是执行上下文的一个属性，它包含了函数的形参、所有的函数和变量声明，这个对象的是在代码解析的时候创建的。

JS 在拿到一个变量或者一个函数的时候，会有两步操作，即解析和执行。
- **在解析阶段**，JS会检查语法，并对函数进行预编译。解析的时候会先创建一个全局执行上下文环境，先把代码中即将执行的变量、函数声明都拿出来，变量先赋值为undefined，函数先声明好可使用。在一个函数执行之前，也会创建一个函数执行上下文环境，跟全局执行上下文类似，不过函数执行上下文会多出this、arguments和函数的参数。
  - 全局上下文：变量定义，函数声明
  - 函数上下文：变量定义，函数声明，this，arguments
- **在执行阶段**，就是按照代码的顺序依次执行。

那为什么会进行变量提升呢？主要有以下两个原因：提高性能、容错性更好
1. 提高性能：在JS代码执行之前，会进行语法检查和预编译，并且这一操作只进行一次。这么做就是为了提高性能，如果没有这一步，那么每次执行代码前都必须重新解析一遍该变量（函数），而这是没有必要的，因为变量（函数）的代码并不会改变，解析一遍就够了。在解析的过程中，还会为函数生成预编译代码。在预编译时，会统计声明了哪些变量、创建了哪些函数，并对函数的代码进行压缩，去除注释、不必要的空白等。这样做的好处就是每次执行函数时都可以直接为该函数分配栈空间（不需要再解析一遍去获取代码中声明了哪些变量，创建了哪些函数），并且因为代码压缩的原因，代码执行也更快了。
2. 容错性更好：变量提升可以在一定程度上提高 JS 的容错性，看下面的代码
   ```javascript
   a = 1;var a;console.log(a);
   ```
   如果没有变量提升，这两行代码就会报错，但是因为有了变量提升，这段代码就可以正常执行。
   虽然在开发过程中可以完全避免这样写，但是有时代码很复杂的时候。可能因为疏忽而先使用后定义了，这样也不会影响正常使用。由于变量提升的存在，而会正常运行。

总结：
- 解析和预编译过程中的声明提升可以提高性能，让函数可以在执行时预先为变量分配栈空间
- 声明提升还可以提高JS代码的容错性，使一些不规范的代码也可以正常执行

变量提升虽然有一些优点，但是他也会造成一定的问题，在 ES6 中提出了 let、const 来定义变量，它们就没有变量提升的机制。下面看一下变量提升可能会导致的问题：

```javascript
var tmp = new Date();

function fn(){
    console.log(tmp);
    if(false){
        var tmp = 'hello world';
    }
}

fn();  // undefined
```

在这个函数中，原本是要打印出外层的 tmp 变量，但是因为变量提升的问题，内层定义的 tmp 被提到函数内部的最顶部，相当于覆盖了外层的 tmp，所以打印结果为 undefined。

```javascript
var tmp = 'hello world';

for (var i = 0; i < tmp.length; i++) {
    console.log(tmp[i]);
}

console.log(i); // 11
```

由于遍历时定义的 i 会变量提升成为一个全局变量，在函数结束之后不会被销毁，所以打印出来 11。

## 什么是尾调用，使用尾调用有什么好处

尾调用指的是函数的最后一步调用另一个函数。代码执行是基于执行栈的，所以当在一个函数里调用另一个函数时，会保留当前的执行上下文，然后再新建另外一个执行上下文加入栈中。使用尾调用的话，因为已经是函数的最后一步，所以这时可以不必再保留当前的执行上下文，从而节省了内存，这就是尾调用优化。但是 ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。

## ES6 模块与 CommonJS 模块有什么异同

ES6 Module 和 CommonJS 模块的区别：

- CommonJS 是对模块的浅拷⻉，ES6 Module 是对模块的引⽤，即 ES6 Module 只存只读，不能改变其值，也就是指针指向不能变，类似 const；
- import 的接⼝是 read-only（只读状态），不能修改其变量值。 即不能修改其变量的指针指向，但可以改变变量内部指针指向，可以对 commonJS 重新赋值（改变指针指向），但是对 ES6 Module 赋值会编译报错。

ES6 Module 和 CommonJS 模块的共同点：CommonJS 和 ES6 Module 都可以对引⼊的对象进⾏赋值，即对对象内部属性的值进⾏改变。

## use strict是什么意思? 使用它区别是什么

use strict 是一种 ECMAscript5 添加的（严格模式）运行模式，这种模式使得 Javascript 在更严格的条件下运行。设立严格模式的目的如下：
1. 消除 Javascript 语法的不合理、不严谨之处，减少怪异行为;
2. 消除代码运行的不安全之处，保证代码运行的安全；
3. 提高编译器效率，增加运行速度；
4. 为未来新版本的 Javascript 做好铺垫。

区别：
- 禁止使用 with 语句。
- 禁止 this 关键字指向全局对象。
- 对象不能有重名的属性。

## 如何判断一个对象是否属于某个类

- 第一种方式，使用 instanceof 运算符来判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。
- 第二种方式，通过对象的 constructor 属性来判断，对象的 constructor 属性指向该对象的构造函数，但是这种方式不是很安全，因为 constructor 属性可以被改写。
- 第三种方式，如果需要判断的是某个内置的引用类型的话，可以使用 Object.prototype.toString() 方法来打印对象的 `[[Class]]` 属性来进行判断。

## 强类型语言和弱类型语言的区别

- 强类型语言：强类型语言也称为强类型定义语言，是一种总是强制类型定义的语言，要求变量的使用要严格符合定义，所有变量都必须先定义后使用。Java和C++等语言都是强制类型定义的，也就是说，一旦一个变量被指定了某个数据类型，如果不经过强制转换，那么它就永远是这个数据类型了。例如你有一个整数，如果不显式地进行转换，你不能将其视为一个字符串。
- 弱类型语言：弱类型语言也称为弱类型定义语言，与强类型定义相反。JavaScript语言就属于弱类型语言。简单理解就是一种变量类型可以被忽略的语言。比如JavaScript是弱类型定义的，在JavaScript中就可以将字符串'12'和整数3进行连接得到字符串'123'，在相加的时候会进行强制类型转换。

两者对比：强类型语言在速度上可能略逊色于弱类型语言，但是强类型语言带来的严谨性可以有效地帮助避免许多错误。

## 解释性语言和编译型语言的区别

1. 解释型语言 使用专门的解释器对源程序逐行解释成特定平台的机器码并立即执行。是代码在执行时才被解释器一行行动态翻译和执行，而不是在执行之前就完成翻译。解释型语言不需要事先编译，其直接将源代码解释成机器码并立即执行，所以只要某一平台提供了相应的解释器即可运行该程序。其特点总结如下
   - 解释型语言每次运行都需要将源代码解释称机器码并执行，效率较低；
   - 只要平台提供相应的解释器，就可以运行源代码，所以可以方便源程序移植；
   - JavaScript、Python等属于解释型语言。
2. 编译型语言 使用专门的编译器，针对特定的平台，将高级语言源代码一次性的编译成可被该平台硬件执行的机器码，并包装成该平台所能识别的可执行性程序的格式。在编译型语言写的程序执行之前，需要一个专门的编译过程，把源代码编译成机器语言的文件，如exe格式的文件，以后要再运行时，直接使用编译结果即可，如直接运行exe文件。因为只需编译一次，以后运行时不需要编译，所以编译型语言执行效率高。其特点总结如下：
   - 一次性的编译成平台相关的机器语言文件，运行时脱离开发环境，运行效率高；
   - 与特定平台相关，一般无法移植到其他平台；
   - C、C++等属于编译型语言。
  
两者主要区别在于：前者源程序编译后即可在该平台运行，后者是在运行期间才编译。所以前者运行速度快，后者跨平台性好。

## for...in 和 for...of 的区别

for...of 是 ES6 新增的遍历方式，允许遍历一个含有 iterator 接口的数据结构（数组、对象等）并且返回各项的值，和 ES3中 的 for...in 的区别如下

- for...of 遍历获取的是对象的键值，for...in 获取的是对象的键名；
- for...in 会遍历对象的整个原型链，性能非常差不推荐使用，而 for...of 只遍历当前对象不会遍历原型链；
- 对于数组的遍历，for...in 会返回数组中所有可枚举的属性(包括原型链上可枚举的属性)，for...of 只返回数组的下标对应的属性值；

总结： for...in 循环主要是为了遍历对象而生，不适用于遍历数组；for...of 循环可以用来遍历数组、类数组对象，字符串、Set、Map 以及 Generator 对象。

## 如何使用 for...of 遍历对象

for...of 是作为 ES6 新增的遍历方式，允许遍历一个含有 iterator 接口的数据结构（数组、对象等）并且返回各项的值，普通的对象用 for...of 遍历是会报错的。

如果需要遍历的对象是类数组对象，用 Array.from 转成数组即可。

```javascript
var obj = {
    0:'one',
    1:'two',
    length: 2
};
obj = Array.from(obj);
for(var k of obj){
    console.log(k)
}
```

如果不是类数组对象，就给对象添加一个 `[Symbol.iterator]` 属性，并指向一个迭代器即可。

```javascript
//方法一：
var obj = {
    a:1,
    b:2,
    c:3
};

obj[Symbol.iterator] = function(){
    var keys = Object.keys(this);
    var count = 0;
    return {
        next(){
            if(count<keys.length){
                return {value: obj[keys[count++]],done:false};
            }else{
                return {value:undefined,done:true};
            }
        }
    }
};

for(var k of obj){
    console.log(k);
}


// 方法二
var obj = {
    a:1,
    b:2,
    c:3
};
obj[Symbol.iterator] = function*(){
    var keys = Object.keys(obj);
    for(var k of keys){
        yield [k,obj[k]]
    }
};

for(var [k,v] of obj){
    console.log(k,v);
}
```

## ajax、axios、fetch 的区别

1. AJAX Ajax 即“AsynchronousJavascriptAndXML”（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发技术。它是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。其缺点如下：
   - 本身是针对 MVC 编程，不符合前端 MVVM 的浪潮
   - 基于原生 XHR 开发，XHR 本身的架构不清晰
   - 不符合关注分离（Separation of Concerns）的原则
   - 配置和调用方式非常混乱，而且基于事件的异步模型不友好。
2. Fetch fetch 号称是 AJAX 的替代品，是在 ES6 出现的，使用了 ES6 中的 promise 对象。Fetch 是基于 promise 设计的。Fetch 的代码结构比起 ajax 简单很多。fetch 不是 ajax 的进一步封装，而是原生 js，没有使用 XMLHttpRequest 对象。
   - fetch 的优点：
      - 语法简洁，更加语义化
      - 基于标准 Promise 实现，支持 async/await
      - 更加底层，提供的 API 丰富（request, response）
      - 脱离了 XHR，是 ES 规范里新的实现方式
   - fetch 的缺点：
      - fetch 只对网络请求报错，对 400，500 都当做成功的请求，服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。
      - fetch 默认不会带 cookie，需要添加配置项： fetch(url, {credentials: 'include'})
      - fetch 不支持 abort，不支持超时控制，使用 setTimeout 及 Promise.reject 的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
      - fetch 没有办法原生监测请求的进度，而 XHR 可以
3. Axios Axios 是一种基于 Promise 封装的 HTTP 客户端，其特点如下：
   - 浏览器端发起 XMLHttpRequests 请求
   - node 端发起 http 请求
   - 支持 Promise API
   - 监听请求和返回
   - 对请求和返回进行转化
   - 取消请求
   - 自动转换 json 数据
   - 客户端支持抵御 XSRF 攻击

## forEach 和 map 方法有什么区别

这方法都是用来遍历数组的，两者区别如下：
- forEach()方法会针对每一个元素执行提供的函数，对数据的操作会改变原数组，该方法没有返回值；
- map()方法不会改变原数组的值，返回一个新数组，新数组中的值为原数组调用函数处理之后的值；

# 原型与原型链

## 对原型、原型链的理解

在 JavaScript 中是使用构造函数来新建一个对象的，每一个构造函数的内部都有一个 prototype 属性，它的属性值是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。当使用构造函数新建一个对象后，在这个对象的内部将包含一个指针，这个指针指向构造函数的 prototype 属性对应的值，在 ES5 中这个指针被称为对象的原型。一般来说不应该能够获取到这个值的，但是现在浏览器中都实现了 proto 属性来访问这个属性，但是最好不要使用这个属性，因为它不是规范中规定的。ES5 中新增了一个 Object.getPrototypeOf() 方法，可以通过这个方法来获取对象的原型。

当访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 Object.prototype 所以这就是新建的对象为什么能够使用 toString() 等方法的原因。

特点： JavaScript 对象是通过引用来传递的，创建的每个新对象实体中并没有一份属于自己的原型副本。当修改原型时，与之相关的对象也会继承这一改变。 

## 原型修改、重写

```javascript
function Person(name) {
    this.name = name
}
// 修改原型
Person.prototype.getName = function() {}
var p = new Person('hello')
console.log(p.__proto__ === Person.prototype) // true
console.log(p.__proto__ === p.constructor.prototype) // true
// 重写原型
Person.prototype = {
    getName: function() {}
}
var p = new Person('hello')
console.log(p.__proto__ === Person.prototype)        // true
console.log(p.__proto__ === p.constructor.prototype) // false
```

可以看到修改原型的时候 p 的构造函数不是指向 Person 了，因为直接给 Person 的原型对象直接用对象赋值时，它的构造函数指向的了根构造函数 Object，所以这时候 p.constructor === Object ，而不是 p.constructor === Person。要想成立，就要用 constructor 指回来：

```javascript
Person.prototype = {
    getName: function() {}
}
var p = new Person('hello')
p.constructor = Person
console.log(p.__proto__ === Person.prototype)        // true
console.log(p.__proto__ === p.constructor.prototype) // true
```

## 原型链指向

```javascript
p.__proto__  // Person.prototype
Person.prototype.__proto__  // Object.prototype
p.__proto__.__proto__ //Object.prototype
p.__proto__.constructor.prototype.__proto__ // Object.prototype
Person.prototype.constructor.prototype.__proto__ // Object.prototype
p1.__proto__.constructor // Person
Person.prototype.constructor  // Person
```

## 原型链的终点是什么？如何打印出原型链的终点

由于 Object 是构造函数，原型链终点是 `Object.prototype.__proto__`，而 `Object.prototype.__proto__=== null // true`，所以，原型链的终点是 null。原型链上的所有原型都是对象，所有的对象最终都是由 Object 构造的，而 Object.prototype 的下一级是 `Object.prototype.__proto__`。 

## 如何获得对象非原型链上的属性

使用后 `hasOwnProperty()` 方法来判断属性是否属于原型链的属性：

```javascript
function iterate(obj){
   var res=[];
   for(var key in obj){
        if(obj.hasOwnProperty(key))
           res.push(key+': '+obj[key]);
   }
   return res;
}
```

# 执行上下文/作用域链/闭包

## 对闭包的理解

闭包是指有权访问另一个函数作用域中变量的函数，创建闭包的最常见的方式就是在一个函数内创建另一个函数，创建的函数可以访问到当前函数的局部变量。

闭包有两个常用的用途；
- 闭包的第一个用途是使我们在函数外部能够访问到函数内部的变量。通过使用闭包，可以通过在外部调用闭包函数，从而在外部访问到函数内部的变量，可以使用这种方法来创建私有变量。
- 闭包的另一个用途是使已经运行结束的函数上下文中的变量对象继续留在内存中，因为闭包函数保留了这个变量对象的引用，所以这个变量对象不会被回收。

比如，函数 A 内部有一个函数 B，函数 B 可以访问到函数 A 中的变量，那么函数 B 就是闭包。

```javascript
function A() {
  let a = 1
  window.B = function () {
      console.log(a)
  }
}
A()
B() // 1
```

在 JS 中，闭包存在的意义就是让我们可以间接访问函数内部的变量。经典面试题：循环中使用闭包解决 var 定义函数的问题

```javascript
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```

首先因为 setTimeout 是个异步函数，所以会先把循环全部执行完毕，这时候 i 就是 6 了，所以会输出一堆 6。解决办法有三种：
1. 第一种是使用闭包的方式
   ```javascript
   for (var i = 1; i <= 5; i++) {
     (function (j) {
       setTimeout(function timer() {
         console.log(j);
       }, j * 1000);
     })(i);
   }
   ```
   在上述代码中，首先使用了立即执行函数将 i 传入函数内部，这个时候值就被固定在了参数 j 上面不会改变，当下次执行 timer 这个闭包的时候，就可以使用外部函数的变量 j，从而达到目的。
2. 第二种就是使用 setTimeout 的第三个参数，这个参数会被当成 timer 函数的参数传入
   ```javascript
   for (var i = 1; i <= 5; i++) {
     setTimeout(
       function timer(j) {
         console.log(j)
       },
       i * 1000,
       i
     )
   }
   ```
3. 第三种就是使用 let 定义 i 了来解决问题了，这个也是最为推荐的方式
   ```javascript
   for (let i = 1; i <= 5; i++) {
     setTimeout(function timer() {
       console.log(i)
     }, i * 1000)
   }
   ```

## 对作用域、作用域链的理解

1. 全局作用域和函数作用域
   - 全局作用域
     - 最外层函数和最外层函数外面定义的变量拥有全局作用域
     - 所有未定义直接赋值的变量自动声明为全局作用域
     - 所有 window 对象的属性拥有全局作用域
     - 全局作用域有很大的弊端，过多的全局作用域变量会污染全局命名空间，容易引起命名冲突。
   - 函数作用域
     - 函数作用域声明在函数内部的变零，一般只有固定的代码片段可以访问到
     - 作用域是分层的，内层作用域可以访问外层作用域，反之不行
2. 块级作用域
   - 使用 ES6 中新增的 let 和 const 指令可以声明块级作用域，块级作用域可以在函数中创建也可以在一个代码块中的创建（由 `{ }` 包裹的代码片段）
   - let 和 const 声明的变量不会有变量提升，也不可以重复声明
   - 在循环中比较适合绑定块级作用域，这样就可以把声明的计数器变量限制在循环内部。

作用域链： 在当前作用域中查找所需变量，但是该作用域没有这个变量，那这个变量就是自由变量。如果在自己作用域找不到该变量就去父级作用域查找，依次向上级作用域查找，直到访问到 window 对象就被终止，这一层层的关系就是作用域链。

作用域链的作用是保证对执行环境有权访问的所有变量和函数的有序访问，通过作用域链，可以访问到外层环境的变量和函数。

作用域链的本质上是一个指向变量对象的指针列表。变量对象是一个包含了执行环境中所有变量和函数的对象。作用域链的前端始终都是当前执行上下文的变量对象。全局执行上下文的变量对象（也就是全局对象）始终是作用域链的最后一个对象。

当查找一个变量时，如果当前执行环境中没有找到，可以沿着作用域链向后查找。

## 对执行上下文的理解

1. 执行上下文类型
   - 全局执行上下文：任何不在函数内部的都是全局执行上下文，它首先会创建一个全局的 window 对象，并且设置 this 的值等于这个全局对象，一个程序中只有一个全局执行上下文。
   - 函数执行上下文：当一个函数被调用时，就会为该函数创建一个新的执行上下文，函数的上下文可以有任意多个。
   - eval 函数执行上下文：执行在 eval 函数中的代码会有属于他自己的执行上下文，不过 eval 函数不常使用，不做介绍。
2. 执行上下文栈
   - JavaScript 引擎使用执行上下文栈来管理执行上下文
   - 当 JavaScript 执行代码时，首先遇到全局代码，会创建一个全局执行上下文并且压入执行栈中，每当遇到一个函数调用，就会为该函数创建一个新的执行上下文并压入栈顶，引擎会执行位于执行上下文栈顶的函数，当函数执行完成之后，执行上下文从栈中弹出，继续执行下一个上下文。当所有的代码都执行完毕之后，从栈中弹出全局执行上下文。
3. 创建执行上下文，创建执行上下文有两个阶段：创建阶段和执行阶段
   - 创建阶段
     - this 绑定
       - 在全局执行上下文中，this 指向全局对象（window对象）
       - 在函数执行上下文中，this 指向取决于函数如何调用。如果它被一个引用对象调用，那么 this 会被设置成那个对象，否则 this 的值被设置为全局对象或者 undefined
     - 创建词法环境组件
       - 词法环境是一种有标识符——变量映射的数据结构，标识符是指变量/函数名，变量是对实际对象或原始数据的引用。
       - 词法环境的内部有两个组件：**环境记录器**：用来储存环境中存在的变量和函数声明的实际位置；**外部环境的引用**：这个引用指向了外部的词法环境，可以访问父级作用域
     - 创建变量环境组件，变量环境也是一个词法环境，其环境记录器持有变量声明语句在执行上下文中创建的绑定关系
   - 执行阶段，此阶段会完成对变量的分配，最后执行完代码 

简单来说执行上下文就是指：在执行一点JS代码之前，需要先解析代码。解析的时候会先创建一个全局执行上下文环境，先把代码中即将执行的变量、函数声明都拿出来，变量先赋值为 undefined，函数先声明好可使用。这一步执行完了，才开始正式的执行程序。

在一个函数执行之前，也会创建一个函数执行上下文环境，跟全局执行上下文类似，不过函数执行上下文会多出 this、arguments 和函数的参数
- 全局上下文：变量定义，函数声明
- 函数上下文：变量定义，函数声明，this，arguments














 






