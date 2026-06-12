# 07. Promise的链式调用

## 课程目标

- 理解Promise链式调用的原理
- 掌握then方法和catch方法的使用
- 学会使用链式调用处理异步任务序列
- 理解Promise状态传递机制

## catch方法

`.catch(onRejected)` 等同于 `.then(null, onRejected)`

catch方法是then方法的语法糖，专门用于处理Promise失败的情况。

```javascript
// 基本用法
new Promise((resolve, reject) => {
  reject(new Error('abc'));
}).catch((err) => {
  console.log('失败了！！', err);
});
```

## 链式调用原理

### 1. then方法必定会返回一个新的Promise

可以理解为"后续处理也是一个任务"。

### 2. 新任务的状态取决于后续处理

**情况一：没有相关的后续处理**
- 新任务的状态和前任务一致，数据为前任务的数据

**情况二：有后续处理但还未执行**
- 新任务挂起

**情况三：后续处理执行了**
根据后续处理的情况确定新任务的状态：
- 后续处理执行无错：新任务的状态为**完成**，数据为后续处理的返回值
- 后续处理执行有错：新任务的状态为**失败**，数据为异常对象
- 后续执行后返回的是一个任务对象：新任务的状态和数据与该任务对象一致

由于链式任务的存在，异步代码拥有了更强的表达力。

### 链式调用示例

```javascript
// 常见任务处理代码

/*
 * 任务成功后，执行处理1，失败则执行处理2
 */
pro.then(处理1).catch(处理2)

/*
 * 任务成功后，依次执行处理1、处理2
 */
pro.then(处理1).then(处理2)

/*
 * 任务成功后，依次执行处理1、处理2，若任务失败或前面的处理有错，执行处理3
 */
pro.then(处理1).then(处理2).catch(处理3)
```

## 实战案例

### 案例1：基本链式调用

```javascript
// then方法返回新的Promise
const pro1 = new Promise((resolve, reject) => {
  console.log('学习');
  resolve();
});

const pro2 = pro1.then(() => {
  return new Promise((resolve, reject) => {});
});

// pro1和pro2是不同的Promise对象
setTimeout(() => {
  console.log(pro2);
}, 1000);
```

### 案例2：链式调用传递数据

```javascript
const pro1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

const pro2 = pro1.then((data) => {
  console.log(data); // 输出: 1
  return data + 1;
});

const pro3 = pro2.then((data) => {
  console.log(data); // 输出: 2
});

console.log(pro1, pro2, pro3); // 初始状态都是pending

setTimeout(() => {
  console.log(pro1, pro2, pro3); // 1秒后状态变化
}, 2000);
```

### 案例3：错误处理

```javascript
// resolve状态下，catch不会执行
new Promise((resolve, reject) => {
  resolve();
})
  .then((res) => {
    console.log(res.toString());
    return 2;
  })
  .catch((err) => {
    return 3;
  })
  .then((res) => {
    console.log(res); // 输出: 2
  });
```

```javascript
// resolve传递数据
new Promise((resolve, reject) => {
  resolve(1);
})
  .then((res) => {
    console.log(res); // 输出: 1
    return 2;
  })
  .catch((err) => {
    return 3;
  })
  .then((res) => {
    console.log(res); // 输出: 2
  });
```

```javascript
// 错误会传递到最近的catch
new Promise((resolve, reject) => {
  throw new Error(1);
})
  .then((res) => {
    console.log(res);
    return new Error('2');
  })
  .catch((err) => {
    throw err;
    return 3; // 这行不会执行
  })
  .then((res) => {
    console.log(res);
  });
```

```javascript
// catch也能返回新的Promise
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

### 案例4：邓哥的解决方案

```javascript
// 向某位女生发送一则表白短信
// name: 女神的姓名
// 返回：Promise
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

// 链式调用：多次尝试直到成功或全部失败
sendMessage('李建国')
  .catch((reply) => {
    // 失败，继续下一个
    console.log(reply);
    return sendMessage('王富贵');
  })
  .catch((reply) => {
    // 失败，继续下一个
    console.log(reply);
    return sendMessage('周聚财');
  })
  .catch((reply) => {
    // 失败，继续下一个
    console.log(reply);
    return sendMessage('刘人勇');
  })
  .then(
    (reply) => {
      // 成功，结束
      console.log(reply);
      console.log('邓哥终于找到了自己的伴侣');
    },
    (reply) => {
      // 最后一个也失败了
      console.log(reply);
      console.log('邓哥命犯天煞孤星，无伴终老，孤独一生');
    }
  );
```

## 链式调用要点

### 1. 数据传递

```javascript
Promise.resolve(1)
  .then(data => {
    console.log(data); // 1
    return data + 1;
  })
  .then(data => {
    console.log(data); // 2
    return data + 1;
  })
  .then(data => {
    console.log(data); // 3
  });
```

### 2. 错误冒泡

```javascript
Promise.resolve()
  .then(() => {
    throw new Error('错误1');
  })
  .then(() => {
    console.log('这不会执行');
  })
  .catch(err => {
    console.log(err.message); // 错误1
  });
```

### 3. 返回Promise

```javascript
Promise.resolve()
  .then(() => {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve('延迟的结果');
      }, 1000);
    });
  })
  .then(data => {
    console.log(data); // 1秒后输出: 延迟的结果
  });
```

## 练习题

### 练习1：理解链式调用

分析以下代码的输出结果：

```javascript
const pro1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

const pro2 = pro1.then((data) => {
  console.log(data);
  return data + 1;
});

const pro3 = pro2.then((data) => {
  console.log(data);
});

console.log(pro1, pro2, pro3);

setTimeout(() => {
  console.log(pro1, pro2, pro3);
}, 2000);
```

### 练习2：状态传递

分析以下代码的输出结果：

```javascript
new Promise((resolve, reject) => {
  resolve();
})
  .then((res) => {
    console.log(res.toString());
    return 2;
  })
  .catch((err) => {
    return 3;
  })
  .then((res) => {
    console.log(res);
  });
```

### 练习3：数据处理链

分析以下代码的输出结果：

```javascript
new Promise((resolve, reject) => {
  resolve(1);
})
  .then((res) => {
    console.log(res);
    return 2;
  })
  .catch((err) => {
    return 3;
  })
  .then((res) => {
    console.log(res);
  });
```

### 练习4：错误处理

分析以下代码的输出结果：

```javascript
new Promise((resolve, reject) => {
  throw new Error(1);
})
  .then((res) => {
    console.log(res);
    return new Error('2');
  })
  .catch((err) => {
    throw err;
    return 3;
  })
  .then((res) => {
    console.log(res);
  });
```

### 练习5：catch的返回值

分析以下代码的输出结果：

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

## 总结

1. **then方法总是返回新的Promise**，这使得链式调用成为可能
2. **数据会沿着链条传递**，每个then可以处理并传递数据给下一个
3. **错误会沿链条冒泡**，直到被catch捕获
4. **返回Promise时**，后续then会等待该Promise完成
5. **链式调用让异步代码更加清晰和可控**，避免了回调地狱

## 课后作业

1. 编写一个函数，使用Promise链式调用实现以下功能：
   - 读取用户信息
   - 根据用户ID读取订单列表
   - 根据订单ID读取订单详情
   - 捕获任何环节的错误

2. 将以下回调地狱代码改写为Promise链式调用：
```javascript
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        // 最终处理
      });
    });
  });
});
```
