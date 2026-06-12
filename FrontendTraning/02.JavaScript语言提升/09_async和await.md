# async和await

## 学习目标

- 理解async和await的作用和语法
- 掌握async函数的使用方法
- 掌握await关键字的使用场景
- 能够使用async/await处理异步操作
- 理解try-catch在异步错误处理中的应用

## 消除回调

有了Promise，异步任务就有了一种统一的处理方式。有了统一的处理方式，ES官方就可以对其进一步优化。

ES7推出了两个关键字`async`和`await`，用于更加优雅的表达Promise，让异步代码看起来像同步代码一样清晰。

## async关键字

### 基本概念

async关键字用于修饰函数，被它修饰的函数，一定返回Promise。

### 语法规则

```js
// 1. 返回普通值 - 自动包装为resolve状态的Promise
async function method1(){
  return 1; // 该函数的返回值是Promise完成后的数据
}

method1(); // Promise { 1 }

// 2. 返回Promise - 状态保持一致
async function method2(){
  return Promise.resolve(1); // 若返回的是Promise，则method得到的Promise状态和其一致
}

method2(); // Promise { 1 }

// 3. 抛出错误 - 自动包装为reject状态的Promise
async function method3(){
  throw new Error(1); // 若执行过程报错，则任务是rejected
}

method3(); // Promise { <rejected> Error(1) }
```

### 实际应用示例

```js
// async函数的基本使用
async function fetchData() {
  // 模拟异步操作
  const data = await new Promise(resolve => {
    setTimeout(() => {
      resolve({ id: 1, name: '张三' });
    }, 1000);
  });
  
  return data; // 自动包装为Promise.resolve({ id: 1, name: '张三' })
}

fetchData().then(user => {
  console.log('获取到用户数据:', user);
});
```

## await关键字

### 基本概念

`await`关键字表示等待某个Promise完成，**它必须用于`async`函数中**。

### 语法规则

```js
async function method(){
  const n = await Promise.resolve(1);
  console.log(n); // 1
}

// 上面的函数等同于
function method(){
  return new Promise((resolve, reject)=>{
    Promise.resolve(1).then(n=>{
      console.log(n);
      resolve(1)
    })
  })
}
```

### await的使用场景

#### 1. 等待Promise

```js
async function getUser() {
  // 等待Promise完成并获取结果
  const response = await fetch('/api/user');
  const user = await response.json();
  return user;
}
```

#### 2. 等待其他数据

```js
async function method(){
  const n = await 1; // 等同于 await Promise.resolve(1)
  console.log(n); // 1
}
```

#### 3. 错误处理

如果需要针对失败的任务进行处理，可以使用`try-catch`语法：

```js
async function method(){
  try{
    const n = await Promise.reject(123); // 这句代码将抛出异常
    console.log('成功', n)
  }
  catch(err){
    console.log('失败', err)
  }
}

method(); // 输出： 失败 123
```

## 实战案例：邓哥表白

### 场景描述

邓哥的女神不是只有4位，而是40位！为了更加方便的编写表白代码，邓哥决定把这40位女神放到一个数组中，然后利用async和await轻松完成代码。

### 完整实现

```js
// 女神的名字数组
const beautyGirls = [
  '梁平', '邱杰', '王超', '冯秀兰', '赖军',
  '顾强', '戴敏', '吕涛', '冯静', '蔡明',
  '廖磊', '冯洋', '韩杰', '江涛', '文艳',
  '杜秀英', '丁艳', '邓静', '江刚', '乔刚',
  '史平', '康娜', '袁磊', '龙秀英', '姚静',
  '潘娜', '萧磊', '邵勇', '李芳', '谭芳',
  '夏秀英', '程娜', '武杰', '崔军', '廖勇',
  '崔强', '康秀英', '余磊', '邵勇', '贺涛',
];

// 向某位女生发送一则表白短信
// name: 女神的姓名
function sendMessage(name) {
  return new Promise((resolve, reject) => {
    // 模拟 发送表白短信
    console.log(
      `邓哥 -> ${name}：最近有谣言说我喜欢你，我要澄清一下，那不是谣言😘`
    );
    console.log(`等待${name}回复......`);
    
    // 模拟 女神回复需要一段时间
    setTimeout(() => {
      // 模拟 有10%的几率成功
      if (Math.random() <= 0.1) {
        // 成功，调用 resolve，并传递女神的回复
        resolve(`${name} -> 邓哥：我是九，你是三，除了你还是你😘`);
      } else {
        // 失败，调用 reject，并传递女神的回复
        reject(`${name} -> 邓哥：你是个好人😜`);
      }
    }, 1000);
  });
}

// 批量表白的程序
async function proposal() {
  let isSuccess = false;
  
  // 依次向每位女神表白
  for (const girl of beautyGirls) {
    try {
      // 等待女神回复
      const reply = await sendMessage(girl);
      console.log(reply);
      console.log('表白成功!');
      isSuccess = true;
      break; // 表白成功，退出循环
    } catch (reply) {
      // 表白被拒绝
      console.log(reply);
      console.log('表白失败');
    }
  }
  
  // 所有女神都拒绝了
  if (!isSuccess) {
    console.log('邓哥注定孤独一生');
  }
}

// 开始表白之旅
proposal();
```

### 代码分析

1. **顺序执行**：使用`for...of`循环依次向每位女神表白
2. **等待结果**：使用`await`等待每位女神的回复
3. **错误处理**：使用`try-catch`处理表白失败的情况
4. **成功退出**：表白成功后使用`break`退出循环

## 延时函数实现

### delay函数

```js
/**
 * 创建一个延时Promise
 * @param {number} duration - 延时时间（毫秒）
 * @returns {Promise} 延时Promise
 */
function delay(duration) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(); // 延时完成后resolve
    }, duration);
  });
}
```

### 使用async/await延时

```js
// 使用async/await实现延时
(async () => {
  try {
    await delay(1000); // 等待1秒
    console.log('成功');
  } catch (err) {
    console.log('失败');
  }
})();
```

### 传统Promise链式调用对比

```js
// 传统的Promise链式调用
delay(1000).then(
  (data) => {
    console.log('成功');
  },
  (err) => {
    console.log('失败');
  }
);
```

### 顺序延时执行

```js
/**
 * 顺序执行多次延时
 */
async function sequentialDelay() {
  console.log('开始执行');
  
  // 等待1秒 -> 输出ok -> 等待1秒 -> 输出ok -> 等待1秒 -> 输出ok
  for (let i = 0; i < 3; i++) {
    await delay(1000); // 等待1秒
    console.log('ok');
  }
  
  console.log('执行完成');
}

sequentialDelay();
```

## async/await的优势

### 1. 代码可读性强

```js
// Promise链式调用 - 回调地狱
fetch('/api/user')
  .then(response => response.json())
  .then(user => fetch(`/api/posts?userId=${user.id}`))
  .then(response => response.json())
  .then(posts => console.log(posts))
  .catch(err => console.error(err));

// async/await - 同步风格
async function getUserPosts() {
  try {
    const response = await fetch('/api/user');
    const user = await response.json();
    const postsResponse = await fetch(`/api/posts?userId=${user.id}`);
    const posts = await postsResponse.json();
    console.log(posts);
  } catch (err) {
    console.error(err);
  }
}
```

### 2. 错误处理统一

```js
// 使用try-catch统一处理错误
async function handleData() {
  try {
    const data1 = await fetchData1();
    const data2 = await fetchData2();
    const result = await processData(data1, data2);
    return result;
  } catch (error) {
    // 所有错误都在这里处理
    console.error('处理过程中发生错误:', error);
    throw error; // 可以选择重新抛出错误
  }
}
```

### 3. 调试方便

```js
async function debugExample() {
  console.log('开始执行'); // 可以直接添加断点
  
  const result1 = await step1(); // 每一步都可以单独调试
  console.log('步骤1完成', result1);
  
  const result2 = await step2(result1);
  console.log('步骤2完成', result2);
  
  return result2;
}
```

## 注意事项

### 1. await必须在async函数中使用

```js
// 错误示例
function badExample() {
  const result = await Promise.resolve(1); // 语法错误
}

// 正确示例
async function goodExample() {
  const result = await Promise.resolve(1); // 正确
}
```

### 2. async函数总是返回Promise

```js
async function example() {
  return 42; // 实际返回 Promise.resolve(42)
}

const result = example(); // result 是一个Promise
result.then(value => console.log(value)); // 42
```

### 3. 并行执行使用Promise.all

```js
// 串行执行 - 耗时较长
async function serial() {
  const result1 = await fetchData1(); // 等待1秒
  const result2 = await fetchData2(); // 等待1秒
  const result3 = await fetchData3(); // 等待1秒
  // 总耗时：3秒
}

// 并行执行 - 耗时更短
async function parallel() {
  const [result1, result2, result3] = await Promise.all([
    fetchData1(), // 同时执行
    fetchData2(), // 同时执行
    fetchData3()  // 同时执行
  ]);
  // 总耗时：1秒（假设每个请求都是1秒）
}
```

## 练习题

### 题目1：完成delay函数

```js
// 完成delay函数
// 该函数可以等待一段指定的时间
// 返回Promise
function delay(duration) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, duration);
  });
}

// 利用delay函数，等待3次，每次等待1秒，每次等待完成后输出ok
// 等待1秒->ok->等待1秒->ok->等待1秒->ok

async function practice() {
  for (let i = 0; i < 3; i++) {
    await delay(1000);
    console.log('ok');
  }
}

practice();
```

## 总结

### 核心概念

1. **async**：用于修饰函数，让函数总是返回Promise
2. **await**：用于等待Promise完成，只能在async函数中使用
3. **try-catch**：用于处理async/await中的错误

### 使用场景

- 需要按顺序执行多个异步操作
- 需要获取前一个异步操作的结果再执行下一个操作
- 需要统一的错误处理机制
- 希望异步代码看起来像同步代码一样清晰

### 最佳实践

1. 优先使用async/await而不是Promise链
2. 始终使用try-catch处理可能的错误
3. 需要并行执行时使用Promise.all
4. async函数总是返回Promise，调用时记得使用.then()或await