# Promise基础

## 本节课的任务

1. 理解Promise A+规范的基本概念
2. 学会创建Promise
3. 学会针对Promise进行后续处理

## 回调地狱问题

### 邓哥的烦恼

邓哥心中有很多女神，他今天下定决心，要向这些女神表白，他认为，只要女神够多，根据概率学原理，总有一个会接收他。

稳妥起见，邓哥决定使用**串行**的方式进行表白：先给第1位女神发送短信，然后等待女神的回应，如果成功了，就结束，如果失败了，则再给第2位女神发送短信，依次类推。

### 传统回调函数的问题

下面是传统的回调函数实现方式：

```javascript
// 向某位女生发送一则表白短信
// name: 女神的姓名
// onFulffiled: 成功后的回调
// onRejected: 失败后的回调
function sendMessage(name, onFulffiled, onRejected) {
  // 模拟 发送表白短信
  console.log(
    `邓哥 -> ${name}：最近有谣言说我喜欢你，我要澄清一下，那不是谣言😘`
  );
  console.log(`等待${name}回复......`);
  
  // 模拟 女神回复需要一段时间
  setTimeout(() => {
    // 模拟 有10%的几率成功
    if (Math.random() <= 0.1) {
      // 成功，调用 onFuffiled，并传递女神的回复
      onFulffiled(`${name} -> 邓哥：我是九，你是三，除了你还是你😘`);
    } else {
      // 失败，调用 onRejected，并传递女神的回复
      onRejected(`${name} -> 邓哥：你是个好人😜`);
    }
  }, 1000);
}

// 首先向 李建国 发送消息
sendMessage(
  '李建国',
  (reply) => {
    // 如果成功了，输出回复的消息后，结束
    console.log(reply);
  },
  (reply) => {
    // 如果失败了，输出回复的消息后，向 王富贵 发送消息
    console.log(reply);
    sendMessage(
      '王富贵',
      (reply) => {
        // 如果成功了，输出回复的消息后，结束
        console.log(reply);
      },
      (reply) => {
        // 如果失败了，输出回复的消息后，向 周聚财 发送消息
        console.log(reply);
        sendMessage(
          '周聚财',
          (reply) => {
            // 如果成功了，输出回复的消息后，结束
            console.log(reply);
          },
          (reply) => {
            // 如果失败了，输出回复的消息后，向 刘人勇 发送消息
            console.log(reply);
            sendMessage(
              '刘人勇',
              (reply) => {
                // 如果成功了，输出回复的消息后，结束
                console.log(reply);
              },
              (reply) => {
                // 如果失败了，就彻底没戏了
                console.log(reply);
                console.log('邓哥命犯天煞孤星，注定孤独终老！！');
              }
            );
          }
        );
      }
    );
  }
);
```

这样的代码形成了传说中的「**回调地狱 callback hell**」，代码嵌套层次深，难以维护和阅读。

## Promise规范

Promise是一套专门处理异步场景的规范，它能有效的避免回调地狱的产生，使异步代码更加清晰、简洁、统一。

这套规范最早诞生于前端社区，规范名称为[Promise A+](https://promisesaplus.com/)。

### Promise A+ 规范的核心内容

#### 1. 任务对象化

所有的异步场景，都可以看作是一个异步任务，每个异步任务，在JS中应该表现为一个**对象**，该对象称之为**Promise对象**，也叫做任务对象。

#### 2. 两个阶段、三个状态

每个任务对象，都应该有两个阶段、三个状态：

- **未决阶段（pending）**：任务挂起状态
- **已决阶段（settled）**：
  - **完成状态（fulfilled）**：任务成功完成
  - **失败状态（rejected）**：任务失败

状态转换规则：
- 任务总是从未决阶段变到已决阶段，无法逆行
- 任务总是从挂起状态变到完成或失败状态，无法逆行
- 时间不能倒流，历史不可改写，任务一旦完成或失败，状态就固定下来，永远无法改变

#### 3. 状态转换方法

- `resolve(data)`：将任务从挂起状态变为完成状态，data为需要传递的相关数据
- `reject(reason)`：将任务从挂起状态变为失败状态，reason为需要传递的失败原因

#### 4. 后续处理

可以针对任务进行后续处理：
- **onFulfilled**：针对完成状态的后续处理
- **onRejected**：针对失败的后续处理

## Promise API

ES6提供了一套API，实现了Promise A+规范。

### 基本语法

```javascript
// 创建一个任务对象，该任务立即进入 pending 状态
const pro = new Promise((resolve, reject) => {
  // 任务的具体执行流程，该函数会立即被执行
  // 调用 resolve(data)，可将任务变为 fulfilled 状态， data 为需要传递的相关数据
  // 调用 reject(reason)，可将任务变为 rejected 状态，reason 为需要传递的失败原因
});

pro.then(
  (data) => {
    // onFulfilled 函数，当任务完成后，会自动运行该函数，data为任务完成的相关数据
  },
  (reason) => {
    // onRejected 函数，当任务失败后，会自动运行该函数，reason为任务失败的相关原因
  }
);
```

### 实际应用示例

#### 示例1：Promise版本的sendMessage

```javascript
// 向某位女生发送一则表白短信
// name: 女神的姓名
// 该函数返回一个任务对象
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

// 使用Promise发送消息
sendMessage('李建国').then(
  (reply) => {
    // 女神答应了，输出女神的回复
    console.log('成功！', reply);
  },
  (reason) => {
    // 女神拒绝了，输出女神的回复
    console.log('失败！', reason);
  }
);
```

#### 示例2：百米短跑模拟

```javascript
const pro = new Promise((resolve, reject) => {
  console.log('开始百米短跑');
  const duration = Math.floor(Math.random() * 5000);

  setTimeout(() => {
    if (Math.random() < 0.5) {
      // 成功完成比赛
      resolve(duration); // 将任务从挂起->完成，传递比赛用时
    } else {
      // 失败，脚扭伤了
      reject('脚扭伤了！'); // 将任务从挂起->失败，传递失败原因
    }
  }, duration);
});

pro.then(
  (data) => {
    console.log('on yeah! 我跑了', data, '秒');
  },
  (reason) => {
    console.log('不好意思，', reason);
  }
);
```

## Promise的重要特性

### 1. 状态一旦确定就无法改变

```javascript
new Promise((resolve, reject) => {
  console.log('任务开始');
  resolve(1);  // 任务变为fulfilled状态
  reject(2);   // 无效，状态已经确定
  resolve(3);  // 无效，状态已经确定
  console.log('任务结束');
});
// 最终状态：fulfilled，相关数据：1

new Promise((resolve, reject) => {
  console.log('任务开始');
  resolve(1);
  resolve(2);  // 无效，第一次resolve已经确定状态
  console.log('任务结束');
});
// 最终状态：fulfilled，相关数据：1
```

### 2. Promise构造函数的执行器函数会立即执行

```javascript
console.log('1');
const pro = new Promise((resolve, reject) => {
  console.log('2'); // 这个会立即执行
  resolve(3);
});
console.log('4');
// 输出顺序：1, 2, 4
```

### 3. then方法的回调函数是异步执行的

```javascript
const pro = new Promise((resolve, reject) => {
  resolve(1);
});

console.log('before then');
pro.then((data) => {
  console.log('in then:', data);
});
console.log('after then');
// 输出顺序：before then, after then, in then: 1
```

## 练习题

### 练习1：实现delay函数

```javascript
/**
 * 延迟一段指定的时间
 * @param {Number} duration 等待的时间（毫秒）
 * @returns {Promise} 返回一个任务，该任务在指定的时间后完成
 */
function delay(duration) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, duration);
  });
}

// 利用delay函数，等待1秒钟，输出：finish
delay(1000).then(() => {
  console.log('finish');
});
```

### 练习2：分析Promise状态

分析下面的Promise最终状态和相关数据：

```javascript
// Promise 1
new Promise((resolve, reject) => {
  console.log('任务开始');
  resolve(1);
  reject(2);   // 无效，状态已确定
  resolve(3);  // 无效，状态已确定
  console.log('任务结束');
});
// 最终状态：fulfilled，相关数据：1

// Promise 2
new Promise((resolve, reject) => {
  console.log('任务开始');
  resolve(1);
  resolve(2);  // 无效，第一次resolve已确定状态
  console.log('任务结束');
});
// 最终状态：fulfilled，相关数据：1
```

## 总结

### Promise的核心优势

1. **统一的异步处理方式**：所有异步操作都可以用Promise来表示
2. **链式调用**：避免了回调地狱，代码更加清晰
3. **状态管理**：明确的三状态管理，状态一旦确定无法改变
4. **错误处理**：统一的错误处理机制

### 使用Promise的注意事项

1. **状态不可逆**：Promise状态一旦改变就无法再改变
2. **立即执行**：Promise构造函数中的代码会立即执行
3. **异步回调**：then方法中的回调函数是异步执行的
4. **必须返回Promise**：要充分利用Promise的优势，函数应该返回Promise对象

Promise是现代JavaScript异步编程的基础，后续还会学习async/await等更高级的异步处理方式，它们都是基于Promise构建的。