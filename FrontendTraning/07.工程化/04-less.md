# 04. Less - CSS预处理器

## 什么是Less？

**Less**是一种CSS预处理器，它让编写样式变得更加简洁和高效。Less非常类似CSS，但提供了更强大的功能，使样式编写变得更容易。

### Less与CSS的对比

Less代码虽然更加优雅，但**无法被浏览器直接识别**，因此需要一个工具将其转换为标准的CSS代码。

由于**Node.js环境具有读写文件的能力**，在Node环境中可以轻松地完成文件的转换。

npm上有一个包叫做`less`，它运行在Node环境中，通过它可以完成对Less代码的转换。

```
Less代码 → less编译器 → CSS代码 → 浏览器识别
```

**重要概念**：
- 转换代码的过程称为**编译(compile)**
- 转换代码的工具称为**编译器(compiler)**

**可以看出，Node环境在前端工程化中，充当了一个辅助的角色，它并不直接运行前端代码，而是让我们编写前端代码更加舒适便利。**

---

## 体验Less

### 1. 安装less编译器

```bash
# 方案一：全局安装（可以在任何目录使用lessc命令，但不利于版本控制）
npm install -g less

# 方案二：本地安装（推荐，便于版本控制）
npm install -D less
```

**方案二的优势**：使用`npx lessc`运行命令，或在package.json中配置脚本

> npx是npm提供的一个工具，它可以运行当前项目中安装到node_modules的cli命令

### 2. 编写Less代码

创建`index.less`文件：

```less
// 定义变量
@green: #008c8c;

.list {
  display: flex;
  flex-wrap: wrap;
  color: @green;
  
  // 嵌套选择器
  li {
    margin: 1em;
    
    // &符号表示父选择器
    &:hover {
      background: @green;
      color: #fff;
    }
  }
}
```

### 3. 编译Less文件

```bash
# 将 index.less 编译成为 index.css
npx lessc index.less index.css

# 或者在package.json中配置脚本
# "scripts": {
#   "build:less": "lessc index.less index.css"
# }
# 然后运行：npm run build:less
```

### 4. 在HTML中引用编译后的CSS

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Less示例</title>
  <!-- 引用编译后的CSS文件 -->
  <link rel="stylesheet" href="./index.css">
</head>
<body>
  <div class="list">
    <li>项目1</li>
    <li>项目2</li>
  </div>
</body>
</html>
```

---

## Less的核心语法

### 1. 变量（Variables）

变量可以存储颜色、尺寸等值，方便复用和统一管理。

```less
// 定义变量
@primary-color: #5488d5;
@border-radius: 5px;
@container-width: 1140px;

// 使用变量
.container {
  width: @container-width;
  border-radius: @border-radius;
  color: @primary-color;
}

.button {
  background: @primary-color;
  border-radius: @border-radius;
}
```

**变量的优势**：
- 统一管理样式值
- 便于主题切换
- 提高代码可维护性

### 2. 嵌套（Nesting）

Less允许在选择器中嵌套其他选择器，使代码结构更清晰。

```less
.container {
  width: 1140px;
  margin: 1em auto;
  
  // 嵌套子选择器
  .search {
    display: flex;
    
    .title {
      background: @primary;
      color: #fff;
    }
    
    .types {
      flex: 1;
      
      .type-list {
        display: flex;
        
        .radio {
          cursor: pointer;
          
          // &符号表示父选择器
          &:hover {
            background: #f0f0f0;
          }
          
          &.checked {
            color: @primary;
          }
        }
      }
    }
  }
}
```

**&符号的使用**：`&`符号代表父选择器的引用，常用于伪类和伪元素

```less
.button {
  &:hover {
    background: darken(@primary, 10%);
  }
  
  &:active {
    transform: scale(0.98);
  }
  
  &::before {
    content: '';
    display: inline-block;
  }
}
```

### 3. 混合（Mixins）

混合可以将一组样式从一个规则集包含到另一个规则集。

#### 基础混合

```less
// 定义混合：圆角
.round(@r: 5px) {
  border-radius: @r;
}

// 定义混合：绝对定位居中
.abs-center(@type: absolute) {
  position: @type;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

// 定义混合：内容居中
.content-center() {
  display: flex;
  justify-content: center;
  align-items: center;
}

// 使用混合
.a {
  .round(); // 使用默认值5px
}

.b {
  .round(10px); // 传入自定义值
}

.c {
  .round(15px);
}

.container {
  position: relative;
  
  .item {
    width: 100px;
    height: 100px;
    .abs-center(fixed); // 使用fixed定位居中
  }
}
```

#### 带参数的混合

```less
// 定义可复用的阴影样式
.box-shadow(@x: 0, @y: 0, @blur: 10px, @color: #000) {
  box-shadow: @x @y @blur @color;
}

.card {
  .box-shadow(2px, 2px, 8px, rgba(0, 0, 0, 0.1));
}
```

### 4. 注释（Comments）

Less支持两种注释方式：

```less
// 这是Less注释，只存在于Less源代码中，编译后会被删除

/* 这是普通的CSS注释，会生成到编译结果中 */

/*
 * 多行注释
 * 也会生成到编译结果中
 */
```

---

## 实际应用示例

### 示例1：英雄列表页面样式

```less
@primary: #5488d5;

// 全局样式重置
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

ul {
  list-style: none;
}

body {
  background: #ecedf2;
}

// 容器样式
.container {
  width: 1140px;
  margin: 1em auto;
}

// 搜索区域
.search {
  margin-bottom: 30px;
  display: flex;
  justify-content: space-between;
  height: 100px;
  line-height: 50px;
  
  .title {
    background: @primary;
    color: #fff;
    width: 80px;
    text-align: center;
    border-radius: 10px 0 0 10px;
  }
  
  .types {
    flex: 1 0 auto;
    color: #666;
    background: #fff;
    font-size: 14px;
  }
}

// 单选按钮样式
.radio {
  margin-right: 30px;
  padding-left: 30px;
  position: relative;
  cursor: pointer;
  
  // 使用伪元素制作单选框
  &::before {
    content: '';
    position: absolute;
    width: 20px;
    height: 20px;
    left: 0;
    top: 50%;
    transform: translateY(-50%);
    border: 1px solid #ccc;
    border-radius: 50%;
  }
  
  &::after {
    content: '';
    position: absolute;
    width: 14px;
    height: 14px;
    left: 4px;
    top: 50%;
    transform: translateY(-50%) scale(0);
    background: @primary;
    border-radius: 50%;
    transition: 0.25s;
  }
  
  // 选中状态
  &.checked::after {
    transform: translateY(-50%) scale(1);
  }
}

// 英雄列表网格布局
.list {
  display: grid;
  grid-template-columns: repeat(10, 1fr);
  justify-items: center;
  text-align: center;
  font-size: 14px;
  row-gap: 20px;
  color: #333;
  
  img {
    width: 77px;
    height: 77px;
    display: block;
    margin-bottom: 5px;
    border: 2px solid @primary;
    border-radius: 10px 0 10px 0;
  }
}
```

---

## Less开发工作流

### 当前的工作流程

```
1. 编写Less代码
2. 运行编译命令：lessc input.less output.css
3. 在HTML中引用编译后的CSS
4. 修改Less后重新编译
```

**问题**：每次修改Less后都需要手动编译，比较麻烦

**解决方案**：使用构建工具（如Webpack、Vite）自动监听Less文件变化并自动编译（后续课程会讲解）

---

## 练习题

### 练习题1：实现居中混合

编写一个Less混合，实现绝对定位居中：

```less
.abs-center(@type: absolute) {
  position: @type;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

// 使用示例
.modal {
  width: 500px;
  height: 300px;
  .abs-center();
}
```

### 练习题2：主题颜色管理

使用Less变量管理一套主题颜色：

```less
// 定义主题变量
@primary-color: #1890ff;
@success-color: #52c41a;
@warning-color: #faad14;
@error-color: #f5222d;
@text-color: #333;
@bg-color: #f0f2f5;

// 应用到组件中
.button-primary {
  background: @primary-color;
  color: #fff;
}

.button-success {
  background: @success-color;
  color: #fff;
}
```

### 练习题3：组件化样式

将你之前做过的某个项目的CSS代码改造为Less格式，使用变量、嵌套和混合来优化代码结构。

---

## 参考资源

- **Less官方文档**：https://lesscss.org/
- **Less中文网**：https://less.bootcss.com/
- **npm包地址**：https://www.npmjs.com/package/less

---

## 总结

### Less的核心优势

1. **变量**：统一管理样式值，便于维护和主题切换
2. **嵌套**：代码结构更清晰，符合HTML层级关系
3. **混合**：样式复用，减少重复代码
4. **计算**：支持数学运算，如`width: 100% - 20px`

### 工程化思维

- Less是前端工程化的基础工具之一
- 编译过程是前端工程化的核心概念
- Node.js为前端开发提供了强大的辅助能力
- 自动化编译是现代前端开发的标准流程

### 下一步

在后续课程中，我们将学习：
- 使用构建工具自动编译Less
- CSS模块化
- PostCSS等更多CSS工程化工具
