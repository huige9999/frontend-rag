# 04 - IndexedDB

## 一、IndexedDB 简介

IndexedDB 是浏览器内置的**异步 NoSQL 数据库**，用于在客户端持久化存储大量结构化数据（包括文件/二进制对象）。它比 localStorage 容量大得多，且支持事务、索引等数据库特性。

核心概念：
- **Database**：数据库，通过 `indexedDB.open()` 打开或创建
- **Object Store**：对象仓库，类似数据库中的"表"
- **Transaction**：事务，所有读写操作都必须在事务中完成
- **Key**：每条记录的唯一标识，可以手动指定，也可以自增

---

## 二、打开/创建数据库

```js
// 打开名为 "test" 的数据库，版本号为 1
// 如果数据库不存在则自动创建；如果版本号高于已有版本则触发 onupgradeneeded
const request = window.indexedDB.open("test", 1);
```

三个关键回调：

```js
// 1. 版本变更时触发（首次创建或版本升级）—— 建表/改表的唯一时机
request.onupgradeneeded = function (e) {
  const db = e.target.result;
  // 在这里创建 Object Store
};

// 2. 数据库连接成功
request.onsuccess = function (e) {
  const db = e.target.result;
  // 在这里进行增删改查操作
};

// 3. 连接失败
request.onerror = function (e) {
  console.log("数据库连接失败", e.target.error);
};
```

---

## 三、创建 Object Store（建表）

`onupgradeneeded` 是创建/修改 Object Store 的**唯一时机**。

```js
request.onupgradeneeded = function (e) {
  const db = e.target.result;
  // 创建名为 "user" 的对象仓库
  const store = db.createObjectStore("user");
};
```

避免重复创建的写法：

```js
request.onupgradeneeded = function (e) {
  const db = e.target.result;
  // 先判断是否已存在
  if (!db.objectStoreNames.contains("user")) {
    db.createObjectStore("user");
  }
};
```

---

## 四、事务与增删改查（CRUD）

所有数据操作都必须通过 **事务（Transaction）** 完成。

### 4.1 创建事务

```js
// 参数1：涉及的 Object Store 名称数组
// 参数2：事务模式，"readwrite" 可读写，"readonly" 只读（默认）
const transaction = db.transaction(["user"], "readwrite");
const store = transaction.objectStore("user");
```

### 4.2 增 —— `store.add()`

```js
// 添加数据，第二个参数是指定主键（key）
store.add({ name: "蔡徐坤", age: 30 }, "用户1");
```

### 4.3 删 —— `store.delete()`

```js
// 根据主键删除记录
store.delete("用户1");
```

### 4.4 改 —— `store.put()`

```js
// put：如果主键存在则更新，不存在则插入（upsert）
store.put({ name: "马保国", age: 60 }, "用户6");
```

### 4.5 查 —— `store.getAll()` / `store.get()`

```js
// 获取全部记录
const r = store.getAll();
r.onsuccess = function (e) {
  console.log(e.target.result); // 返回数组
};

// 也可以根据主键查单条：store.get("用户1")
```

---

## 五、综合示例：用户管理（含文件存储）

以下示例演示 IndexedDB 存储用户信息，包括 **File 对象（头像图片）** 的持久化。

### 5.1 添加用户（含文件）

```js
// 从 input 获取文件对象
const user = {
  name: username.value,
  avatar: avatar.files[0],  // File 对象可以直接存入 IndexedDB
};

const transaction = db.transaction("user", "readwrite");
const store = transaction.objectStore("user");

// 以用户名作为主键存入
const r = store.add(user, user.name);
r.onsuccess = function () {
  console.log("添加成功");
};
```

> **要点**：IndexedDB 原生支持存储 Blob / File 等二进制对象，无需转 Base64。

### 5.2 查询并渲染（读取文件 + FileReader）

```js
const transaction = db.transaction("user");
const store = transaction.objectStore("user");
const r = store.getAll();

r.onsuccess = function (e) {
  const list = e.target.result;
  if (list !== null) {
    list.forEach(({ name, avatar }) => {
      const tr = document.createElement("tr");
      tr.innerHTML = `<td>${name}</td>`;

      // 使用 FileReader 将 File/Blob 对象转为 Data URL 用于显示
      const fr = new FileReader();
      fr.readAsDataURL(avatar);
      fr.onload = function (e) {
        tr.insertAdjacentHTML(
          "beforeend",
          `<td><img src="${e.target.result}" /></td>`
        );
      };
    });
  }
};
```

> **要点**：从 IndexedDB 读出的 File 对象不能直接当图片 src 使用，需通过 `FileReader.readAsDataURL()` 转换后才能在 `<img>` 中展示。

---

## 六、核心 API 速查

| API | 说明 |
|-----|------|
| `indexedDB.open(name, version)` | 打开/创建数据库 |
| `db.createObjectStore(name)` | 创建对象仓库（仅 onupgradeneeded 中可用） |
| `db.objectStoreNames.contains(name)` | 判断仓库是否已存在 |
| `db.transaction(storeNames, mode)` | 创建事务 |
| `transaction.objectStore(name)` | 获取对象仓库 |
| `store.add(value, key)` | 添加记录 |
| `store.put(value, key)` | 更新/插入记录 |
| `store.delete(key)` | 删除记录 |
| `store.get(key)` | 按主键查询单条 |
| `store.getAll()` | 获取全部记录 |

---

## 七、注意事项

1. **onupgradeneeded 是唯一可以修改数据库结构的地方**（创建/删除 Object Store、创建索引等）
2. **所有读写操作必须在事务中进行**，事务模式分为 `readonly` 和 `readwrite`
3. **IndexedDB 操作全部是异步的**，通过 `onsuccess` / `onerror` 回调获取结果
4. **主键（Key）必须唯一**，`add()` 时如果主键重复会报错；`put()` 则会覆盖
5. **支持存储复杂类型**：对象、数组、Blob、File 等均可直接存储，无需序列化
6. **同源策略**：每个数据库只能在创建它的同源页面中访问
