# 手写题相关复习

## 实现同步调用队列

```javascript
/**
 * -------------------------------------------------------- 题目
 * 实现代码，要求同时满足下列情况
 *
 * 运行 task("测试") 时，输出：
 * > 开始 测试
 *
 * 运行 task("测试").sleep(5).do("跑步") 时，输出：
 * > 开始 测试
 * 等待 5 秒
 * > 进行 跑步
 *
 * 运行 task("测试").sleepFirst(6).do("跑步") 时，输出：
 * 等待 6 秒
 * > 开始 测试
 * > 进行 跑步
 * --------------------------------------------------------
 */
/**
 * -------------------------------------------------------- 解析
 * 观察题目的三次运行，
 * 由第二次运行可以确认需要在函数内部维护一个同步执行队列，让函数的调用和执行分离
 * 由第三次运行可以确认调用可能存在 “插队” 的情况，需要用到浏览器的事件循环机制来实现这个效果，使得所有调用之后再开始执行队列
 * --------------------------------------------------------
 */

function task(name = null) {
  const callbacks = []; // 维护一个同步执行队列
  callbacks.push(() => console.log(`> 开始 ${name}`)); // 先放入 task 函数自身需要执行的任务
  // 维护一个上下文，其中的函数都是向同步队列放入当前函数需要执行的任务，如果是延时任务，注意用 Promise，函数执行后要返回当前上下文
  const context = {
    sleep(s) {
      callbacks.push(() => {
        return new Promise((resolve) => {
          setTimeout(() => {
            console.log(`等待 ${s} 秒`);
            resolve();
          }, s * 1000);
        });
      });
      return this;
    },
    do(project) {
      callbacks.push(() => {
        console.log(`进行 ${project}`);
      });
      return this;
    },
    sleepFirst(s) {
      callbacks.unshift(() => {
        return new Promise((resolve) => {
          setTimeout(() => {
            console.log(`等待 ${s} 秒`);
            resolve();
          }, s * 1000);
        });
      });
      return this;
    },
  };
  // 同步队列执行逻辑
  async function flushCallbacks() {
    if (callbacks.length > 0) {
      let start = callbacks.shift();
      await start();
      flushCallbacks();
    }
  }
  // 注意这里的同步队列执行逻辑需要放到下一次宏任务里，为函数调用入队列让出执行时间
  setTimeout(flushCallbacks, 0);
  // 返回维护的上下文
  return context;
}
```







