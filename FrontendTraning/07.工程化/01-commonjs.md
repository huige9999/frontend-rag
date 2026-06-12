# 01. CommonJS 模块化

## 为什么需要模块化

当前端工程到达一定规模后，就会出现下面的问题：

- **全局变量污染**：不同文件中的同名变量会相互覆盖，导致难以调试的bug
- **依赖混乱**：脚本加载顺序至关重要，但难以管理和维护

上面的问题，共同导致了**代码文件难以细分**。

模块化就是为了解决上面两个问题出现的。模块化出现后，我们就可以把臃肿的代码细分到各个小文件中，便于后期维护管理。

## 前端模块化标准

前端主要有两大模块化标准：

- **CommonJS**，简称CMJ，这是一个**社区**规范，出现时间较早，目前仅node环境支持
- **ES Module**，简称ESM，这是随着ES6发布的**官方**模块化标准，目前浏览器和新版本node环境均支持

### Node环境安装

下载地址：https://nodejs.org/zh-cn/

建议使用 LTS（长期支持）版本进行学习。

## CommonJS如何实现模块化

Node天生支持CommonJS模块化标准。

### 基本规范

Node规定：

1. **每个文件都是模块**：Node中的每个js文件都是一个CMJ模块，通过node命令运行的模块，叫做入口模块

2. **作用域隔离**：模块中的所有全局定义的变量、函数，都不会污染到其他模块

3. **导出机制**：模块可以暴露（导出）一些内容给其他模块使用，需要暴露什么内容，就在模块中给`module.exports`赋值

4. **导入机制**：一个模块可以导入其他模块，使用函数`require("要导入的模块路径")`即可完成，该函数返回目标模块的导出结果
   - 导入模块时，可以省略`.js`
   - 导入相对路径模块时，必须以`./`或`../`开头

5. **模块缓存**：一个模块在被导入时会运行一次，然后它的导出结果会被Node缓存起来，后续对该模块导入时，不会重新运行，直接使用缓存结果

## 代码示例

### 1. 导出模块

```javascript
// math.js - 提供一些数学相关的函数
console.log('math run'); // 模块首次被导入时会执行

// 判断奇数
function isOdd(n) {
  return n % 2 !== 0;
}

// 求和函数
function sum(a, b) {
  return a + b;
}

// 导出这些函数供其他模块使用
module.exports = {
  isOdd,
  sum,
};
```

### 2. 导入模块

```javascript
// index.js - 启动文件，通过node命令运行
console.log('index start');

// 导入math模块，require返回该模块的导出对象
const math = require('./math'); // 返回 { isOdd: fn, sum: fn }

// 使用导入的函数
console.log(math.sum(1, 2)); // 输出: 3
console.log(math.isOdd(3));  // 输出: true

// 多次导入同一个模块，由于缓存机制，模块只会执行一次
require('./math'); // 不会再次执行 console.log('math run')
require('./math');
require('./math');
```

### 3. 模块缓存演示

运行 `node index.js`，输出结果：

```
index start
math run
3
```

可以看到，尽管我们多次require math模块，但`console.log('math run')`只执行了一次，这就是模块缓存的作用。

## 实战练习：逐字打印效果

下面我们通过一个完整的练习来理解CommonJS的模块化。

### 需求描述

创建一个打字机效果，逐字打印文本内容，每个字之间有指定的时间间隔。

### 项目结构

```
demo/
├── config.js   # 配置模块
├── delay.js    # 延迟模块
├── print.js    # 打印模块
└── main.js     # 主模块
```

### 1. 配置模块 config.js

```javascript
// 这是一个配置模块

module.exports = {
  wordDuration: 100, // 打印每个字的时间间隔（毫秒）
  text: `asdfasdfasdfasfd`, // 要打印的文字
};
```

### 2. 延迟模块 delay.js

```javascript
/**
 * 该函数返回一个Promise，它会等待指定的毫秒数，时间到达后该函数完成
 * @param {number} ms 毫秒数
 * @returns {Promise}
 */
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

// 导出delay函数
module.exports = delay;
```

### 3. 打印模块 print.js

```javascript
const config = require('./config');

/**
 * 该函数会做以下两件事：
 * 1. console.clear() 清空控制台
 * 2. 读取config.js中的text配置，打印开始位置到index位置的字符
 * @param {number} index 打印到第几个字符
 */
function print(index) {
  console.clear(); // 清空控制台，实现逐字显示效果
  const txt = config.text.substring(0, index + 1); // 截取从开始到当前位置的文本
  console.log(txt); // 打印文本
}

// 导出print函数
module.exports = print;
```

### 4. 主模块 main.js

```javascript
const config = require('./config');
const print = require('./print');
const delay = require('./delay');

/**
 * 运行该函数，会逐字打印config.js中的文本
 * 每个字之间的间隔在config.js已有配置
 */
async function run() {
  let index = 0;
  while (index < config.text.length) {
    print(index); // 打印到这个位置
    await delay(config.wordDuration); // 等待指定时间
    index++;
  }
}

// 启动程序
run();
```

### 运行效果

在demo目录下执行：

```bash
node main.js
```

你将看到文本逐字显示在控制台中，每个字之间间隔100毫秒。

## CommonJS 重要特性

### 1. 值的拷贝

CommonJS导出的是值的拷贝，也就是说，一旦导出一个值，模块内部的变化不会影响已导出的值。

```javascript
// counter.js
let count = 0;
module.exports = {
  count,
  increment: () => count++
};

// main.js
const counter = require('./counter');
console.log(counter.count); // 0
counter.increment();
console.log(counter.count); // 还是0，因为导出的是原始值的拷贝
```

### 2. 运行时加载

CommonJS是运行时加载模块，只有代码运行到require时才会加载模块。

### 3. 同步加载

CommonJS是同步加载模块，因此只适合服务器端环境（Node.js），不适合浏览器环境。

## CommonJS vs ES Module

| 特性 | CommonJS | ES Module |
|------|----------|-----------|
| 关键字 | require/module.exports | import/export |
| 加载方式 | 运行时同步加载 | 编译时静态加载 |
| 环境支持 | Node.js | 现代浏览器和Node.js |
| 输出 | 值的拷贝 | 值的引用 |
| 本质 | 对象 | 命令式 |

## 总结

CommonJS作为Node.js的模块化标准，具有以下特点：

1. **简单直观**：使用require和module.exports，易于理解
2. **运行时加载**：模块在代码执行时动态加载
3. **缓存机制**：模块只执行一次，后续导入使用缓存
4. **服务端友好**：同步加载适合服务器环境

虽然ES Module已成为官方标准，但CommonJS在Node.js生态中仍然广泛使用，理解它对于前端工程化学习非常重要。

## 扩展阅读

- Node.js官方文档：https://nodejs.org/docs/latest/api/modules.html
- ES Module详解：下一章《02. ES Module》
- 模块化发展历史：从IIFE、AMD、CMD到CommonJS、ES Module