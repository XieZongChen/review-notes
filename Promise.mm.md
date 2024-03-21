# Promise 相关复习

## 对 Promise 的理解

Promise 是异步编程的一种解决方案，它是一个对象，可以获取异步操作的消息，他的出现大大改善了异步编程的困境，避免了地狱回调，它比传统的解决方案回调函数和事件更合理和更强大。

所谓 Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。

- Promise 的实例有三个状态:
  - Pending（进行中）
  - Resolved（已完成）
  - Rejected（已拒绝）

当把一件事情交给 promise 时，它的状态就是 Pending，任务完成了状态就变成了 Resolved、没有完成失败了就变成了 Rejected。

- Promise 的实例有两个过程：
  - pending -> fulfilled : Resolved（已完成）
  - pending -> rejected：Rejected（已拒绝）

注意：一旦从进行状态变成为其他状态就永远不能更改状态了。

Promise 的特点：
- 对象的状态不受外界影响。promise 对象代表一个异步操作，有三种状态，pending（进行中）、fulfilled（已成功）、rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态，这也是 promise 这个名字的由来——“承诺”；
- **一旦状态改变就不会再变，任何时候都可以得到这个结果。promise 对象的状态改变，只有两种可能：从 pending 变为 fulfilled，从 pending 变为 rejected。这时就称为 resolved（已定型）**。如果改变已经发生了，你再对 promise 对象添加回调函数，也会立即得到这个结果。这与事件（event）完全不同，事件的特点是：如果你错过了它，再去监听是得不到结果的。

Promise的缺点：
- 无法取消 Promise，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。
- 当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

总结： Promise 对象是异步编程的一种解决方案，最早由社区提出。Promise 是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个 Promise 实例有三种状态，分别是 pending、resolved 和 rejected，分别代表了进行中、已成功和已失败。实例的状态只能由 pending 转变 resolved 或者 rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。

状态的改变是通过 resolve() 和 reject() 函数来实现的，可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。

注意： 在构造 Promise 的时候，构造函数内部的代码是立即执行的

## 实现一个符合规范的 Promise

```javascript
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';
const isInBrowser = typeof window !== 'undefined';
class Promise {
    static isPromise(val) {
        return val && val instanceof Promise;
    }

    static resolve(val) {
        if (Promise.isPromise(val)) {
            return val;
        }
        return new Promise((resolve) => {
            if (val && typeof val.then === 'function') { // 如果返回值是个thenable对象，需要处理下
                val.then((res) => {
                    resolve(res);
                });
                return;
            }
            resolve(val);
        });
    }

    static reject(val) {
        //reject不区分是不是Promise
        return new Promise((_, reject) => {
            reject(val);
        });
    }

    /**
     * 用MutationObserver生成浏览器的nextTick，nodejs端则用process.nextTick
     */
    static nextTick(nextTickHandler) {
        if (isInBrowser) {
            if (typeof MutationObserver !== 'undefined') { // 首选 MutationObserver 
                var counter = 1;
                var observer = new MutationObserver(nextTickHandler); // 声明 MO 和回调函数
                var textNode = document.createTextNode(counter);
                observer.observe(textNode, { // 监听 textNode 这个文本节点
                    characterData: true // 一旦文本改变则触发回调函数 nextTickHandler
                });
                const start = function () {
                    counter = (counter + 1) % 2; // 每次执行 timeFunc 都会让文本在 1 和 0 间切换
                    textNode.data = counter;
                };
                start();
            } else {
                setTimeout(nextTickHandler, 0);
            }
        } else {
            process.nextTick(nextTickHandler);
        }
    }

    static all(arr) {
        if (!Array.isArray(arr)) {
            throw new TypeError('undefined is not iterable.');
        }
        let count = arr.length;
        const result = [];
        if (count === 0) {
            return Promise.resolve(result);
        }
        return new Promise((resolve, reject) => {
            Promise.resolve(promise).then((res) => {
                count--;
                result[i] = res;
                if (count === 0) {
                    resolve(result);
                }
            }, reject);
        });
    }

    static allSettled(arr) {
        if (!Array.isArray(arr)) {
            throw new TypeError('undefined is not iterable.');
        }
        let count = arr.length;
        const result = [];
        if (count === 0) {
            return Promise.resolve(result);
        }
        return new Promise((resolve) => {
            arr.forEach((promise, i) => {
                Promise.resolve(promise).then((res) => {
                    count--;
                    result[i] = {
                        value: res,
                        status: FULFILLED
                    };
                    if (count === 0) {
                        resolve(result);
                    }
                }, (err) => {
                    count--;
                    result[i] = {
                        reason: err,
                        status: REJECTED
                    };
                    if (count === 0) {
                        resolve(result);
                    }
                });
            });
        });
    }

    static race(arr) {
        if (!Array.isArray(arr)) {
            throw new TypeError('undefined is not iterable.');
        }
        let count = arr.length;
        if (count === 0) {
            return new Promise(() => { }); //返回一个永远pending的promise，这是跟all等不一样的地方
        }
        return new Promise((resolve, reject) => {
            arr.forEach((promise) => {
                Promise.resolve(promise).then(resolve, reject);
            });
        });
    }

    static deferred() {
        let result = {};
        result.promise = new Promise((resolve, reject) => {
            result.resolve = resolve;
            result.reject = reject;
        });
        return result;
    }

    /**
     * 先执行同步代码，替代Promise.resolve().then(func)
     * @example 
     * 
     * const f = () => console.log('now');
     * Promise.try(f);
     * console.log('next');
     */
    static try(func){
        return new Promise((resolve) => {
            resolve(func());
        });
    }

    constructor(fn) {
        this.status = PENDING;
        this.value = undefined;
        this.reason = undefined;

        this.onFulfilledList = [];
        this.onRejectedList = [];
        try {
            fn(this.handleResolve.bind(this), this.handleReject.bind(this));
        } catch (e) {
            this.handleReject(e);
        }
    }

    /**
     * 处理成功
     * 就是我们使用的resolve函数
     * @param val 成功参数
     */
    handleResolve(val) {
        if (this.status !== PENDING) {
            return;
        }
        this.status = FULFILLED;
        this.value = val;
        this.onFulfilledList.forEach((cb) => cb && cb.call(this, val));
        this.onFulfilledList = [];
    }

    /**
     * 处理失败
     * 就是我们使用的reject函数
     * @param err 失败信息
     */
    handleReject(err) {
        if (this.status !== PENDING) {
            return;
        }
        this.status = REJECTED;
        this.reason = err;
        this.onRejectedList.forEach((cb) => cb && cb.call(this, err));
        this.onRejectedList = [];
    }

    then(onFulfilled, onRejected) {
        const promise2 = new Promise((resolve, reject) => {
            const resolvePromise = function (x) {
                if (x === promise2) {
                    reject(new TypeError('The promise and the return value are the same'));
                    return;
                }
                if (x && typeof x === 'object' || typeof x === 'function') {
                    let used; //PromiseA+2.3.3.3.3 只能调用一次
                    try {
                        let then = x.then;
                        if (typeof then === 'function') {
                            //PromiseA+2.3.3
                            then.call(x, (y) => {
                                //PromiseA+2.3.3.1
                                if (used) return;
                                used = true;
                                resolvePromise(y);
                            }, (r) => {
                                //PromiseA+2.3.3.2
                                if (used) return;
                                used = true;
                                reject(r);
                            });
                        } else {
                            //PromiseA+2.3.3.4
                            if (used) return;
                            used = true;
                            resolve(x);
                        }
                    } catch (e) {
                        //PromiseA+ 2.3.3.2
                        if (used) return;
                        used = true;
                        reject(e);
                    }
                } else {
                    //PromiseA+ 2.3.3.4
                    resolve(x);
                }
            };

            const onResolvedFunc = function (val) {
                var cb = function () {
                    try {
                        if (typeof onFulfilled !== 'function') { // 如果成功了，它不是个函数，意味着不能处理，则把当前Promise的状态继续向后传递
                            resolve(val);
                            return;
                        }
                        const x = onFulfilled(val);
                        resolvePromise(x);
                    } catch (e) {
                        reject(e);
                    }
                };
                Promise.nextTick(cb);
            };

            const onRejectedFunc = function (val) {
                var cb = function () {
                    try {
                        if (typeof onRejected !== 'function') { // 如果失败了，它不是个函数，意味着不能处理，则把当前Promise的状态继续向后传递
                            reject(val);
                            return;
                        }
                        const x = onRejected(val);
                        resolvePromise(x);
                    } catch (e) {
                        reject(e);
                    }
                };
                Promise.nextTick(cb);
            };

            if (this.status === PENDING) {
                //这样把then注册的函数，放到list中延时执行。内部加了try/catch，把修改状态的逻辑全放在了handleResolve、handleReject这俩函数中
                this.onFulfilledList.push(onResolvedFunc);
                this.onRejectedList.push(onRejectedFunc);
            } else if (this.status === FULFILLED) { //如果这个Promise已经成功，说明已经resolve过了，不能再依赖resolve来触发，就直接执行成功处理。比如aa = Promise.resolve()，有多处使用.then
                onResolvedFunc(this.value);
            } else { // if(this.status === REJECTED) { //如果这个Promise已经失败，说明已经reject过了，不能再依赖reject来触发，就直接执行失败处理。
                onRejectedFunc(this.reason);
            }
        });

        return promise2;
    }

    catch(onRejected) {
        return this.then(null, onRejected);
    }

    finally(callback) {
        return this.then(
            (val) => Promise.resolve(callback()).then(() => val),
            (err) => Promise.resolve(callback()).then(() => { throw err })
        );
    }

    done(onFulfilled, onRejected) {
        this.then(onFulfilled, onRejected)
            .catch(function (reason) {
                // 抛出一个全局错误
                setTimeout(() => {
                    throw reason
                }, 0);
            });
    }

    toString() {
        return '[object Promise]';
    }
}

module.exports = Promise;
```







