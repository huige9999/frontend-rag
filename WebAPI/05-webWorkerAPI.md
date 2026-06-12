# 05 - Web Worker API

## 一、基本概念

Web Worker 允许在**后台线程**中运行 JavaScript，不会阻塞主线程（UI 线程），适合处理耗时的计算任务。

**核心特点：**
- Worker 运行在独立线程，无法直接操作 DOM
- 主线程与 Worker 通过 `postMessage` / `onmessage` 通信
- 使用完毕后应调用 `worker.terminate()` 终止

## 二、创建 Worker

```js
const worker = new Worker("./assets/mission.js", {
  type: "module",   // 支持 ES Module 语法（import/export）
  name: "worker1",  // 可选，给 Worker 命名
});
```

## 三、主线程与 Worker 通信

### 3.1 主线程发送消息

```js
worker.postMessage(10);  // 发送数据给 Worker
```

### 3.2 主线程接收 Worker 返回的结果

```js
worker.onmessage = function (e) {
  console.log(e.data);  // 接收 Worker 处理后的数据
};
```

### 3.3 终止 Worker

```js
worker.terminate();  // 立即终止 Worker 线程
```

## 四、Worker 内部代码

### 4.1 简单计算任务（mission.js）

```js
console.log("我是新开启的worker线程，可以帮你解决一些问题");

self.onmessage = function (e) {
  let count = 0;
  for (let i = 0; i < 10 ** 1; i++) {
    count += e.data;  // 累加主线程传入的值
  }
  self.postMessage(count);  // 将结果发回主线程
};
```

> Worker 内部使用 `self` 代替 `window`，`self.onmessage` 监听主线程消息，`self.postMessage` 回传结果。

### 4.2 大文件分片处理（fileworker.js）

```js
import "./md5.min.js";  // 引入 MD5 计算库

self.onmessage = async function (e) {
  const [file, CHUNK_SIZE, startIndex, endIndex] = e.data;
  const result = [];
  for (let i = startIndex; i < endIndex; i++) {
    const chunk = await getChunk(file, CHUNK_SIZE, i);
    result.push(chunk);
  }
  self.postMessage(result);
};

// 读取文件分片并计算 MD5 哈希
function getChunk(file, size, index) {
  return new Promise((resolve) => {
    const start = index * size;
    const end = start + end;
    const chunkFile = file.slice(start, end);  // Blob.slice 切割文件
    const fr = new FileReader();
    fr.onload = function (e) {
      const arrbuffer = e.target.result;
      const hash = SparkMD5.ArrayBuffer.hash(arrbuffer);  // 计算 MD5
      resolve({ start, end, chunkFile, index, hash });
    };
    fr.readAsArrayBuffer(chunkFile);
  });
}
```

## 五、实战：大文件分片上传

利用多个 Worker 并行处理文件分片，提升大文件处理效率。

### 5.1 主线程动画（run.js）— 不受 Worker 影响

```js
const box = document.querySelector(".box");
let left = 0, s = 3;
setInterval(function () {
  left += s;
  if (left >= 500) s = -3;
  if (left <= 0) s = 3;
  box.style.left = left + "px";
}, 10);
```

> 这段动画在主线程运行，演示 Worker 处理耗时任务时 UI 不会被卡顿。

### 5.2 多 Worker 并行分片（demo.html 核心代码）

```js
const CHUNK_SIZE = 5 * 1024 * 1024;                    // 每片 5MB
const MAX_WORKER = navigator.hardwareConcurrency || 4;  // 根据 CPU 核心数决定 Worker 数量

fileDom.onchange = async function (e) {
  const file = e.target.files[0];
  const chunklength = Math.ceil(file.size / CHUNK_SIZE);  // 计算总分片数
  const count = Math.ceil(chunklength / MAX_WORKER);       // 每个 Worker 处理的分片数
  const result = [];
  let finished = 0;

  for (let i = 0; i < MAX_WORKER; i++) {
    const worker = new Worker("./assets/fileworker.js", { type: "module" });

    const startIndex = i * count;
    let endIndex = startIndex + count;
    if (endIndex > chunklength) endIndex = chunklength;

    worker.postMessage([file, CHUNK_SIZE, startIndex, endIndex]);

    worker.onmessage = function (e) {
      finished++;
      worker.terminate();                   // 处理完毕，终止该 Worker
      e.data.forEach((item) => {
        result[item.index] = item;          // 按序号归位结果
      });
      if (finished === MAX_WORKER) {
        console.log(result);                // 所有 Worker 完成，进行后续上传
      }
    };
  }
};
```

**流程说明：**
1. 用户选择文件 → 计算总分片数
2. 根据 `navigator.hardwareConcurrency` 创建对应数量的 Worker
3. 每个 Worker 负责一段分片：读取 → 计算 MD5 哈希
4. 所有 Worker 完成后 → 汇总结果 → 进行后续上传

## 六、API 速查表

| API | 说明 |
|-----|------|
| `new Worker(url, options)` | 创建 Worker 实例 |
| `worker.postMessage(data)` | 向 Worker 发送数据 |
| `worker.onmessage` | 接收 Worker 返回的数据 |
| `worker.terminate()` | 终止 Worker 线程 |
| `self.onmessage` | Worker 内监听主线程消息 |
| `self.postMessage(data)` | Worker 内向主线程发送数据 |
| `navigator.hardwareConcurrency` | 获取 CPU 逻辑核心数 |
