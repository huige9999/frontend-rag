# Jest 配置文件

在官网对应的 ：https://jestjs.io/docs/configuration 可以看到 Jest 中所有的配置项目。

当我们要对 Jest 进行大量的配置的时候，肯定是需要配置文件的，那么首先我们需要生成一个配置文件：

```js
npx jest --init
```

生成配置文件如下图所示：

![image-20230424092507341](https://resource.duyiedu.com/xiejie/2023-04-24-012507.png)



下面介绍一些配置文件中常见的配置项。



collectCoverage：会收集并显示测试覆盖率，包含每个文件中每种类型的代码（语句、分支、函数和行）的测试覆盖率

<img src="https://resource.duyiedu.com/xiejie/2023-04-24-012840.png" alt="image-20230424092839487" style="zoom:50%;" />

在上面的表格中，我们能够看到如下的信息：

- % Stmts：包含语句的百分比，即被测试覆盖的语句占总语句数的比例。
- % Branch：包含分支的百分比，即被测试覆盖的分支占总分支数的比例。
- % Funcs：包含函数的百分比，即被测试覆盖的函数占总函数数的比例。
- % Lines：包含行的百分比，即被测试覆盖的行占总行数的比例。
- Uncovered Line #s：未被测试覆盖的行号。

从上面的测试报告中，我们可以看出，tools.js 文件下面的 sum 和 sub 这两个函数的测试没有被覆盖到，因为我们在测试的时候，我们是将原来的 sum 和 sub 替换掉了的。

当 collectCoverage 设置为 true 之后，还可以设置 coverageThreshold 代码覆盖率的阀值：

```js
module.exports = {
  // ...
  collectCoverage: true,
  coverageThreshold: {
    global: {
      branches: 90,
      functions: 90,
      lines: 90,
      statements: 90,
    },
  },
  // ...
};
```

另外，在项目根目录下面，还新生成了一个 coverage 的目录，里面其实就是各种格式（xml、json、html）的测试报告，之所以生成不同格式的报告，是为了方便你后面通过不同的工具来进行读取。

例如 HTML 版本的测试报告如下图所示：

![image-20230424095300700](https://resource.duyiedu.com/xiejie/2023-04-24-015301.png)

testMatch：这个配置项可以指定 Jest 应该运行哪些测试文件。默认情况下， Jest 会查找 .test.js 或者 .spec.js 结尾的文件

例如我们将该配置修改为如下：

```js
testMatch: [
    "**/test/**/*.[jt]s?(x)",
],
```



moduleFileExtensions :指定 Jest 查找测试文件时应该搜索哪些文件扩展名。



setupFilesAfterEnv：指定 Jest 在运行测试之前应该运行哪些文件。例如：

```js
setupFilesAfterEnv: ['<rootDir>/src/setupTests.js']
```

在执行每个测试套件（文件）之前，都会先执行这个 setupTests 文件

## 关键代码

### jest.config.js

Jest 配置文件，配置了覆盖率收集、覆盖率阈值、测试文件匹配规则以及 setup 文件。

```js
/*
 * For a detailed explanation regarding each configuration property, visit:
 * https://jestjs.io/docs/configuration
 */

module.exports = {
  // Automatically clear mock calls, instances, contexts and results before every test
  clearMocks: true,

  // Indicates whether the coverage information should be collected while executing the test
  collectCoverage: true,

  // The directory where Jest should output its coverage files
  coverageDirectory: "coverage",

  // Indicates which provider should be used to instrument code for coverage
  coverageProvider: "v8",

  // An object that configures minimum threshold enforcement for coverage results
  coverageThreshold: {
    global: {
      branches: 90,
      functions: 90,
      lines: 90,
      statements: 90,
    },
  },

  // A list of paths to modules that run some code to configure or set up the testing framework before each test
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],

  // The glob patterns Jest uses to detect test files
  testMatch: [
    "**/test/**/*.[jt]s?(x)",
  ],
};
```

### package.json

项目配置文件，包含 jest 测试依赖和 axios 请求依赖。

```json
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "jest"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "jest": "^29.5.0"
  },
  "dependencies": {
    "axios": "^1.3.5"
  }
}
```

### src/setupTests.js

在每个测试套件执行前运行的 setup 文件，配置全局的 beforeEach 和 afterEach。

```js
beforeEach(() => {
  console.log("全局的beforeEach");
});

afterEach(() => {
  console.log("全局的afterEach");
});
```

### api/userApi.js

用户 API 模块，封装了 axios 请求。

```js
/**
 * 和请求相关的
 */

const axios = require("axios");

class User {
  /**
   * 获取所有的用户
   */
  static all() {
    return axios.get("/users.json").then((resp) => resp.data);
  }

  static testArg(){
    // 这个方法本身 axios 是没有的
    // 我们通过模拟 axios 这个模块，然后给 axios 这个模块添加了这么一个 test方法
    // 这里在实际开发中没有太大意义，仅做演示
    return axios.test();
  }
}

module.exports = User;
```

### utils/tools.js

被测试的工具库，导出加减乘除四个函数。

```js
/**
 * 工具库
 */

exports.sum = function (a, b) {
  return a + b;
};

exports.sub = function (a, b) {
  return a - b;
};

exports.mul = function (a, b) {
  return a * b;
};

exports.div = function (a, b) {
  return a / b;
};
```

### test/mockFile.spec.js

测试文件，演示模拟文件模块，使用 jest.requireActual 获取原始模块并部分替换。

```js
const { sum, sub, mul, div } = require("../utils/tools");

jest.mock("../utils/tools", () => {
  // 在这里来改写文件模块的实现

  // 拿到 ../utils/tools 路径所对应的文件原始模块
  const originalModule = jest.requireActual("../utils/tools");

  // 这里相当于是替换了原始的模块
  // 一部分方法使用原始模块中的方法
  // 一部分方法（sum、sub）被替换掉了
  return {
    ...originalModule,
    sum: jest.fn(() => 100),
    sub: jest.fn(() => 50),
  };
});

test("对模块进行测试", () => {
  expect(sum(1, 2)).toBe(100);
  expect(sub(10, 3)).toBe(50);
  expect(mul(10, 3)).toBe(30);
  expect(div(10, 2)).toBe(5);
});
```

### test/mockModule.test.js

测试文件，演示模拟 axios 第三方模块。

```js
// const axios = require("axios");
const User = require("../api/userApi");
const userData = require("./user.json");

// 模拟 axios 模块
jest.mock("axios", () => {
  const userData = require("./user.json");
  // 模拟响应数据
  const resp = {
    data: userData,
  };
  return {
    get: jest.fn(() => Promise.resolve(resp)),
    test : jest.fn(() => Promise.resolve("this is a test")),
  };
});

// 测试用例
test("测试获取用户数据", async () => {
  // 现在我们已经模拟了 axios
  // 但是目前的 axios 没有书写任何的行为
  // 因此我们需要在这里进行一个 axios 模块行为的指定
  // 指定了在使用 axios.get 的时候返回 resp 响应
  // axios.get.mockImplementation(()=>Promise.resolve(resp));

  await expect(User.all()).resolves.toEqual(userData);
  await expect(User.testArg()).resolves.toEqual("this is a test");
});
```
