# Lesson 14: ES Next - JavaScript Evolution from ES2016 to ES2024

## 本章导读

### JavaScript 简短历史回顾

1995年，网景公司一位叫做 Brendan Eich 的工程师，为即将发布的 Netscape Navigator2 开发了一个叫做 Mocha（后来改名为 LiveScript） 的脚本语言，当时的计划是在客户端和服务端都使用它，在服务端的名字叫做 LiveWire.

后来为了赶上发布时间，网景与 Sun 公司结成开发联盟，共同完成了 LiveScript 的开发。就在 Netscape Navigator2 正式发布之前，网景把 LiveScript 改名为了 JavaScript，为的是蹭一下 Java 热度。

### JavaScript 标准化历程

早期存在的问题：
- Netscape Navigator 中的 JavaScript
- IE 中的 JScript
- 两个版本功能相似但细节不同，导致兼容性问题

1997年，JavaScript1.1作为提案被提交给了欧洲计算机制造协会（Ecma），其中的第 39 技术委员会（TC39）负责来为这门语言制定标准。TC39 委员会的成员有网景、Sun、微软等，他们推出了 ECMA-262，也就是 ECMAScript。

### ECMAScript 版本发展历程

- **ECMAScript1**: 本质上与网景的 JavaScript1.1 相同，删除了浏览器特定代码
- **ECMAScript2**: 编校工作，符合 ISO/IEC-16262 要求
- **ECMAScript3**: 第一次真正更新，新增正则表达式、try/catch 等
- **ECMAScript4**: 激进修订，最终被废弃
- **ECMAScript5**: 2009年发布，新增 JSON、严格模式等
- **ECMAScript6 (ES2015)**: 重大更新，新增类、模块、箭头函数等
- **ES2016-ES2024**: 一年一版本，持续演进

---

## ES2016 (ES7)

ES2016 是一个相对较小的更新版本，只引入了两个新特性：

### 1. Array.prototype.includes

Array.prototype.includes 方法用于检查一个数组中是否包含某个特定的元素，返回布尔值 true 或 false。

```javascript
const arr = [1, 2, 3, 4, 5];
console.log(arr.includes(3)); // true
console.log(arr.includes(-1)); // false
```

**优势**：它是 indexOf 的改进版本，区别在于 indexOf 返回的是元素的索引值，而 includes 返回布尔值。这样所表达的语义更好一些。此外，includes 还可以正确处理 NaN，而 indexOf 不能。

```javascript
// 以前通常使用 indexOf 来查找特定的元素
console.log(arr.indexOf(3)); // 2
console.log(arr.indexOf(-1)); // -1

console.log([NaN].includes(NaN)); // true
console.log([NaN].indexOf(NaN)); // -1
```

### 2. Exponentiation Operator (指数运算符)

这个操作符用于求幂运算，类似于数学中的指数运算。它是 Math.pow() 的简化写法。

```javascript
console.log(Math.pow(2, 3)); // 8
console.log(Math.pow(3, 4)); // 81

console.log(2 ** 3); // 8
console.log(3 ** 4); // 81
```

**重要注意**：这个运算符的结合性是右结合性，计算的时候从右往左。

```javascript
let i = 1;
let o = {
  get a() {
    return ++i;
  },
};
console.log(o.a ** o.a ** o.a);
// 2 ** 3 ** 4
// 2 ** 81
// 2417851639229258349412352
```

---

## ES2017 (ES8)

ES2017 引入了一些关键特性，尤其是 async/await，极大简化了异步编程的难度。

### 1. Async Functions

async 函数使得异步操作变得更加简洁，await 可以暂停 async 函数的执行，等待一个 Promise 被解决，并返回结果。

**对比示例：找到韩梅梅的班主任**

使用 Promise 链式调用：
```javascript
fetch("./stu.json")
  .then((response) => response.json())
  .then((data) => {
    let classId = null;
    for (let student of data.student) {
      if (student.name === "韩梅梅") {
        classId = student.classId;
        break;
      }
    }
    return classId;
  })
  .then((classId) => {
    return fetch("./classes.json")
      .then((response) => response.json())
      .then((data) => {
        let teacherId = null;
        for (let cls of data.classes) {
          if (cls.id === classId) {
            teacherId = cls.teacherId;
            break;
          }
        }
        return teacherId;
      });
  })
  .then((teacherId) => {
    return fetch("./teacher.json")
      .then((response) => response.json())
      .then((data) => {
        for (let teacher of data.teachers) {
          if (teacher.id === teacherId) {
            console.log(`韩梅梅的班主任为${teacher.name}`);
            break;
          }
        }
      });
  })
  .catch((error) => {
    console.error("发生错误:", error);
  });
```

使用 async/await：
```javascript
// 使用 fetch 获取韩梅梅的班级 ID
function getClassId() {
  return fetch("./stu.json")
    .then((response) => response.json())
    .then((data) => {
      for (let i of data.student) {
        if (i.name === "韩梅梅") {
          return i.classId;
        }
      }
    });
}

// 使用 fetch 获取班级对应的老师 ID
function getTeacherId(classId) {
  return fetch("./classes.json")
    .then((response) => response.json())
    .then((data) => {
      for (let i of data.classes) {
        if (i.id === classId) {
          return i.teacherId;
        }
      }
    });
}

// 使用 fetch 获取老师名字
function getTeacherName(teacherId) {
  return fetch("./teacher.json")
    .then((response) => response.json())
    .then((data) => {
      for (let i of data.teachers) {
        if (i.id === teacherId) {
          return i.name;
        }
      }
    });
}

async function getInfo(){
  try{
    let classId = await getClassId();
    let teacherId = await getTeacherId(classId);
    let teacherName = await getTeacherName(teacherId);
  }catch(e){
    console.log(e);
  }
}
```

### 2. Object.entries()

Object.entries() 返回一个给定对象自身可枚举属性的键值对数组。

```javascript
const obj = {
  name: "张三",
  age: 18,
};

console.log(Object.entries(obj)); // [['name', '张三'], ['age', 18]]
```

**和 for...in 的区别**：
1. for...in 会遍历原型链上的属性，Object.entries() 只返回对象自身的属性
2. for...in 只遍历键，Object.entries() 返回键值对数组

### 3. Object.values()

Object.values() 返回一个给定对象自身可枚举属性值的数组。

```javascript
const obj = {
  name: "张三",
  age: 18,
};

console.log(Object.values(obj)); // [ '张三', 18 ]
console.log(Object.keys(obj)); // [ 'name', 'age' ]
console.log(Object.entries(obj)); // [ [ 'name', '张三' ], [ 'age', 18 ] ]
```

### 4. String.prototype.padStart()

padStart() 方法用于在当前字符串的开头填充指定的字符，直到字符串达到指定的长度。

```javascript
console.log("5".padStart(3, "0")); // "005"
console.log("123".padStart(10, "*")); // "*******123"
console.log("123".padStart(10)); // "       123"
```

**应用场景**：格式化数字、字符串，例如为日期、时间或编号填充前导零。

### 5. String.prototype.padEnd()

padEnd() 方法用于在当前字符串的末尾填充指定的字符。

```javascript
console.log("5".padEnd(3, "0")); // "500"
console.log("123".padEnd(10, "*")); // "123*******"
console.log("123".padEnd(10), "test"); // "123       test"
```

**应用场景**：创建固定宽度的输出，在报告生成或格式化输出时有用。

```javascript
// 商品数据
const products = [
  { name: "Apple", price: 1.5, stock: 120 },
  { name: "Banana", price: 0.8, stock: 60 },
  { name: "Watermelon", price: 5.0, stock: 10 },
  { name: "Grapes", price: 2.7, stock: 30 },
];

// 打印表头
console.log("Product".padEnd(15) + "Price".padEnd(10) + "Stock".padEnd(10));

// 打印表格内容
products.forEach((product) => {
  console.log(
    product.name.padEnd(15) +
      product.price.toString().padEnd(10) +
      product.stock.toString().padEnd(10)
  );
});
```

### 6. Object.getOwnPropertyDescriptors()

Object.getOwnPropertyDescriptors() 方法返回一个对象的所有自身属性的描述符。

```javascript
const obj = {
  name: "张三",
  age: 18,
  get getAge() {
    return this.age;
  },
};

const describs = Object.getOwnPropertyDescriptors(obj);
console.log(describs);

/*
输出：
{
  name: { value: '张三', writable: true, enumerable: true, configurable: true },
  age: { value: 18, writable: true, enumerable: true, configurable: true },
  getAge: {
    get: [Function: get getAge],
    set: undefined,
    enumerable: true,
    configurable: true
  }
}
*/
```

### 7. Trailing Commas (尾随逗号)

允许在函数参数列表和调用中的最后一个参数后面添加逗号。

```javascript
function foo(param1, param2,) {
  // 这里的最后一个参数后允许有逗号
}

foo("a", "b",);
```

---

## ES2018 (ES9)

ES2018 引入了一些重要的新特性，尤其是对正则表达式、异步迭代和对象的增强。

### 1. Asynchronous Iteration (for await...of)

ES2018 添加了 for await...of 循环，用于迭代异步可迭代对象。

```javascript
const asyncObj = {
  async *[Symbol.asyncIterator]() {
    yield 1;
    yield 2;
    yield 3;
  },
};

async function proceeAsyncData(data) {
  for await (let item of data) {
    console.log(item);
  }
}

proceeAsyncData(asyncObj);
```

**实际应用场景**：分页获取用户数据

```javascript
// 根据传入的页码数返回对应页码的用户数据
async function fetchUser(page) {
  const pageSize = 10;
  await new Promise((resolve) => setTimeout(resolve, 1500));
  const totalUsers = 45;
  const start = (page - 1) * pageSize;
  const end = Math.min(start + pageSize, totalUsers);
  
  if (start >= totalUsers) return [];
  
  const users = Array.from({ length: end - start }, (_, i) => {
    return {
      id: start + i + 1,
      name: `用户${start + i + 1}`,
    };
  });
  
  return users;
}

// 异步生成器函数：分页获取所有的用户
async function* fetchAllUsers() {
  let page = 1;
  let hasMore = true;
  
  while (hasMore) {
    const users = await fetchUser(page);
    if (users.length === 0) {
      hasMore = false;
    } else {
      yield users;
      page++;
    }
  }
}

async function processAllUsers() {
  let allUsers = [];
  
  for await (let users of fetchAllUsers()) {
    console.log(`当前页的用户数据：`, users);
    allUsers = allUsers.concat(users);
  }
  
  console.log(`所有的用户数据：`, allUsers);
}
processAllUsers();
```

### 2. Rest/Spread Properties for Objects

在 ES2018 中，... 运算符可以用于对象。

**Rest 示例**：
```javascript
const { a, b, ...rest } = { a: 1, b: 2, c: 3, d: 4, e: 5 };
console.log(a, b, rest); // 1 2 { c: 3, d: 4, e: 5 }
```

**Spread 示例**：
```javascript
const o1 = { a: 1, b: 2 };
const o2 = { c: 3, d: 4 };
const merged = { ...o1, ...o2 };
console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }
```

### 3. RegExp Named Capture Groups (命名捕获组)

ES2018 增加了在正则表达式中使用命名捕获组的功能。

```javascript
const regex = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;
const result = regex.exec("2024-09-11");

console.log(result.groups); // { year: '2024', month: '09', day: '11' }
console.log(result.groups.year); // '2024'
console.log(result.groups.month); // '09'
console.log(result.groups.day); // '11'
```

### 4. RegExp Lookbehind Assertions (后行断言)

**前行断言**（ES2018之前）：
1. 正向前行断言 (?=...)：检查某个模式的后面是否有特定字符
2. 负向前行断言 (?!...)：检查某个模式的后面是否没有特定字符

**后行断言**（ES2018新增）：
1. 正向后行断言（?<=...）：确保某个模式前面是否有特定字符
2. 负向后行断言（?<!...）：确保某个模式前面是否没有特定字符

```javascript
// 匹配数字，但是数字前面必须有$
const reg1 = /(?<=\$)\d+/;
console.log("100$456".match(reg1)); // [ '456', index: 4, input: '100$456' ]

// 匹配数字，但是数字前面不能有$
const reg2 = /(?<!\$)\d+/;
console.log("100$456".match(reg2)); // [ '100', index: 0, input: '100$456' ]
```

### 5. RegExp Unicode Property Escapes

ES2018 为正则表达式引入了 Unicode 属性转义。

- \p{ } : 指定对应的 Unicode 字符
- \P{ }: 指定不能有对应的 Unicode 字符

```javascript
const regExp = /\p{Script=Greek}/u;
console.log("α".match(regExp)); // [ 'α', index: 0, input: 'α', groups: undefined ]
console.log("a".match(regExp)); // null

const regExp2 = /\p{Script=Han}/u;
console.log(regExp2.test("你好")); // true

const regExp3 = /\p{Emoji}/u;
console.log(regExp3.test("😄")); // true
```

### 6. s (dotAll) Flag for Regular Expressions

正则表达式中的 . 是一个通配符，用来匹配除换行符之外的任何单个字符。ES2018 引入了 s 标志（也叫 dotAll 模式），让 . 能够匹配包括换行符在内的所有字符。

```javascript
const regex = /foo.bar/s;
const str = "foo\nbar";
console.log(regex.test(str)); // true
```

### 7. Promise.prototype.finally()

Promise.prototype.finally() 方法允许你在 Promise 被处理完（不论是成功还是失败）之后执行代码。

```javascript
fetch("./data.json")
  .then((response) => response.json())
  .catch((error) => console.error("Error:", error))
  .finally(() => console.log("Finished fetching data."));
```

**实际应用场景**：数据加载时的网络请求 + 加载动画

```javascript
function showLoading() {
  console.log("loading...");
}

function hideLoading() {
  console.log("hide loading...");
}

// 请求数据
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const isSuccess = Math.random() > 0.5;
      if (isSuccess) {
        resolve("数据加载成功");
      } else {
        reject("数据加载失败");
      }
    }, 2000);
  });
}

function loadData() {
  showLoading();
  fetchData()
    .then((data) => {
      console.log("成功：", data);
    })
    .catch((err) => {
      console.log("失败：", err);
    })
    .finally(() => {
      // 无论请求成功还是失败都会执行
      hideLoading();
    });
}
loadData();
```

---

## ES2019 (ES10)

ES2019 增强了数组、字符串、对象等常用操作的简洁性和功能性。

### 1. Array.prototype.flat()

flat() 方法用于将嵌套的数组"拍平"，即将多维数组展开拍平。

```javascript
const arr = [1, [2, [3, [4]], 5]];
console.log(arr.flat()); // [1, 2, [3, [4]], 5]
console.log(arr.flat(2)); // [1, 2, 3, [4], 5]
console.log(arr.flat(Infinity)); // [1, 2, 3, 4, 5] 无限展开
```

### 2. Array.prototype.flatMap()

flatMap() 是 map() 和 flat() 的结合体。它首先对数组的每个元素执行一个映射函数，然后将结果"拍平"一层。

```javascript
let arr = [1, 2, 3, 4, 5];
arr = arr.flatMap((x) => [x, x * 2, x * 3]);
console.log(arr); // [1, 2, 3, 2, 4, 6, 3, 6, 9, 4, 8, 12, 5, 10, 15]
```

**优点**：相比于 map().flat() 组合，flatMap() 性能更高，语法也更简洁。

**缺点**：无法指定拍平的层数

### 3. String.prototype.trimStart() / trimEnd()

trimStart() 和 trimEnd() 方法分别用于去除字符串开头和结尾的空白字符。

```javascript
const str = "   Hello World   ";
console.log(str.trimStart()); // "Hello World   "
console.log(str.trimEnd()); // "   Hello World"
```

### 4. Optional catch Binding (可选的 catch 绑定)

ES2019 允许在 catch 块中省略对错误参数的声明。

```javascript
try {
  // 可能出错的代码
} catch {
  console.log("捕获到异常");
}
```

### 5. Object.fromEntries()

Object.fromEntries() 方法是 Object.entries() 的反转。它将一个键值对数组转换为对象。

```javascript
const arr = [
  ["name", "zhangsan"],
  ["age", 20],
];
console.log(Object.fromEntries(arr)); // { name: 'zhangsan', age: 20 }
```

**应用场景**：将 Map 转换为对象

```javascript
const m = new Map([
  ["name", "张三"],
  ["age", 20],
  ["gender", "男"],
]);
console.log(Object.fromEntries(m)); // { name: '张三', age: 20, gender: '男' }
```

### 6. Symbol.prototype.description

ES2019 为 Symbol 添加了 description 属性，允许你访问 Symbol 创建时传递的描述信息。

```javascript
const sym = Symbol("这是一个简单的Symbol");
console.log(sym.description); // "这是一个简单的Symbol"
```

### 7. Function.prototype.toString() Revision

ES2019 修订了 Function.prototype.toString() 方法，规定该方法必须返回函数的准确源代码，包括注释、空格和格式化。

```javascript
function example() {
  // 这是一个注释
  return "Hello, world!";
}

console.log(example.toString());
/*
function example() {
  // 这是一个注释
  return "Hello, world!";
}
*/
```

### 8. Well-formed JSON.stringify()

ES2019 修复了 JSON.stringify() 在处理某些特定 Unicode 字符时生成无效 JSON 的问题。例如，U+2028 和 U+2029（段落分隔符和行分隔符）现在会被正确转义。

```javascript
const str = "Hello World"; // 包含特殊字符的字符串
const jsonstr = JSON.stringify(str);
console.log(jsonstr); // "Hello World"
```

---

## ES2020 (ES11)

ES2020 推出了一些新的特性，增强了 JS 对异步编程、模块化、对象访问等方面的支持。

### 1. BigInt

BigInt 是一种新的基础数据类型，用于表示任意精度的整数。

**BigInt 字面量表示**：在一串数字后面跟小写字母 n

```javascript
1234n
0b10110010n  // 二进制
0o777n       // 八进制
0x800AFn     // 十六进制
```

**类型转换和计算**：

```javascript
console.log(BigInt(Number.MAX_SAFE_INTEGER));
console.log(2 ** 53 - 1);

let str = "1" + "0".repeat(10);
console.log(BigInt(str));

// 除法会丢弃余数并向下舍入
console.log(5 / 2);    // 2.5
console.log(5n / 2n);  // 2n
```

**注意事项**：
- 不能混用 BigInt 操作数和常规数值操作数进行算术运算
- 允许 BigInt 操作数和常规数值做比较运算
- Math 的任何方法均不接受 BigInt 操作数

### 2. Dynamic import()

import() 语法允许按需动态加载模块。

```javascript
async function loadChartLibrary() {
  if (conditionToLoadChart) {
    const { Chart } = await import("chart.js");
    const chart = new Chart(ctx, {
      /* chart configuration */
    });
  }
}
```

**路由懒加载示例**：

```javascript
// 之前：同步导入，所有模块会在应用启动时一并加载
import HomeView from './views/HomeView.vue';
import AboutView from './views/AboutView.vue';

// 之后：按需加载，只有当用户访问某个特定路由时才会加载对应的组件
const routes = [
  { path: '/', component: () => import('./views/HomeView.vue') },
  { path: '/about', component: () => import('./views/AboutView.vue') }
];
```

### 3. globalThis

globalThis 是一个跨平台的全局对象，统一了不同环境下的全局对象访问方式。

- 在浏览器中，它等同于 window
- 在 Node.js 中等同于 global

### 4. Nullish Coalescing Operator (??) (空值合并运算符)

?? 运算符是一个逻辑运算符，当左侧操作数为 null 或 undefined 时，返回右侧操作数。否则，返回左侧操作数。

```javascript
// 使用 || 逻辑运算符：会将所有的"假值"视为无效
console.log(0 || 10);       // 10
console.log('' || 10);      // 10
console.log(false || 10);  // 10

// 使用 ?? 逻辑运算符：只处理 null 和 undefined
console.log(0 ?? 10);       // 0
console.log('' ?? 10);      // ''
console.log(false ?? 10);   // false
console.log(undefined ?? 10); // 10
console.log(null ?? 10);     // 10
```

### 5. Optional Chaining (?.) (可选链操作符)

可选链操作符 ?. 允许你安全地访问对象嵌套的属性或调用方法。

```javascript
const user = {
  address: {
    street: "123 Main St",
  },
};

console.log(user?.address?.street); // "123 Main St"
console.log(user?.contact?.email);  // undefined
// console.log(user.contact.email);  // TypeError: Cannot read properties of undefined
```

### 6. Promise.allSettled()

Promise.allSettled() 方法返回一个在所有给定的 Promise 都已经解决或拒绝后解决的 Promise。

```javascript
const promises = [
  Promise.resolve(1),
  Promise.reject("Error"),
  Promise.resolve(3),
];

Promise.allSettled(promises).then((results) => {
  console.log(results);
  // 输出: [
  //   {status: 'fulfilled', value: 1}, 
  //   {status: 'rejected', reason: 'Error'}, 
  //   {status: 'fulfilled', value: 3}
  // ]
});
```

**实际应用场景**：仪表盘从多个服务获取数据

```javascript
// 模拟API请求
const fetchUserProfile = () =>
  new Promise((resolve) =>
    setTimeout(() => resolve({ id: 1, name: "Alice" }), 1000)
  );

const fetchWeatherData = () =>
  new Promise((resolve, reject) =>
    setTimeout(() => reject("天气API请求失败"), 3000)
  );

const fetchNotifications = () =>
  new Promise((resolve) =>
    setTimeout(() => resolve([{ id: 1, message: "新消息" }]), 500)
  );

Promise.allSettled([
  fetchUserProfile(),
  fetchWeatherData(),
  fetchNotifications(),
]).then((results) => {
  results.forEach((result, index) => {
    if (result.status === "fulfilled") {
      console.log(`Promise ${index + 1} 成功`, result.value);
    } else {
      console.log(`Promise ${index + 1} 失败`, result.reason);
    }
  });
});
```

### 7. for-in Loop Order Guarantee

ES2020 标准化了 for-in 的遍历顺序：
1. 首先遍历整数属性（按升序）
2. 然后遍历字符串键
3. 最后遍历符号属性

```javascript
const obj = {
  2: "Second",
  1: "First",
  b: "Letter B",
  a: "Letter A",
  [Symbol("symbolKey")]: "Symbol Key",
};

for (let key in obj) {
  console.log(key);
}
// 输出：
// 1
// 2
// b
// a
```

### 8. import.meta

import.meta 是 ES 模块的一部分，它为开发者提供了一种访问当前模块元数据的方法。

```html
<body>
  <script type="module" src="./index.js"></script>
</body>
```

```javascript
console.log(import.meta);
// {url: 'http://127.0.0.1:5500/index.js', resolve: ƒ}
```

**应用场景**：
- Vite 中针对别名的配置
- Vite 中访问环境变量

### 9. String.prototype.matchAll()

String.prototype.matchAll() 允许你对字符串进行全局正则匹配，并返回包含所有的匹配结果的迭代器。

```javascript
const text = "Hello world! Welcome to JavaScript.";
const reg = /\b(\w+)\b/g;

const matches = text.matchAll(reg);
for (const m of matches) {
  console.log(m);
}
```

**实际场景示例**：提取文本中的所有 URL

```javascript
const text = `
  Visit us at https://www.example.com or follow our blog at http://blog.example.com!
  You can also check https://support.example.com for more information.
`;

const regex = /(https?):\/\/([^\s/$.?#].[^\s!]*)/g;

text.matchAll(regex).forEach((m) => {
  console.log(`完整的 URL: ${m[0]}`);   // 完整的匹配结果
  console.log(`协议: ${m[1]}`);         // 捕获的协议 (http 或 https)
  console.log(`域名: ${m[2]}`);         // 捕获的域名部分
});
```

### 10. Module Namespace Exports

ES2020 为模块导出添加了对命名空间导出的标准化。

```javascript
// 在 module.js 中
export const a = 1;
export const b = 2;

// 在 main.js 中
import * as moduleNamespace from "./module.js";
console.log(moduleNamespace.a); // 1
console.log(moduleNamespace.b); // 2
```

---

## ES2021 (ES12)

ES2021 引入了一些实用的特性，包括逻辑赋值运算符、字符串方法、Promise 方法和国际化增强。

### 1. 逻辑赋值运算符 (&&=, ||=, ??=)

逻辑赋值运算符结合了逻辑运算符和赋值操作。

**&&=**：仅当左操作数为真值时，才对其进行赋值

```javascript
let user = { isLogin: true };
user.isLogin &&= "admin";
console.log(user.isLogin); // "admin"
```

**||=**：仅当左操作数为假值时，才对其进行赋值

```javascript
let setting = { theme: "" };
setting.theme ||= "dark";
console.log(setting.theme); // "dark"
```

**??=**：仅当左操作数为 null 或 undefined 时，才对其进行赋值

```javascript
let x = undefined;
x ??= "default";
console.log(x); // "default"
```

### 2. String.prototype.replaceAll()

replaceAll() 方法允许你替换字符串中所有匹配项。

```javascript
const str = "hello world, hello javascript";
console.log(str.replaceAll("hello", "hi")); // "hi world, hi javascript"
```

### 3. AggregateError

AggregateError 是一种新的错误类型，用于表示多个错误的集合。

```javascript
const aggreErrors = new AggregateError(
  [new Error("请求API出错"), new Error("数据库连接错误")],
  "发生了多个错误"
);
console.log(aggreErrors.message);
console.log(aggreErrors.errors);
```

### 4. Promise.any()

Promise.any() 接受一组 Promise，只要有一个 Promise 成功，它就会立即返回这个结果。如果所有 Promise 都失败，它会返回一个失败的 AggregateError。

```javascript
const p1 = Promise.reject(1);
const p2 = Promise.resolve(2);
const p3 = Promise.resolve(3);

Promise.any([p1, p2, p3])
  .then((result) => {
    console.log(result); // 2
  })
  .catch((err) => {
    console.log(err);
  });
```

**实际应用场景**：从多个 CDN 加载资源

```javascript
// 模拟从多个 CDN 加载 jQuery
const loadFromCDN1 = () =>
  new Promise((reject) => setTimeout(() => reject("CDN 1 failed"), 1000));
const loadFromCDN2 = () =>
  new Promise((resolve) => setTimeout(() => resolve("CDN 2: Loaded jQuery"), 1500));
const loadFromCDN3 = () =>
  new Promise((resolve) => setTimeout(() => resolve("CDN 3: Loaded jQuery"), 2000));

Promise.any([loadFromCDN1(), loadFromCDN2(), loadFromCDN3()])
  .then((value) => {
    console.log(value);
    init();
  })
  .catch((err) => {
    console.log(err);
  });

function init() {
  console.log("jQuery is ready");
}
```

### 5. 数字分隔符

数字分隔符（下划线 _）用于使大型数字更加可读。

```javascript
const largeNum = 1_000_000_000;
console.log(largeNum); // 1000000000
```

### 6. WeakRefs 和 FinalizationRegistry

**WeakRef** 允许你创建对对象的弱引用，不会阻止该对象被垃圾回收机制回收。

```javascript
let obj = { name: "张三" };
const objWeakRef = new WeakRef(obj);

console.log(objWeakRef.deref()); // { name: '张三' }

obj = null; // 释放 obj 的引用
// 垃圾回收器会回收 obj
console.log(objWeakRef.deref()); // 可能返回 undefined
```

**FinalizationRegistry** 允许在对象被垃圾回收后执行某些回调逻辑。

```javascript
class DatabaseConnection {
  constructor(name) {
    this.name = name;
    this.isConnected = true;
    console.log(`已连接至 ${this.name} 数据库`);
  }

  close() {
    this.isConnected = false;
    console.log(`已关闭 ${this.name} 数据库`);
  }
}

const databaseConnection = new DatabaseConnection("MongoDB");

const reg = new FinalizationRegistry((heldValue) => {
  heldValue.close();
});

reg.register(databaseConnection, databaseConnection);
```

### 7. Intl.ListFormat

Intl.ListFormat 是一个国际化 API，用于根据语言环境来格式化列表。

```javascript
const javaScriptFrameworks = ["React", "Vue", "Angular"];

const shortUnitFormat = new Intl.ListFormat("en", {
  style: "short",
  type: "unit",
});
console.log(shortUnitFormat.format(javaScriptFrameworks)); // "Angular, React, Vue"

const longConjunctionFormatter = new Intl.ListFormat("en", {
  style: "long",
  type: "conjunction",
});
console.log(longConjunctionFormatter.format(javaScriptFrameworks)); // "Angular, React, and Vue"
```

### 8. Intl.DateTimeFormat 新选项

ES2021 中引入了两个新的选项：
- **dateStyle**：指定日期显示格式（full、long、medium、short）
- **timeStyle**：指定时间的显示格式（full、long、medium、short）

```javascript
const date = new Date();

const formatter = new Intl.DateTimeFormat("en-US", {
  dateStyle: "medium",
  timeStyle: "short",
});
console.log(formatter.format(date)); // "Sep 12, 2024, 12:04 PM"
```

---

## ES2022 (ES13)

ES2022 的新特性涵盖了数组、类、错误处理、异步操作等多个领域。

### 1. at() 方法

at() 方法为数组、字符串等可索引的数据类型提供了一种更简洁、安全的方式来访问元素，支持正索引和负索引。

```javascript
const arr = [10, 20, 30, 40, 50];
console.log(arr.at(0));   // 10
console.log(arr.at(2));   // 30
console.log(arr.at(-1));  // 50

const str = "Hello";
console.log(str.at(0));   // "H"
console.log(str.at(-1));  // "o"
```

### 2. 类字段和私有成员

ES2022 允许开发者在类中直接定义字段，并正式推出了原生私有成员的书写方式。

**类字段**：
```javascript
class Person {
  name = "张三";
  age = 18;
  
  sayHello() {
    console.log(`Hello, I'm ${this.name}`);
  }
}

const p = new Person();
p.sayHello(); // "Hello, I'm 张三"
```

**私有成员**（使用 # 表示）：
```javascript
class Person {
  #name;
  #age;

  constructor(name, age) {
    this.#name = name;
    this.#age = age;
  }

  get name() {
    return this.#name;
  }
}

const p = new Person("张三", 20);
console.log(p.name); // "张三"
console.log(p.#name); // 报错：私有成员不能在类外部访问
```

### 3. Error.cause

ES2022 引入了 Error.cause 属性，为 Error 对象提供了一种标准化的方式来传递额外的上下文信息。

```javascript
try {
  throw new Error("出错了", { cause: "因为网络原因" });
} catch (e) {
  console.log(e.message); // "出错了"
  console.log(e.cause);    // "因为网络原因"
}
```

### 4. Top-Level await

ES2022 允许在模块的顶级作用域中使用 await，无需再将 await 包含在 async 函数内。

```javascript
const response = await fetch("https://jsonplaceholder.typicode.com/todos/1");
const data = await response.json();
console.log(data);
```

### 5. Object.hasOwn()

Object.hasOwn() 是 Object.prototype.hasOwnProperty() 的替代方案，避免了重写问题。

```javascript
const obj = {
  name: "张三",
  hasOwnProperty() {
    return "hello";
  },
};

// 旧方式（会被重写影响）
console.log(obj.hasOwnProperty("name")); // "hello"

// 新方式（不受重写影响）
const result = Object.hasOwn(obj, "name");
console.log(result); // true
```

### 6. RegExp Match Indices

通过在正则表达式中使用 d 标志，可以获取到详细的匹配位置。

```javascript
const regex = /(?<first>foo)(?<second>bar)?/d;
const match = regex.exec("foobarfoo");

console.log(match.groups);      // { first: 'foo', second: 'bar' }
console.log(match.indices);      // [[0, 6], [0, 3], [3, 6]]
```

**实际应用场景**：语法高亮

```javascript
const reg = /(let|const|var)/d;
const code = "let x = 10";
const match = reg.exec(code);

if (match) {
  const [start, end] = match.indices[0];
  console.log(
    `高亮位置：${code.slice(start, end)} 位于第 ${start} 到第 ${end} 个字符之间`
  );
}
```

---

## ES2023 (ES14)

ES2023 带来的新特性为数组、Set 和弱引用等提供了强大的功能。

### 1. findLast() 和 findLastIndex()

findLast() 和 findLastIndex() 为数组提供了一种从右到左查找元素的方法。

```javascript
const arr = [11, 24, 13, 74, 55];

console.log(arr.find((x) => x % 2 === 0));        // 24
console.log(arr.findIndex((x) => x % 2 === 0));    // 1

console.log(arr.findLast((x) => x % 2 === 0));    // 74
console.log(arr.findLastIndex((x) => x % 2 === 0)); // 3
```

### 2. Hashbang 语法

ES2023 引入了 Hashbang Grammar 特性，允许 JS 文件在文件的第一行包含 hashbang（#!）。

```javascript
#!/usr/bin/env node
console.log('Hello, World!');
```

在 Unix/Linux 系统中执行：
```bash
chmod +x ./index.js
./index.js
```

### 3. Change Array by Copy Methods

新增了一些数组方法，这些方法在不修改原数组的情况下返回操作后的新数组。

```javascript
const arr = [1, 2, 3, 4, 5];

console.log(arr.toReversed());  // [5, 4, 3, 2, 1]
console.log(arr);               // [1, 2, 3, 4, 5] (原数组不变)

console.log(arr.toSorted((a, b) => b - a));  // [5, 4, 3, 2, 1]
console.log(arr.toSpliced(1, 3));            // [1, 5]
console.log(arr.with(0, 100));                // [100, 2, 3, 4, 5]
```

### 4. Set 方法的增强

ES2023 新增了一些适用于 Set 数据结构的方法：

```javascript
const setA = new Set([1, 2, 3]);
const setB = new Set([3, 4, 5]);

console.log(setA.intersection(setB));            // Set { 3 } 交集
console.log(setA.union(setB));                   // Set { 1, 2, 3, 4, 5 } 并集
console.log(setA.difference(setB));              // Set { 1, 2 } 差集
console.log(setA.symmetricDifference(setB));     // Set { 1, 2, 4, 5 } 对称差集
console.log(setA.isDisjointFrom(setB));          // false 是否不相交
```

### 5. Symbols as WeakMap keys

ES2023 引入了这一特性，未注册的 Symbol 可以作为 WeakMap 和 WeakSet 的键。

```javascript
let s = Symbol("foo"); // 未注册的 Symbol
let obj = { name: "bar" };
let wm = new WeakMap();

wm.set(s, obj);
console.log(wm.get(s)); // { name: 'bar' }
```

注意：注册的 Symbol（通过 Symbol.for() 注册的符号）仍然不能作为键。

```javascript
let s = Symbol.for("foo"); // 注册的 Symbol
let wm = new WeakMap();
wm.set(s, obj); // TypeError: Invalid value used as weak map key
```

---

## ES2024 (ES15)

ES2024 引入了一些实用的特性，包括分组功能、Promise 增强和正则表达式改进。

### 1. GroupBy 分组

GroupBy 为开发者提供了一种强大的分组功能，允许根据特定的回调函数对数组中的元素进行分组。

**Object.groupBy()**：返回一个对象

```javascript
const fruits = [
  { name: "apple", type: "pome" },
  { name: "banana", type: "berry" },
  { name: "cherry", type: "drupe" },
  { name: "pear", type: "pome" },
];

const groupedFruits = Object.groupBy(fruits, (f) => f.type);
console.log(groupedFruits);
// {
//   pome: [{ name: 'apple', type: 'pome' }, { name: 'pear', type: 'pome' }],
//   berry: [{ name: 'banana', type: 'berry' }],
//   drupe: [{ name: 'cherry', type: 'drupe' }]
// }
```

**Map.groupBy()**：返回一个 Map

```javascript
const arr = [6.1, 4.2, 6.3];
const grouped = Map.groupBy(arr, Math.floor);
console.log(grouped);
// Map { 6 => [ 6.1, 6.3 ], 4 => [ 4.2 ] }
```

### 2. Promise.withResolvers

Promise.withResolvers 是一个静态方法，旨在简化创建 Promise 及其关联的 resolve 和 reject 函数的过程。

```javascript
const { promise, resolve, reject } = Promise.withResolvers();

// 后期可以手动控制何时进行 resolve 或 reject
resolve("成功");
```

**实际应用场景**：多个 API 请求

```javascript
function fetchMutipleAPIs(apiEndpoints) {
  const { promise, resolve, reject } = Promise.withResolvers();
  
  let completed = 0;
  const total = apiEndpoints.length;
  const results = [];
  
  apiEndpoints.forEach((endPoint, index) => {
    fetchDataFromURL(endPoint)
      .then((data) => {
        results[index] = data;
        completed++;
        if (completed === total) {
          resolve(results);
        }
      })
      .catch((e) => {
        console.log("请求失败 error:", e.message);
      });
  });
  
  return promise;
}
```

### 3. 正则表达式的 /v 标志

/v 标志代表 Unicode Sets 模式，提供了一种更强大、更直观的方式来处理 Unicode 字符集。

**字符集操作**：支持集合的并集、交集和差集

```javascript
// 并集 ||
const regex1 = /[\p{Script=Latin}||\p{Script=Cyrillic}]/v;

// 交集 &&
const regex2 = /[\p{Alphabetic}&&\p{Script=Latin}]/v;

// 差集 --
const regex3 = /[\p{Script=Latin}--\p{Lowercase_Letter}]/v;
```

**嵌套的字符类**：

```javascript
const regex = /[[a-f]&&[d-z]]/v;
console.log(regex.test("e")); // true
console.log(regex.test("b")); // false
```

### ES2024 其他新特性

- 可调整大小的 ArrayBuffer
- Atomics.waitAsync() 方法
- 字符串 isWellFormed() 和 toWellFormed() 方法

---

## 总结

从 ES2016 到 ES2024，JavaScript 持续演进，不断引入新的特性和改进：

- **ES2016**: 数组 includes 方法和指数运算符
- **ES2017**: async/await、对象方法增强
- **ES2018**: 异步迭代、对象扩展运算符、正则表达式增强
- **ES2019**: 数组拍平、字符串修剪、Object.fromEntries
- **ES2020**: BigInt、动态导入、可选链、空值合并
- **ES2021**: 逻辑赋值运算符、Promise.any、国际化增强
- **ES2022**: 类字段、私有成员、Error.cause、Top-Level await
- **ES2023**: 数组查找方法、Set 增强、Symbol 作为 WeakMap 键
- **ES2024**: GroupBy 分组、Promise.withResolvers、正则 /v 标志

这些新特性使 JavaScript 代码更加简洁、高效和易于维护，为开发者提供了更强大的工具来构建现代化的 Web 应用。