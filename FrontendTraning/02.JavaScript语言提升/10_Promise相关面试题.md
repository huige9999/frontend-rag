# 10. Promise相关面试题

## 章节概述

本章节将深入探讨Promise相关的常见面试题，涵盖Promise的基本概念、链式调用规则、事件循环机制以及async/await的使用。通过实际的代码示例和详细的输出分析，帮助开发者掌握Promise在面试中的高频考点。

## Promise的基本概念

Promise是JavaScript中用于处理异步操作的对象，它有三种状态：

- **pending**（进行中）：初始状态，既没有被兑现，也没有被拒绝
- **fulfilled**（已成功）：操作成功完成
- **rejected**（已失败）：操作失败

Promise状态一旦改变就不会再变，只能从pending变为fulfilled或从pending变为rejected。

## 链式调用规则

### 1. then方法必定会返回一个新的Promise

可以理解为"后续处理也是一个任务"

### 2. 新任务的状态取决于后续处理

- 若没有相关的后续处理，新任务的状态和前任务一致，数据为前任务的数据
- 若有后续处理但还未执行，新任务挂起
- 若后续处理执行了，则根据后续处理的情况确定新任务的状态：
  - 后续处理执行无错，新任务的状态为完成，数据为后续处理的返回值
  - 后续处理执行有错，新任务的状态为失败，数据为异常对象
  - 后续执行后返回的是一个任务对象，新任务的状态和数据与该任务对象一致

## Promise的静态方法

| 方法名 | 含义 |
|--------|------|
| Promise.resolve(data) | 直接返回一个完成状态的任务 |
| Promise.reject(reason) | 直接返回一个拒绝状态的任务 |
| Promise.all(任务数组) | 返回一个任务，任务数组全部成功则成功，任何一个失败则失败 |
| Promise.any(任务数组) | 返回一个任务，任务数组任一成功则成功，任务全部失败则失败 |
| Promise.allSettled(任务数组) | 返回一个任务，任务数组全部已决则成功，该任务不会失败 |
| Promise.race(任务数组) | 返回一个任务，任务数组任一已决则已决，状态和其一致 |

## async和await

### async关键字

async关键字用于修饰函数，被它修饰的函数，一定返回Promise。

```javascript
async function method1() {
  return 1; // 该函数的返回值是Promise完成后的数据
}

method1(); // Promise { 1 }

async function method2() {
  return Promise.resolve(1); // 若返回的是Promise，则method得到的Promise状态和其一致
}

method2(); // Promise { 1 }

async function method3() {
  throw new Error(1); // 若执行过程报错，则任务是rejected
}

method3(); // Promise { <rejected> Error(1) }
```

### await关键字

`await`关键字表示等待某个Promise完成，**它必须用于`async`函数中**。

```javascript
async function method() {
  const n = await Promise.resolve(1);
  console.log(n); // 1
}

// 上面的函数等同于
function method() {
  return new Promise((resolve, reject) => {
    Promise.resolve(1).then((n) => {
      console.log(n);
      resolve(1);
    });
  });
}
```

`await`也可以等待其他数据：

```javascript
async function method() {
  const n = await 1; // 等同于 await Promise.resolve(1)
}
```

如果需要针对失败的任务进行处理，可以使用`try-catch`语法：

```javascript
async function method() {
  try {
    const n = await Promise.reject(123); // 这句代码将抛出异常
    console.log('成功', n);
  } catch (err) {
    console.log('失败', err);
  }
}

method(); // 输出： 失败 123
```

## 事件循环

根据目前所学，进入事件队列的函数有以下几种：

- `setTimeout`的回调，宏任务（macro task）
- `setInterval`的回调，宏任务（macro task）
- Promise的`then`函数回调，**微任务**（micro task）
- `requestAnimationFrame`的回调，宏任务（macro task）
- 事件处理函数，宏任务（macro task）

---

## 面试题详解

### 面试题 1

下面代码的输出结果是什么？

```javascript
const promise = new Promise((resolve, reject) => {
  console.log(1);
  resolve();
  console.log(2);
});

promise.then(() => {
  console.log(3);
});

console.log(4);
```

**输出结果：**
```
1
2
4
3
```

**解析：**
1. Promise构造函数是同步执行的，所以先输出1和2
2. resolve()被调用，Promise状态变为fulfilled
3. then方法注册的回调函数进入微任务队列
4. 继续执行同步代码，输出4
5. 同步代码执行完毕后，执行微任务队列中的回调，输出3

### 面试题 2

下面代码的输出结果是什么？

```javascript
setTimeout(() => {
  console.log(1);
});

const promise = new Promise((resolve, reject) => {
  console.log(2);
  resolve();
});

promise.then(() => {
  console.log(3);
});

console.log(4);
```

**输出结果：**
```
2
4
3
1
```

**解析：**
1. setTimeout的回调进入宏任务队列
2. Promise构造函数同步执行，输出2
3. resolve()被调用，then回调进入微任务队列
4. 继续执行同步代码，输出4
5. 同步代码执行完毕后，先执行微任务队列，输出3
6. 微任务队列清空后，执行宏任务队列，输出1

### 面试题 3

下面代码的输出结果是什么？

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject();
  }, 1000);
});

const promise2 = promise1.catch(() => {
  return 2;
});

console.log('promise1', promise1);
console.log('promise2', promise2);

setTimeout(() => {
  console.log('promise1', promise1);
  console.log('promise2', promise2);
}, 2000);
```

**输出结果：**
```
promise1 Promise { <pending> }
promise2 Promise { <pending> }
promise1 Promise { <rejected> }
promise2 Promise { 2 }
```

**解析：**
1. promise1内部的setTimeout会在1秒后reject
2. promise2是promise1.catch()的结果，catch返回新的Promise
3. 初始时两个Promise都是pending状态
4. 1秒后promise1变为rejected状态
5. catch捕获错误，promise2变为fulfilled状态，结果为2
6. 2秒后输出两个Promise的最终状态

### 面试题 4

下面代码的输出结果是什么？

```javascript
async function m() {
  console.log(0);
  const n = await 1;
  console.log(n);
}

m();
console.log(2);
```

**输出结果：**
```
0
2
1
```

**解析：**
1. 调用m()函数，async函数立即执行，输出0
2. 遇到await 1，await后面的代码被包装成微任务
3. 继续执行主线程的同步代码，输出2
4. 同步代码执行完毕后，执行微任务队列，输出1

### 面试题 5

下面代码的输出结果是什么？

```javascript
async function m() {
  console.log(0);
  const n = await 1;
  console.log(n);
}

(async () => {
  await m();
  console.log(2);
})();

console.log(3);
```

**输出结果：**
```
0
3
1
2
```

**解析：**
1. 立即执行函数表达式(IIFE)开始执行
2. 调用m()函数，输出0
3. m()函数中await 1，m()函数暂停执行
4. 外部的await m()也会暂停
5. 继续执行主线程同步代码，输出3
6. 同步代码执行完毕后，执行微任务队列
7. m()函数恢复执行，输出1
8. m()函数完成，外部await恢复，输出2

### 面试题 6

下面代码的输出结果是什么？

```javascript
async function m1() {
  return 1;
}

async function m2() {
  const n = await m1();
  console.log(n);
  return 2;
}

async function m3() {
  const n = m2();
  console.log(n);
  return 3;
}

m3().then((n) => {
  console.log(n);
});

m3();

console.log(4);
```

**输出结果：**
```
Promise { <pending> }
Promise { <pending> }
4
1
3
2
1
3
```

**解析：**
1. 第一次调用m3()，m2()被调用但没有await，返回Promise对象
2. 输出第一个Promise { <pending> }
3. 第二次调用m3()，同样返回Promise对象
4. 输出第二个Promise { <pending> }
5. 继续执行同步代码，输出4
6. 同步代码执行完毕后，开始执行微任务队列
7. 第一个m3()的Promise链执行，m2()中的await生效，输出1
8. m2()返回2，m3()返回3，输出3
9. then回调执行，输出2
10. 第二个m3()的Promise链执行，输出1和3

### 面试题 7

下面代码的输出结果是什么？

```javascript
Promise.resolve(1).then(2).then(Promise.resolve(3)).then(console.log);
```

**输出结果：**
```
1
```

**解析：**
1. Promise.resolve(1)返回一个fulfilled的Promise，值为1
2. then(2)：2不是函数，被忽略，Promise状态和值传递到下一个then
3. then(Promise.resolve(3))：Promise.resolve(3)不是函数，被忽略
4. then(console.log)：console.log是函数，接收传入的值1，输出1

### 面试题 8

下面代码的输出结果是什么？

```javascript
var a;
var b = new Promise((resolve, reject) => {
  console.log('promise1');
  setTimeout(() => {
    resolve();
  }, 1000);
})
  .then(() => {
    console.log('promise2');
  })
  .then(() => {
    console.log('promise3');
  })
  .then(() => {
    console.log('promise4');
  });

a = new Promise(async (resolve, reject) => {
  console.log(a);
  await b;
  console.log(a);
  console.log('after1');
  await a;
  resolve(true);
  console.log('after2');
});

console.log('end');
```

**输出结果：**
```
promise1
Promise { <pending> }
end
promise2
promise3
promise4
Promise { <pending> }
after1
```

**解析：**
这是一个复杂的Promise链式调用和async/await结合的例子：

1. 同步执行：创建Promise b，输出'promise1'，b的then链被注册
2. 创建Promise a，输出a本身（还是pending状态）
3. 输出'end'
4. 1秒后b的setTimeout触发resolve
5. b的then链依次执行，输出'promise2'、'promise3'、'promise4'
6. a中的await b完成，输出a（此时仍是pending）
7. 输出'after1'
8. await a会造成死锁，因为a需要等待自己完成，所以'after2'永远不会输出

### 面试题 9

下面代码的输出结果是什么？

```javascript
async function async1() {
  console.log('async1 start');
  await async2();
  console.log('async1 end');
}

async function async2() {
  console.log('async2');
}

console.log('script start');

setTimeout(function () {
  console.log('setTimeout');
}, 0);

async1();

new Promise(function (resolve) {
  console.log('promise1');
  resolve();
}).then(function () {
  console.log('promise2');
});

console.log('script end');
```

**输出结果：**
```
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```

**解析：**
这是一个经典的事件循环面试题：

1. 输出'script start'
2. setTimeout回调进入宏任务队列
3. 调用async1()，输出'async1 start'
4. await async2()，async2()是async函数，立即执行，输出'async2'
5. async2返回的Promise被await，async1的剩余部分进入微任务队列
6. 创建新Promise，同步执行，输出'promise1'
7. resolve()被调用，then回调进入微任务队列
8. 输出'script end'
9. 同步代码执行完毕，执行微任务队列：
   - 先执行async1的剩余部分，输出'async1 end'
   - 再执行Promise的then回调，输出'promise2'
10. 微任务队列清空，执行宏任务队列，输出'setTimeout'

---

## 知识点总结

### Promise执行顺序
1. Promise构造函数的代码是同步执行的
2. then/catch方法的回调是异步的，进入微任务队列
3. 微任务队列的优先级高于宏任务队列

### async/await执行顺序
1. async函数调用会立即执行同步部分
2. await表达式会暂停函数执行，将剩余部分包装成微任务
3. await后面的表达式如果是Promise，会等待其完成

### 事件循环执行顺序
1. 执行所有同步代码
2. 执行所有微任务（micro task）
3. 执行一个宏任务（macro task）
4. 重复步骤2-3

### 面试重点
1. Promise的状态变化和链式调用
2. async/await的转换机制
3. 微任务和宏任务的执行顺序
4. 复杂异步代码的执行流程分析

---

## 扩展练习

建议读者自己分析以下代码的输出结果，并验证理解：

```javascript
console.log('start');

setTimeout(() => {
  console.log('timeout1');
  Promise.resolve().then(() => {
    console.log('promise1');
  });
}, 0);

Promise.resolve().then(() => {
  console.log('promise2');
  setTimeout(() => {
    console.log('timeout2');
  }, 0);
});

console.log('end');
```

**答案：**
```
start
end
promise2
timeout1
promise1
timeout2
```

通过这些练习，可以更好地理解JavaScript的事件循环机制和Promise的异步处理特性。