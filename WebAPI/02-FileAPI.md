# 02 - File API

## 一、Blob 对象

Blob（Binary Large Object）表示一个不可变的原始数据块。它可以用来存储任意类型的数据（文本、二进制等），是 File 对象的底层基础。

**核心要点：**
- 通过 `new Blob(数组, 配置项)` 创建
- `slice(start, end)` 可切割 Blob，返回新的 Blob
- `text()` 方法返回 Promise，将 Blob 内容解析为文本

```js
// 创建 Blob：第一个参数是数组，第二个参数指定 MIME 类型
const blob = new Blob(["I am a teacher"], { type: "text/plain" });
console.log(blob); // Blob { size: 14, type: "text/plain" }

// 使用 slice 切割 Blob
const length = 4;
const firBlob = blob.slice(0, length);   // 前4个字节
const lastBlob = blob.slice(length);      // 剩余部分

// text() 异步读取 Blob 内容
Promise.all([firBlob.text(), lastBlob.text()]).then((val) => {
  console.log(val); // ["I am", " a teacher"]
});
```

**Blob 常用属性与方法：**

| 属性/方法 | 说明 |
|-----------|------|
| `size` | 数据大小（字节） |
| `type` | MIME 类型 |
| `slice(start, end, type)` | 切割返回新 Blob |
| `text()` | 返回 Promise，解析为文本 |
| `arrayBuffer()` | 返回 Promise，解析为 ArrayBuffer |

## 二、File 对象

File 对象继承自 Blob，表示用户系统中的一个文件。获取 File 对象有三种方式。

**核心要点：**
- `new File(数组, 文件名, 配置)` 可手动创建
- `<input type="file">` 的 `change` 事件中通过 `e.target.files` 获取
- 拖拽释放时通过 `e.dataTransfer.files` 获取

### 2.1 手动创建 File

```js
const file = new File(["我是一个文本内容"], "文本.txt", {
  type: "text/plain",
});
console.log(file); // File { name: "文本.txt", size: 24, type: "text/plain" }
```

### 2.2 通过文件输入框获取

```html
<!-- multiple 属性允许选择多个文件 -->
<input type="file" multiple />
```

```js
const file = document.querySelector("input");
file.addEventListener("change", function (e) {
  console.log(e.target.files); // FileList 伪数组
});
```

### 2.3 通过拖拽获取

```html
<div class="drop-content"></div>
```

```js
const dropContent = document.querySelector(".drop-content");
// 必须阻止 dragover 默认行为，否则 drop 不会触发
dropContent.addEventListener("dragover", (e) => e.preventDefault());
dropContent.addEventListener("drop", function (e) {
  e.preventDefault();
  console.log(e.dataTransfer.files); // FileList 伪数组
});
```

**File 对象额外属性（相比 Blob）：**

| 属性 | 说明 |
|------|------|
| `name` | 文件名 |
| `lastModified` | 最后修改时间戳 |
| `webkitRelativePath` | 文件的相对路径（目录上传时有效） |

## 三、FileReader

FileReader 用于异步读取 File / Blob 对象的内容，支持多种读取格式。

**核心要点：**
- 创建实例：`new FileReader()`
- 读取完成触发 `load` 事件，结果在 `e.target.result`
- 不同的 `readAsXxx` 方法决定输出格式

### 3.1 读取文本内容

```html
<input type="file" />
```

```js
const fileDom = document.querySelector("[type=file]");
fileDom.addEventListener("change", function (e) {
  const fr = new FileReader();
  fr.addEventListener("load", function (e) {
    console.log(e.target.result); // 文本内容字符串
  });
  // 以文本方式读取
  fr.readAsText(e.target.files[0]);
});
```

### 3.2 读取图片为 DataURL

```js
const fr = new FileReader();
fr.addEventListener("load", function (e) {
  console.log(e.target.result); // base64 格式的 DataURL 字符串
  const img = new Image();
  img.src = e.target.result;
  document.body.appendChild(img);
});
// 以 DataURL 方式读取，适合图片预览
fr.readAsDataURL(e.target.files[0]);
```

**FileReader 常用方法：**

| 方法 | 说明 |
|------|------|
| `readAsText(file)` | 以文本方式读取 |
| `readAsDataURL(file)` | 以 Base64 DataURL 方式读取（适合图片预览） |
| `readAsArrayBuffer(file)` | 以 ArrayBuffer 方式读取（适合二进制处理） |
| `readAsBinaryString(file)` | 以二进制字符串方式读取（已废弃） |
| `abort()` | 中断读取 |

**FileReader 常用事件：**

| 事件 | 说明 |
|------|------|
| `load` | 读取完成 |
| `loadstart` | 开始读取 |
| `loadend` | 读取结束（无论成功失败） |
| `error` | 读取失败 |
| `progress` | 读取进度 |

## 四、文件上传实战

综合运用 File API，实现一个支持多种方式选择文件、拖拽上传、展示文件列表的完整功能。

### 4.1 HTML 结构

```html
<div class="container">
  <!-- 拖拽区域 -->
  <div class="drop-content">将目录或多个文件拖拽到此进行扫描</div>

  <!-- 操作按钮 -->
  <div class="upload-btns">
    <div class="scan-file">扫描文件</div>
    <div class="scan-dir">扫描目录</div>
    <div class="upload">上传文件</div>
  </div>

  <!-- 文件列表表格 -->
  <table>
    <thead>
      <tr>
        <th>名称</th>
        <th>文件目录</th>
        <th>类型</th>
        <th>文件大小</th>
        <th>操作</th>
      </tr>
    </thead>
    <tbody class="list"></tbody>
  </table>

  <!-- 隐藏的文件输入框 -->
  <input type="file" class="file" multiple />
  <input type="file" webkitdirectory class="dir" />
</div>
```

**关键点：**
- `multiple` 属性允许选择多个文件
- `webkitdirectory` 属性允许选择整个文件夹
- input 隐藏，通过按钮的 click 间接触发

### 4.2 单/多文件与文件夹上传

```js
const tempFileList = []; // 临时文件列表

// 点击"扫描文件"按钮 → 触发隐藏的 file input
scanFile.addEventListener("click", function () {
  fileInput.click();
});

// 文件选择后，将 FileList 展开存入数组并渲染
fileInput.addEventListener("change", function (e) {
  tempFileList.push(...e.target.files);
  renderFilelist();
});

// 点击"扫描目录"按钮 → 触发隐藏的 dir input（带 webkitdirectory）
scanDir.addEventListener("click", function () {
  fileInputDir.click();
});

fileInputDir.addEventListener("change", function (e) {
  tempFileList.push(...e.target.files); // 文件夹内文件的 webkitRelativePath 有值
  renderFilelist();
});
```

### 4.3 拖拽上传（递归读取文件夹）

拖拽上传的核心在于通过 `webkitGetAsEntry()` 获取文件系统入口（Entry），然后递归读取。

```js
dropContent.addEventListener("dragover", (e) => e.preventDefault());
dropContent.addEventListener("drop", (e) => {
  e.preventDefault();
  for (const item of e.dataTransfer.items) {
    // 将 DataTransferItem 转为 FileSystemEntry
    getFileByEntry(item.webkitGetAsEntry());
  }
});

// 递归函数：区分文件和文件夹
function getFileByEntry(entry, path = "") {
  if (entry.isFile) {
    // 是文件：直接获取 File 对象
    entry.file((file) => {
      file.path = `${path}${file.name}`; // 自定义路径属性，方便显示
      tempFileList.push(file);
      renderFilelist();
    });
  } else {
    // 是文件夹：创建读取器，读取子条目后递归处理
    const reader = entry.createReader();
    reader.readEntries((entries) => {
      for (const item of entries) {
        getFileByEntry(item, `${path}${entry.name}/`);
      }
    });
  }
}
```

**拖拽上传流程：**
1. `e.dataTransfer.items` 获取拖拽项
2. `item.webkitGetAsEntry()` 转为 `FileSystemEntry`
3. `entry.isFile` 判断是文件还是目录
4. 文件：`entry.file(callback)` 获取 File 对象
5. 目录：`entry.createReader().readEntries(callback)` 读取子条目，递归处理

### 4.4 文件列表渲染与大小转换

```js
function renderFilelist() {
  list.innerHTML = "";
  tempFileList.forEach((file, index) => {
    const tr = document.createElement("tr");
    list.appendChild(tr);
    tr.innerHTML = `
      <td>${file.name}</td>
      <td>${file.webkitRelativePath || file.path}</td>
      <td>${file.type}</td>
      <td>${transformByte(file.size)}</td>
      <td onclick=delFile(${index})>删除</td>
    `;
  });
}

// 文件大小单位转换
function transformByte(size) {
  if (size < 1024 ** 2) {
    return (size / 1024).toFixed(1) + "KB";
  } else if (size < 1024 ** 3) {
    return (size / 1024 ** 2).toFixed(1) + "MB";
  } else {
    return (size / 1024 ** 3).toFixed(1) + "GB";
  }
}

// 删除文件
function delFile(index) {
  tempFileList.splice(index, 1);
  renderFilelist();
}
```

## 五、知识总结

```
File API 核心对象关系：
  Blob（二进制数据块）
    └── File（继承 Blob，增加 name、lastModified 等属性）

File 的三种获取方式：
  1. new File()                    → 手动创建
  2. <input type="file"> change    → 文件选择框
  3. e.dataTransfer.files          → 拖拽释放

FileReader 读取方式：
  - readAsText()        → 文本
  - readAsDataURL()     → Base64 图片预览
  - readAsArrayBuffer() → 二进制

文件夹上传核心：
  - <input webkitdirectory>     → 直接选择文件夹
  - webkitGetAsEntry()          → 拖拽时获取 FileSystemEntry
  - entry.createReader()        → 读取目录内容
  - 递归遍历处理嵌套文件夹
```
