# 整合 TypeScript



## 准备工作

首先我们需要有一个基于 *ts* 的项目。

第一步通过 npm init -y 初始化项目

接下来通过：

```js
npm install typescript
```

局部安装 typescript。



之后还需要生成 typescript 的配置文件，通过命令：

```js
npx tsc --init
```

因为我们的项目是在 node 环境中运行，所以还需要安装 node 的类型说明文件

```js
npm i --save-dev @types/node
```



在 node 环境中，如果模块化使用的是 commonjs 规范，那么会存在一个问题，如果在一个模块中导出内容，在另外一个模块中导入这些内容，会提示"无法重新声明块范围变量"。

之所以会这样，是因为在 commonjs 规范里面，没有像 ESmodule 中能形成闭包的模块概念，所有的模块在引用的时候会被抛到全局，因此 ts 就会认为这里重复声明了模块。

要解决这个问题，首先在 ts 配置文件中，将 esModuleInterrop 开启为 true，该配置项用于控制是否启用 ES 模块规范的兼容性处理。

接下来在 tools.ts 文件的最后一行添加 export { } ，这一行代码会让 ts 认为这是一个 ESModule，从而不存在变量重复声明的问题。



项目书写完之后，我们可以配置要编译的 js 存储到哪一个目录下面：

```js
{
  "compilerOptions": {
    "outDir": "./dist",  
  },
  "include": ["./src"]
}
```



## 使用 jest 测试

首先第一步还是安装 jest，命令如下：

```js
npm install --save-dev jest
```

生成配置文件：

```js
npx jest --init
```



接下来在 *src* 目录下面创建 \__test__ 这个目录，在这个目录里面新增测试套件，一般来讲一个函数对应一个测试套件，在测试套件中会针对不同的参数来书写对应的测试用例。

```ts
const { randomNum } = require("../utils/tools");

test("测试随机数", () => {
  // 得到一个 4 位数的数组
  const result = randomNum();
  expect(result.length).toBe(4);
  expect(new Set(result).size).toBe(4);
  result.forEach((num: number) => {
    expect(num).toBeGreaterThanOrEqual(0);
    expect(num).toBeLessThanOrEqual(9);
  });
});

export {};
```

```ts
const { isRepeat } = require("../utils/tools");

test("参数为string类型数组", () => {
  expect(isRepeat(["1", "1", "2", "3"])).toBe(true);
  expect(isRepeat(["1", "4", "2", "3"])).toBe(false);
});

test("参数为number类型数组", () => {
  expect(isRepeat([1, 1, 2, 3])).toBe(true);
  expect(isRepeat([1, 4, 5, 6])).toBe(false);
});

export {};
```

在书写了测试用例之后，会发现 ts 报错，说找不到 jest 相关的类型说明，这一点和 node 是相似的，需要安装 jest 相关的类型说明文件

```js
npm i --save-dev @types/jest
```

除此之外，我们还需要安装一个名为 ts-jest 的库，这是一个 ts 的预处理器，可以让我们在使用 jest 来测试 ts 代码的时候直接运行 ts 代码：

```js
npm i ts-jest -D
```

还需要修改 jest 的配置文件，将 preset 修改为 ts-jest

```js
preset: "ts-jest",
```



## 关键代码

### jest.config.ts

Jest 配置文件，核心配置为 `preset: "ts-jest"`，用于支持 TypeScript 测试。

```ts
export default {
  clearMocks: true,
  collectCoverage: true,
  coverageDirectory: "coverage",
  coverageProvider: "v8",
  preset: "ts-jest",
};
```

### tsconfig.json

TypeScript 编译配置文件，指定编译输出目录和模块规范。

```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "outDir": "./dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  },
  "include": ["./src"]
}
```

### package.json

项目依赖配置，包含 jest、ts-jest、@types/jest 等测试相关依赖。

```json
{
  "name": "demo",
  "version": "1.0.0",
  "scripts": {
    "test": "jest",
    "build": "tsc"
  },
  "dependencies": {
    "readline-sync": "^1.4.10"
  },
  "devDependencies": {
    "@types/jest": "^29.5.1",
    "@types/node": "^18.16.0",
    "jest": "^29.5.0",
    "ts-jest": "^29.1.0",
    "typescript": "^5.0.4"
  }
}
```

### src/utils/tools.ts

工具函数模块，提供 `isRepeat`（判断数组元素是否重复）和 `randomNum`（生成4位不重复随机数）两个函数。

```ts
/**
 * 判断数组里面的数字是否重复
 * @param arr
 */
function isRepeat(arr: (string | number)[]): boolean {
  for (let i = 0; i < arr.length - 1; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {
        // 说明有数字重复
        return true;
      }
    }
  }
  return false;
}

/**
 * 生成 4 位随机数的方法
 */
function randomNum(): number[] {
  let num: number = 0; // 用于存放 0-9 之间的随机数
  let comNum: number[] = []; // 存放电脑所生成的数字，一开始是空数组
  while (true) {
    comNum.length = 0;
    for (let i = 0; i < 4; i++) {
      num = Math.floor(Math.random() * 10);
      comNum.push(num);
    }
    // 经历了上面的 for 循环，comNum 里面已经有 4 个数了
    // 但是数字可能是重复的 [1,1,3,4]
    if (!isRepeat(comNum)) {
      // 进入该 if，说明符合要求
      return comNum;
    }
  }
}

module.exports = {
    isRepeat,
    randomNum
};

export {};
```

### src/index.ts

猜数字游戏主入口，使用 readline-sync 实现玩家与电脑的交互。

```ts
// 该游戏是一个猜数字的游戏
// 玩家输入 4 位 0-9 不重复的数字，和电脑所生成 4 位 0-9 不重复的数字进行一个比较
// 如果位置和大小都正确，计入 A
// 如果数字正确但是位置不对，计入 B
// 电脑 1 2 3 4
// 玩家 5 2 1 7
// 返回 1A1B

const readline = require("readline-sync");
const { isRepeat, randomNum } = require("./utils/tools");

/**
 * 游戏的主函数
 */
function main(): void {
  // guessNum 用户输入的猜的数字，a 代表 A 的个数，b 代表 B 的个数
  // chance 代表猜测的机会
  let guessNum: string,
    a: number = 0,
    b: number = 0,
    chance: number = 10;

  // 电脑所生成的 4 位不重复的数字
  const comNum: number[] = randomNum();
  // 鼓励语句
  const arr: string[] = [
    "加油！",
    "还差一点了",
    "你马上就要猜中了",
    "很简单的，再想想",
    "也许你需要冷静一下",
  ];
  while (chance) {
    console.log("请输入你要猜测的数字");
    guessNum = readline.question("");
    if (guessNum.length !== 4) {
      console.log("长度必须为4");
    } else if (isNaN(Number(guessNum))) {
      console.log("输入的数字有问题");
    } else {
      // 符合要求，进行一个判断
      // 判断是否重复 需要将字符串转换为数组
      let guessNum2: string[] = [...guessNum];
      if (!isRepeat(guessNum2)) {
        // 如果能够进入到此 if 说明玩家输入的数字是OK的 可以开始进行判断
        for (let i = 0; i < guessNum2.length; i++) {
          for (let j = 0; j < comNum.length; j++) {
            if (guessNum2[i] == comNum[j].toString()) {
              // 如果能够进入到此 if 说明数字相同
              if (i === j) {
                // 如果进入此 if  说明 位置也相同
                a++;
              } else {
                b++;
              }
            }
          }
        }
        if (a === 4) {
          // 如果进入此 if 说明玩家全部猜对了 跳出while
          break;
        } else {
          console.log(`${a}A${b}B`);
          chance--;
          if (chance !== 0) {
            let index = Math.floor(Math.random() * arr.length);
            console.log(`你还剩下${chance}次机会,${arr[index]}`);
          }
          a = b = 0; // 清空 a 和 b 的值
        }
      } else {
        console.log("你输入的数字重复了, 请重新输入!");
      }
    }
  }
  // 如果跳出了上面的while 说明游戏结束了 但是 分为 2 种情况
  // 1. 提前猜对了   2. 机会用完了
  if (chance === 0) {
    // 进入此 if 说明是机会用完了
    console.log("很遗憾,你已经没有机会了！");
    console.log(`电脑生成的随机数为${comNum}`);
  } else {
    console.log("恭喜你,猜测正确,游戏结束");
    console.log("Thank you for playing");
  }
}

main();
```

### src/__test__/randomNum.test.ts

测试 `randomNum` 函数，验证生成的随机数长度为4、元素不重复且在0-9范围内。

```ts
const { randomNum } = require("../utils/tools");

test("测试随机数", () => {
  // 得到一个 4 位数的数组
  const result = randomNum();
  expect(result.length).toBe(4);
  expect(new Set(result).size).toBe(4);
  result.forEach((num: number) => {
    expect(num).toBeGreaterThanOrEqual(0);
    expect(num).toBeLessThanOrEqual(9);
  });
});

export {};
```

### src/__test__/isRepeat.test.ts

测试 `isRepeat` 函数，验证对字符串数组和数字数组的重复判断是否正确。

```ts
const { isRepeat } = require("../utils/tools");

test("参数为string类型数组", () => {
  expect(isRepeat(["1", "1", "2", "3"])).toBe(true);
  expect(isRepeat(["1", "4", "2", "3"])).toBe(false);
});

test("参数为number类型数组", () => {
  expect(isRepeat([1, 1, 2, 3])).toBe(true);
  expect(isRepeat([1, 4, 5, 6])).toBe(false);
});

export {};
```
