# 05. 构建工具的使用

> **工程化，为复杂应用而生**
> 
> 本文介绍如何使用构建工具（Webpack）搭建前端工程

## 学习目标

- 理解构建工具的作用和重要性
- 掌握 Webpack 的基本概念和配置
- 学会使用开发服务器进行开发
- 理解资源模块的处理方式
- 掌握 CSS Module 的使用
- 了解构建工具对代码的优化处理

## 一、构建工具概述

### 1.1 什么是构建工具

**构建工具是用来搭建前端工程的工具**

它运行在 Node.js 环境中，主要做的事情是**打包**。

具体来说，构建工具以某个模块作为入口，根据入口分析出所有模块的依赖关系，然后对各种模块进行合并、压缩，形成最终的打包结果。

**在构建工具的世界中，一切皆是模块**

### 1.2 为什么需要构建工具

构建工具给我们带来的好处：

1. **可以使用任意模块化标准**  
   无须担心兼容性问题，因为构建工具完成打包后，已经没有了任何模块化语句

2. **可以将非 JS 代码也视为模块**  
   可以对 CSS、图片等资源进行更加细粒度的划分

3. **可以在前端开发中使用 npm**  
   无论是自己写的模块，还是通过 npm 安装的模块，构建工具一视同仁，统统视为依赖

4. **非常适合开发单页应用**  
   单页应用是前端用户体验最好的 web 应用，要优雅的实现单页应用，最好依托于前端框架

## 二、Webpack 基础配置

### 2.1 项目结构

```
webpack-proj/
├── public/              # 静态资源目录
│   └── index.html      # 页面模板
├── src/                # 源代码目录
│   ├── main.js         # 入口文件
│   ├── home/           # 模块目录
│   ├── movie/          # 模块目录
│   ├── a.js            # 模块文件
│   └── b.js            # 模块文件
├── dist/               # 打包输出目录（自动生成）
├── webpack.config.js   # Webpack 配置文件
├── babel.config.js     # Babel 配置文件
├── postcss.config.js   # PostCSS 配置文件
└── package.json        # 项目配置文件
```

### 2.2 核心配置文件

#### webpack.config.js

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const CopyPlugin = require('copy-webpack-plugin');

module.exports = {
  // 构建模式：development 开发模式，production 生产模式
  mode: 'development',
  
  // 入口文件：构建的起点
  entry: './src/main.js',
  
  // 输出配置：打包结果的输出位置和命名规则
  output: {
    path: path.resolve(__dirname, './dist'),           // 输出目录
    filename: 'js/app-[contenthash:5].js',            // 主文件命名规则（contenthash：根据内容生成hash）
    assetModuleFilename: 'assets/[hash:5][ext]',      // 资源文件命名规则
    chunkFilename: 'js/chunk-[contenthash:5].js',     // 代码分割文件命名规则
    publicPath: '/',                                  // 公共路径
  },
  
  // 构建目标：web 平台
  target: 'web',
  
  // 源码映射：方便调试
  devtool: 'source-map',
  
  // 开发服务器配置
  devServer: {
    port: 8080,    // 开发服务器端口
  },
  
  // 模块解析配置
  resolve: {
    // 路径别名：@ 表示 src 目录
    alias: {
      '@': path.resolve(__dirname, 'src'),
    },
  },
  
  // 统计信息：只显示错误
  stats: 'errors-only',
  
  // 模块处理规则
  module: {
    rules: [
      // CSS/LESS 文件处理
      {
        test: /\.(css|less)$/i,
        use: [
          MiniCssExtractPlugin.loader,  // 提取 CSS 到单独文件
          'css-loader',                  // 处理 CSS 中的 import 和 url()
          'postcss-loader',              // CSS 后处理（添加厂商前缀等）
          'less-loader',                 // 将 LESS 转换为 CSS
        ],
      },
      // 音视频文件处理
      {
        test: /\.(mp3|mp4)$/,
        type: 'asset/resource',  // 输出为单独文件
      },
      // 图片文件处理
      {
        test: /\.(gif|png|webp|svg|jpg|jpeg|bmp)$/,
        type: 'asset',  // 根据大小自动选择 data URI 或单独文件
        parser: {
          dataUrlCondition: {
            maxSize: 1024,  // 小于 1KB 使用 data URI
          },
        },
      },
      // JS 文件处理（Babel 转换）
      {
        test: /\.js$/,
        exclude: /node_modules/,  // 排除 node_modules
        use: 'babel-loader',
      },
    ],
  },
  
  // 插件配置
  plugins: [
    // HTML 页面生成插件
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, 'public/index.html'),
    }),
    // CSS 提取插件
    new MiniCssExtractPlugin({
      filename: 'css/[name]-[contenthash:5].css',
    }),
    // 清理输出目录插件
    new CleanWebpackPlugin(),
    // 文件复制插件
    new CopyPlugin({
      patterns: [
        {
          from: path.resolve(__dirname, 'public'),
          to: './',
          globOptions: {
            ignore: ['**/*.html'],  // 排除 HTML 文件
          },
        },
      ],
    }),
  ],
};
```

#### babel.config.js

```javascript
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        useBuiltIns: 'usage',  // 按需引入 polyfill
        corejs: 3,             // 使用 core-js 3
      },
    ],
  ],
};
```

#### postcss.config.js

```javascript
module.exports = () => ({
  plugins: [
    'cssnano',        // CSS 压缩
    'autoprefixer',  // 自动添加厂商前缀
  ],
});
```

#### package.json

```json
{
  "name": "movie-list",
  "version": "1.0.0",
  "scripts": {
    "serve": "webpack serve",      // 启动开发服务器
    "build": "webpack --mode=production"  // 生产环境打包
  },
  "devDependencies": {
    "@babel/core": "^7.14.0",
    "@babel/preset-env": "^7.14.1",
    "autoprefixer": "^10.2.5",
    "babel-loader": "^8.2.2",
    "clean-webpack-plugin": "*",
    "copy-webpack-plugin": "^8.1.1",
    "core-js": "^3.12.0",
    "css-loader": "^5.2.4",
    "cssnano": "^5.0.2",
    "html-webpack-plugin": "^5.3.1",
    "less": "^4.1.1",
    "less-loader": "^8.1.1",
    "mini-css-extract-plugin": "^1.6.0",
    "postcss": "^8.2.14",
    "postcss-loader": "^5.2.0",
    "webpack": "^5.36.2",
    "webpack-cli": "^4.7.0",
    "webpack-dev-server": "^3.11.2"
  },
  "dependencies": {
    "jquery": "^3.6.0"
  }
}
```

## 三、核心概念详解

### 3.1 入口文件

入口文件是构建工具开始分析的起点，通常配置为 `src/main.js`：

```javascript
// src/main.js
import $ from 'jquery';
import home from './home';

$('<h1>').text('hello webpack').appendTo(document.body);
console.log(home);
```

### 3.2 页面模板

对于单页应用，webpack 会自动生成一个页面，参考 `public/index.html` 作为模板：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

webpack 会自动在生成的 HTML 中添加对 JS 和 CSS 的引用。

### 3.3 Public 目录

webpack 会将 `public` 目录中的所有文件（除页面模板外）复制到打包结果中。

### 3.4 开发服务器

在开发阶段，使用 `npm run serve` 命令启动开发服务器：

```bash
npm run serve
```

**开发服务器的特点：**

1. 不会生成物理文件，打包结果放在内存中
2. 源码变动时自动重新打包
3. 自动刷新页面以显示最新的打包结果
4. 提供更好的开发体验

### 3.5 文件缓存与 Hash

打包后的资源文件名会包含 hash 值，如 `js/app-9ea93.js`。

**Hash 的作用：**

- 源码内容不变，hash 不变
- 源码内容变化，hash 变化
- 解决浏览器缓存问题，确保用户获取最新代码

**Hash 类型：**

- `contenthash`：根据文件内容生成
- `hash`：根据整个构建生成
- `chunkhash`：根据代码块生成

## 四、资源模块处理

### 4.1 资源路径转换

**在 CSS 中使用资源路径：**

```css
.container {
  /* 打包前：源码路径 */
  background: url('../assets/1.png');
}

/* 打包后：自动转换为真实路径 */
.container {
  background: url(/img/1492ea.png);
}
```

webpack 会自动识别 CSS 中的路径并转换。

**在 JS 中动态使用资源路径：**

```javascript
// ❌ 错误：webpack 无法识别字符串中的路径
const url = './assets/1.png';
img.src = url;

// ✅ 正确：通过模块化导入
import url from './assets/1.png';
img.src = url;  // url 得到的是真实路径：/img/1492ea.png
```

### 4.2 图片资源处理

**小图片处理（< 1KB）：**

```javascript
// 小图片会被转换为 base64 data URI
import smallIcon from './small.png';
// smallIcon 的值类似：data:image/png;base64,iVBORw0KGgo...
```

**大图片处理（>= 1KB）：**

```javascript
// 大图片会被输出为单独文件
import bigImage from './big.jpg';
// bigImage 的值类似：/assets/abc23.jpg
```

### 4.3 音视频处理

```javascript
import videoUrl from './video.mp4';
import audioUrl from './audio.mp3';

video.src = videoUrl;  // /assets/video123.mp4
audio.src = audioUrl;  // /assets/audio456.mp3
```

## 五、代码优化

### 5.1 缺省文件和后缀名

导入模块时的简化规则：

```javascript
// 可以省略 .js 后缀
import './home';           // 实际导入：./home.js
import './movie';          // 实际导入：./movie/index.js

// 导入目录时默认导入 index.js
import './movie/a/b';      // 实际导入：./movie/a/b/index.js
```

### 5.2 路径别名

使用 `@` 别名简化路径：

```javascript
// 普通导入（层级很深时很麻烦）
import '../../../b/b1/index.js';

// 使用别名（简化导入）
import '@/b/b1';  // @ 表示 src 目录
```

别名配置在 `webpack.config.js` 中：

```javascript
resolve: {
  alias: {
    '@': path.resolve(__dirname, 'src'),
  },
}
```

### 5.3 JS 兼容性处理

webpack 通过 Babel 自动对 JS 代码进行兼容性处理：

```javascript
// 源码：现代 ES6+ 语法
const test = () => {
  console.log('hello');
  const arr = [1, 2, 3];
  return arr.map(x => x * 2);
};

// 打包后：转换为 ES5 语法
var test = function() {
  console.log('hello');
  var arr = [1, 2, 3];
  return arr.map(function(x) {
    return x * 2;
  });
};
```

**配置文件：**

- `babel.config.js`：配置 JS 降级规则
- `.browserslistrc`：配置要兼容的浏览器范围

### 5.4 打包压缩

生产环境打包时，webpack 会自动：

1. **压缩代码**：去除空格、注释
2. **混淆名称**：变量名、函数名缩短
3. **优化结构**：代码结构优化

```javascript
// 源码
function calculateSum(numbers) {
  return numbers.reduce((sum, num) => sum + num, 0);
}

// 打包后
function a(b){return b.reduce((c,d)=>c+d,0)}
```

### 5.5 源码映射

打包后的代码难以阅读，调试困难。**源码映射** 解决了这个问题：

- 每个打包后的 JS 文件都有对应的 `.map` 文件
- `.map` 文件保存了源码和打包代码的映射关系
- 浏览器报错时会显示源码位置，而非打包代码位置

## 六、CSS 工程化

### 6.1 样式文件支持

webpack 支持所有样式预处理器：

- CSS
- LESS
- SASS/SCSS
- Stylus

打包时会统一转换为纯 CSS。

### 6.2 自动厂商前缀

webpack 根据浏览器兼容性配置，自动添加 CSS 厂商前缀：

```css
/* 源码 */
.container {
  display: flex;
  transition: 1s;
}

/* 打包后：自动添加前缀 */
.container {
  display: -webkit-box;
  display: -webkit-flex;
  display: flex;
  -webkit-transition: 1s;
  transition: 1s;
}
```

### 6.3 CSS Module

**问题：** 样式文件多了后，如何避免类名冲突？

**解决方案：** CSS Module

**开启方式：** 文件名使用 `.module.` 格式

- `index.module.less`
- `movie.module.css`

**工作原理：** 文件中的所有类名会被 hash 化

```less
// 源码：index.module.less
.container {}
.list {}
.item {}

// 打包结果：类名被 hash 化（绝无重名可能）
._2GFVidHvoHtfgtrdifua24 {}
._1fsrc5VinfYHBXCF1s58qS {}
.urPUKUukdS_UTSuWRI5-5 {}
```

**使用方式：**

```javascript
// ❌ 错误：直接使用类名
import './index.module.less';
dom.classList.add('container');  // 最终的类名不是 container

// ✅ 正确：通过导入的对象获取类名
import styles from './index.module.less';
dom.classList.add(styles.container);  // styles.container 对应转换后的类名
```

**CSS Module 示例：**

```javascript
// src/main.js
import styles1 from './b.module.less';
import styles2 from './c.module.less';
import $ from 'jquery';

// 使用不同模块中的同名类
$('<h1>')
  .text('b.module.less样式中的类样式a')
  .addClass(styles1.a)   // b.module.less 中的 a 类
  .appendTo(document.body);

$('<h1>')
  .text('c.module.less样式中的类样式a')
  .addClass(styles2.a)   // c.module.less 中的 a 类（不会冲突）
  .appendTo(document.body);
```

## 七、构建流程

### 7.1 插件和加载器

webpack 本身并不做所有事情，它通过**插件**和**加载器**整合各种技术：

- **Loader**：处理特定类型的文件（如 babel-loader 处理 JS）
- **Plugin**：执行更广泛的任务（如压缩、优化）

**常见加载器：**

- `babel-loader`：JS 转换
- `css-loader`：CSS 处理
- `less-loader`：LESS 转换
- `postcss-loader`：CSS 后处理

**常见插件：**

- `HtmlWebpackPlugin`：生成 HTML
- `MiniCssExtractPlugin`：提取 CSS
- `CleanWebpackPlugin`：清理输出目录
- `CopyPlugin`：复制静态资源

### 7.2 配置文件说明

- **`.browserslistrc`**：指定浏览器兼容范围
- **`babel.config.js`**：Babel 配置，JS 降级处理
- **`postcss.config.js`**：PostCSS 配置，CSS 转换
- **`webpack.config.js`**：Webpack 主配置，整合所有技术

## 八、开发实践

### 8.1 开发流程

1. **安装依赖**

```bash
npm install
```

2. **启动开发服务器**

```bash
npm run serve
```

3. **编写代码**

在 `src` 目录中编写模块代码

4. **查看效果**

访问 `http://localhost:8080` 查看效果

### 8.2 生产打包

```bash
npm run build
```

打包结果输出到 `dist` 目录。

### 8.3 开发技巧

**1. 动态获取资源路径**

```javascript
import url from './assets/1.png';
img.src = url;
```

**2. 省略文件和后缀名**

```javascript
import './home';     // ./home.js
import './movie';    // ./movie/index.js
```

**3. 使用别名简化导入**

```javascript
import '@/b/b1';  // 实际：src/b/b1/index.js
```

**4. 使用 CSS Module**

```javascript
import styles from './index.module.less';
dom.classList.add(styles.container);
```

## 九、总结

构建工具是现代前端开发的基础设施，它：

1. **提升开发效率**：自动处理模块化、转换、优化
2. **优化代码质量**：压缩、混淆、兼容性处理
3. **改善用户体验**：开发服务器、自动刷新、缓存控制
4. **支持复杂应用**：单页应用、代码分割、资源优化

**关键要点：**

- 理解构建工具的作用和流程
- 掌握基本配置的使用
- 学会资源模块的处理方式
- 了解 CSS Module 的使用
- 理解开发服务器和生产打包的区别

**下一步学习：**

- 深入学习 Webpack 高级配置
- 了解其他构建工具（Vite、Rollup）
- 学习性能优化策略
- 掌握 CI/CD 集成
